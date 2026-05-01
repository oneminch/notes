- Traditional [[Bundling|build tools]] like Webpack bundle entire applications before serving them in development. For a modest application with 1000 modules:

```
Start dev server -> Bundle all modules (30-60+ secs) -> Parse & transform -> Serve
```

- This creates 2 problems:
	- **Cold start time**: Linear relationship between codebase size and startup time
	- **Update speed**: [[HMR|Hot Module Replacement]] (HMR) degrades as applications grow, because [[Bundling|bundlers]] must reconstruct module graphs and rebundle.
- Vite solves these problems by exploiting native [[JS Module System|ES modules]] in browsers.

### Architecture

![Vite's Architecture Pipeline|1280](assets/images/vite.architecture-pipeline.svg)

- Vite operates on two distinct modes with different architectures: development and production.
- This dual architecture exists because:
    - Development prioritizes **speed** (unbundled, instant transformation)
    - Production prioritizes **optimization** (bundled, tree-shaking, code-splitting)
- Vite optimizes for each context separately.

#### Development Mode

- Uses **unbundled ES modules** served on-demand.
- `<script type="module">` tells the browser to use ESM. 
- The browser sends a request to the Vite dev server when it encounters an import statement, which is then transformed by Vite on the fly and returned. 
    - The browser does the dependency graph traversal. Vite just transforms individual files on demand.

- **Key components:**
	- **Dependency pre-bundling**: All bare imports discovered (`lodash-es`, etc.) and pre-bundled into `node_modules/.vite/deps`.
    	- [[JS Module System#CommonJS Modules|CommonJS]] / UMD dependencies (*bare imports*) converted to ESM, multiple files combined into single modules to reduce HTTP requests
    	- Browsers need absolute or relative URLs. They can't work with bare imports.
	- **Source transformation**: JSX/TSX/Vue transformed on-demand as browser requests files
	- **Dev server** (Connect): Middleware-based HTTP server handling [[Module Resolution]], transformation, and HMR

- **What happens during a `vite dev`**?
    - Vite reads `vite.config.ts` and resolves all plugins. 
        - Vite calls each plugin's `config` hook first (to let plugins mutate config).
        - `configResolved` hooks fire once before server / build starts to read the final merged config.
        - `configureServer` is called to set up the HTTP server.
    - Source files are scanned to discover all bare imports (`axios`, etc.) and are pre-bundled into `.vite/deps/`.
    - The dev server starts (a Connect middleware stack). 
        - A WebSocket server starts alongside it.
    - Browser loads `index.html`. 
        - Vite's HTML transform injects `/@vite/client` — the HMR runtime.
    - Browser sees `<script type="module" src="/src/main.ts">`, and fires `GET /src/main.ts`.
    - Vite runs the plugin pipeline on `main.ts`: 
        - `resolveId` (absolute path), `load` (reads disk), `transform` (Oxc strips TypeScript). 
        - It also rewrites `import.meta.env` and `import.meta.hot`.
    - The transformed JS lands in the browser. 
        - The browser parses imports and fires more GETs: `/src/utils.ts`, `/node_modules/.vite/deps/axios.js`, and virtual modules if any.
    - Each of those goes through the same pipeline, and results are cached in the module graph.
    - When `utils.ts` is edited and saved: 
        - **(↓)** A file-change event is fired.
        - **(↓)** Vite invalidates `utils.ts` and `main.ts` (its importer) in the module graph.
        - **(↓)** Sends `{ type: 'update', updates: [{ path: '/src/utils.ts', ... }] }` over WebSocket.
        - **(↓)** The HMR client in the browser re-imports `/src/utils.ts?t=<timestamp>` (cache-busting query parameter).
        - Done.
- **What happens during a `vite build`**?
    - Vite does a production compile. 
        - Starting from `<root>/index.html`, Vite resolves the module graph, bundles everything for deployment, and writes optimized output to `dist/` by default.
    - Vite reads config and build options.
    - It uses `index.html` as the entry point unless overridden.
    - It walks all imports to build the full dependency graph.
    - It bundles modules for production instead of serving them separately.
    - It applies production optimizations such as minification and asset handling.
    - It empties `outDir` first if it is inside the project root, unless configured otherwise.
    - It writes the final files to disk, usually `dist/`, which can be deployed to a host or CDN. 
    - `vite build --watch` rebuilds when source files change, but config changes still require restarting the build.

> [!example] Dependency Resolution Example:
> ```javascript
> // Browser requests: /src/App.jsx
> import React from 'react'  // → /node_modules/.vite/deps/react.js (pre-bundled)
> import './App.css'         // → /src/App.css?direct (transformed)
> import Logo from './logo.svg' // → /src/logo.svg (asset)
> ```

> [!note]
> There are situations where certain dependencies will not be auto-detected or pre-bundled during the initial scan. For instance, linked dependencies (packages linked from the same monorepo) are treated as source code, and Vite will not attempt to bundle them.
> 
> `optimizeDeps.include` config can be used to force pre-bundling for specific dependencies.
> 
> ```ts
> export default defineConfig({
>     optimizeDeps: {
>         include: ['linked-dep'],
>     },
> })
> ```

- **The Module Graph** 
    - Vite builds an internal graph to track dependencies between files and cache their transformation results.
    - Every time a file is transformed, Vite records: which file this is, its imports, what imports it, and the cached transform result. 
        - When a file changes on disk, Vite walks the module graph upward to find every importer that depends on it — that's the HMR boundary.
        - The browser fetches a single transformed module. Only affected modules are invalidated and re-fetched by the browser via WebSocket notification.

#### Production Mode

- Uses **Rollup** for bundling:

```
Source files → Rollup (tree-shaking, code-splitting) → Optimized bundles
```

### Plugin System

- Vite plugins extend **Rollup's plugin interface** with Vite-specific hooks:

```ts
// Plugin structure
{
    name: 'plugin-name',
    
    // Rollup hooks (work in both dev & build)
    // (1) Module Resolution
    //     Maps specifier (e.g. axios) to absolute file path 
    //     (or virtual module id) (e.g. /node_modules/.vite/deps/axios.js)
    resolveId(id) { },
    
    // (2) Load Module Content
    //     Read source code from disk or generate it.
    load(id) { },
    
    // (3) Transform Module
    //     Mutate source code. 
    //     All plugins run in order, each receives the previous output.
    transform(code, id) { }, 
    // OR
    transform: {
        filter: {
            id: /FILTER_RE$/
        },
        handler(code) { }
    }, 
    
    // Vite-specific hooks
    configureServer(server) { },  // Extend dev server
    transformIndexHtml(html) { }, // Process HTML
    handleHotUpdate(ctx) { }      // Custom HMR handling
}
```

```markdown
<!-- Execution order during development: -->
Request arrives
    ↓
1. resolveId → Determine module path
    ↓
2. load → Get source content (file system, virtual, network)
    ↓
3. transform → Apply transformations (multiple plugins chain)
    ↓
Response sent
```

- **Plugin Pipeline**:
    - `resolveId(id)` - [[Module Resolution]]
        - *Intercepts the lookup* of module specifier.
        - Maps specifier to absolute file path (or virtual module ID) (e.g. `axios` -> `/node_modules/.vite/deps/axios.js`)
    - `load(resolvedId)` - Load Module Content
        - *Intercepts the read* of source code from disk or generate it.
        - Read operation is usually delegated to the filesystem, but plugins can intercept and return generated code.
    - `transform(code, id)` - Transform Module
        - Mutate source code. 
        - All plugins run in order, each receives the previous output.

- **Plugin Ordering**
    - The resolved plugins run in this order: 
        - `alias` -> user plugins with `enforce: 'pre'` → Vite core plugins → user plugins without `enforce` → Vite feature plugins (JSON, web workers, etc.) → user plugins with `enforce: 'post'` → Vite post-build plugins.

    - User plugins execute in array order, with transformations chaining:

```javascript
// Each plugin handles specific concern
export default {
    plugins: [
        vue(),           // .vue file transformation
        viteReact(),     // JSX + Fast Refresh
        compression(),   // Gzip assets
        legacy()         // Polyfills for old browsers
    ]
}
```

> [!tip]
> - `resolveId` is useful:
>     - when the module id doesn't correspond to a real file on disk. e.g. virtual modules (`virtual:...`).
>     - when a specifier needs to be redirected to a different path. e.g. aliases.
>     - to conditionally externalize a module (by returning `{ id, external: true }`). 
> - `load` uses module ID produced by `resolveId` to return the module's source code, or `null` to let Rolldown fall back to reading from disk.
>     - It's useful for:
>         - virtual modules where there is no content on disk.
>         - custom binary / non-text loaders.
>         - injecting code at load time before `transform`.
> - `configResolved` hook is used for plugins that need to branch on config values (e.g. different behavior in `serve` vs `build`). 

#### Examples

##### Build Info (Virtual Module)

```ts
// main.ts
import { BUILD_INFO } from 'virtual:build-info';

const app = document.querySelector('#app');
app.textContent = `v${BUILD_INFO.version} · ${BUILD_INFO.commit} · built ${BUILD_INFO.date}`;

document.body.appendChild(app);
```

```ts
// plugins/vite-plugin-build-info.ts
import { execSync } from 'node:child_process'
import type { Plugin } from 'vite'

interface BuildInfo {
    version: string
    commit: string
    date: string
    mode: string
}

const VIRTUAL_ID = 'virtual:build-info'
const RESOLVED_ID = '\0virtual:build-info'

export default function buildInfo(): Plugin {
    let info: BuildInfo

    return {
        name: 'vite-plugin-build-info',

        // (1) Config phase — runs once, before server / build starts
        configResolved(config) {
            const commit = (() => {
                try {
                    return execSync('git rev-parse --short HEAD').toString().trim()
                } catch {
                    return 'no-git'
                }
            })()

            info = {
                version:  process.env.npm_package_version ?? '0.0.0',
                commit,
                date:     new Date().toISOString(),
                mode:     config.command,  // 'serve' | 'build'
            }
        },

        // (2) Intercept the bare specifier 'virtual:build-info'
        resolveId(id) {
            if (id === VIRTUAL_ID) return RESOLVED_ID
        },

        // (3) Return the module source for the resolved id
        load(id) {
            if (id !== RESOLVED_ID) return
            
            return `export const BUILD_INFO = ${JSON.stringify(info)}`
        },

        // (4) Stamp the HTML too (useful for CSP / cache-busting)
        // Runs on every HTML request
        // Injects <meta name="build-commit"> tag
        transformIndexHtml() {
            return [
                {
                    tag: 'meta',
                    attrs: { name: 'build-commit', content: info.commit },
                    injectTo: 'head',
                },
            ]
        },
    }
}
```

```ts
// plugins/vite-plugin-build-info.d.ts
declare module 'virtual:build-info' {
    export const BUILD_INFO: {
        version: string
        commit: string
        date: string
        mode: string
    }
}
```

##### Markdown to HTML

```ts
// src/main.ts
import aboutHtml, { raw } from '../about.md'

document.getElementById('content')!.innerHTML = readmeHtml
```

```ts
// vite.config.ts
import markdown from './plugins/vite-plugin-markdown'

export default defineConfig({
    plugins: [markdown()],
})

// plugins/vite-plugin-markdown.ts
import { marked } from 'marked'
import type { Plugin } from 'vite'

export default function markdown(): Plugin {
    return {
        name: 'vite-plugin-markdown',

        // HMR works automatically. Vite's file watcher
        // tracks every file that passed through `transform`.
        transform(code, id) {
            if (!id.endsWith('.md')) return null

            const html = marked.parse(code) as string

            return {
                code: `
                    export const html = ${JSON.stringify(html)};
                    export const raw = ${JSON.stringify(code)};
                    export default html;
                `,
                map: null,
            }
        },
    }
}
```

```ts
// src/env.d.ts
declare module '*.md' {
    const html: string
    export const raw: string
    export default html
}
```

### Asset Handling

- Vite treats assets differently based on type and import method:

#### Static Assets

```javascript
// URL import - returns public path
import imgUrl from './image.png'
// → '/assets/image.a1b2c3d4.png'

// Explicit raw import
import shaderCode from './shader.glsl?raw'
// → string content

// Worker import
import Worker from './worker.js?worker'
// → Worker constructor
```

- **Processing pipeline:**

```
Asset imported
    ↓
Size check: < 4KB?
    ├─ Yes → Inline as base64 data URL
    └─ No  → Copy to dist/ with content hash
    ↓
Return reference path
```

#### CSS Handling

```javascript
// Standard import - injects <style> in dev, extracts in build
import './style.css'

// CSS Modules
import styles from './component.module.css'
// → { className: 'component_className_a1b2c3' }

// Direct injection
import './critical.css?inline'
// → Always injects as <style>, never extracted
```

```markdown
<!-- Development: -->
style.css → Transform → Inject via JS → `<style>` tag

<!-- Production: -->
style.css → PostCSS → Code split → Extract → separate .css file
```

#### JSON and Other Imports

```javascript
// Entire object
import data from './data.json'

// Named imports (tree-shakeable)
import { field } from './data.json'

// WebAssembly
import init from './module.wasm'
```

## Environment API

- The Environment API addresses a fundamental limitation: 
    - Vite previously assumed single build target per mode.

### The Problem

- Modern applications often need multiple build outputs simultaneously:

```
Single application
    ├─ Client code (browser)
    ├─ SSR code (Node.js)
    ├─ Edge workers (Cloudflare Workers)
    └─ React Server Components (different runtime)
```

- Previously, each needed separate Vite instances or build passes.
- The Environment API became necessary because:
    - **Framework evolution**: RSCs, Remix, Solid Start need different build targets
    - **Edge computing**: Cloudflare Workers, Deno Deploy have different [[Module Resolution]] than Node.js
    - **Complexity reduction**: Previously required multiple Vite instances or complex plugin coordination

### Architecture

- The Environment API introduces **environment-specific module graphs**:

```javascript
// vite.config.js
export default {
    environments: {
        client: {
            // Browser environment
            resolve: {
                conditions: ['browser']
            }
        },
        ssr: {
            // Node.js environment  
            resolve: {
                conditions: ['node']
            },
            build: {
                outDir: 'dist/server'
            }
        },
        rsc: {
            // React Server Components
            resolve: {
                conditions: ['react-server']
            }
        }
    }
}
```

## Optimization Techniques

- **Dependency Pre-bundling**
    - **Problem:** `node_modules` contain:
        - CommonJS modules (need conversion)
        - Hundreds of internal imports (HTTP overhead)
    - **Solution:**

```
esbuild scans imports
    ↓
Bundle each package → Single ESM file
    ↓
Cache in node_modules/.vite/deps/
    ↓
Reuse until package.json or config changes
```

```javascript
/* --- Example --- */
// lodash has 100+ internal files

// Before pre-bundling:
import { debounce } from 'lodash-es'
// → 100+ HTTP requests for lodash internals

// After pre-bundling:
import { debounce } from '/node_modules/.vite/deps/lodash-es.js'
// → Single request, single file
```

- **Code Splitting**
    - Vite uses Rollup's automatic code splitting:

```javascript
// Dynamic imports create split points
const AdminPanel = () => import('./AdminPanel.jsx')

// Builds to:
// dist/AdminPanel.a1b2c3.js (loaded on demand)
// dist/index.b2c3d4.js (main bundle)
```

```javascript
/* --- Shared Dependency Handling --- */

// Both routes import React
const Home = () => import('./Home.jsx')
const About = () => import('./About.jsx')

// Results in:
// react.js (shared chunk)
// Home.js (specific code)  
// About.js (specific code)
```

- **CSS Code Splitting**

```javascript
// Component-level CSS
import './Button.css'

// Production build:
Button.css → Analyzed for used selectors → Extracted to Button.a1b2c3.css
// Loaded only when Button.js loads
```


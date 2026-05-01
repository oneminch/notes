---
aliases:
    - Nuxt.js
---

## Routing

- Dynamic Routes - e.g. `~/pages/users/[id].vue` -> `/users/123`
	- Optional Parameters - e.g. `~/pages/[[slug]]/index.vue` -> `/`, `/test`, etc.
- Catch-all Route - e.g. `~/pages/blog/[...slug].vue`

- To display a nested route, `<NuxtPage>` must be added to the parent page.

- Routes can be grouped under a folder wrapped in parenthesis without affecting file-based routing.

```
-| pages/
---| index.vue
---| (marketing)/
-----| about.vue
-----| contact.vue
```


> [!note]
> Pages must have a single root element for route transitions between pages to work. That includes [[HTML]] comments as well.

## Plugins

- **Runtime extensions**
	- Run when the application starts (client/server).
- Executed during app bootstrap.
- Can access Vue app instance.
- Can be client-only (`.client.ts`) or server-only (`.server.ts`).
- Auto-imported from `app/plugins/`.
- **Purpose:**
	- Add Vue plugins or directives
	- Provide runtime helpers to components
	- Initialize client/server libraries
	- Run code on app initialization

```ts
// app/plugins/hello.ts
export default defineNuxtPlugin(() => {
	return {
        provide: {
            hello: (msg: string) => `Hello ${msg}!`
        }
    }
})
```

## Modules

- **Build-time extensions**
	- Run during development/build process.
- Executed before app runs (during `nuxt dev` or `nuxt build`)
- Can modify Nuxt configuration
- Can inject plugins, components, composables.
- Auto-imported from `modules/` or specified in `nuxt.config.ts`
- **Purpose:**
	- Add server routes, middleware, or components
	- Extend Nuxt configuration
	- Register templates or layouts
	- Hook into build process
	- Integrate third-party services

```ts
// modules/hello/index.ts
import { defineNuxtModule, addServerHandler } from 'nuxt/kit'

export default defineNuxtModule({
    meta: { name: 'hello' },
    setup(options, nuxt) {
        addServerHandler({
            route: '/api/hello',
            handler: resolver.resolve('./runtime/api-route')
        })
    }
})
```

- Modules have access to two surfaces for injecting things into the app:
    - **Build-time surface** (`module.ts` via `@nuxt/kit` helpers in `setup()`):
        - The `setup()` function of a module runs at build time and manipulates the `nuxt` object.
        - `addPlugin()` — registers a Vue plugin file to be executed at runtime
        - `addComponent()` — registers a component for auto-import
        - `addComposable()` / `addImports()` — registers composables for auto-import
        - `addTemplate()` — writes a virtual file to the `#build/` virtual filesystem
        - `addServerHandler()` — registers a Nitro API route
        - `extendPages()` — modify or add to the route list
        - `extendNitroConfig()` — merge configuration into Nitro before it initializes
        - `addTypeTemplate()` — generates `.d.ts` augmentation files
    - **Runtime surface** (`src/runtime/`):
        - Modules aren't included in the application runtime, but the runtime directory enables providing Vue components, composables, Nuxt plugins, API routes, Nitro middlewares, Nitro plugins, stylesheets, and other assets that get injected into the user's app.
        - The files in `src/runtime/` are injected _into the built app_ and run at runtime.
            - They cannot reference anything from `setup()`.

## Commands

- `nuxt build`
	- Creates a `.output` directory with all the application, server and dependencies ready for production.
	- Creates an entry point that launches a ready-to-run [[Node]] server.
		- `node .output/server/index.mjs`

- `nuxt generate`
	- Builds and pre-renders an application and stores the result in plain HTML files.
	- Uses the `Nitro` crawler.
	- Similar to `nuxt build` with the `nitro.static` option set to `true`, or running `nuxt build --prerender`.
	- The resulting `.output/public` directory can be deployed to any static hosting service.
	- By default, pages that are not linked to a discoverable page can't be pre-rendered automatically.
	- Outputs SPA mode when `ssr` is set to `false`.

- `nuxt prepare`
	- Creates a `.nuxt` directory and generates types.

- `nuxt preview`
	- Starts a server to preview a Nuxt application after `nuxt build`.

- `nuxt add <ENTITY> <NAME>`
	- Scaffold an entity into a Nuxt app.
	- **`ENTITY`** - `api`, `app`, `app-config`, `component`, `composable`, `error`, `layer`, `layout`, `middleware`, `module`, `page`, `plugin`, `server-middleware`, `server-plugin`, `server-route`, `server-util`

- `nuxt build-module`
	- Used to build a Nuxt module before publishing.
	- Runs `@nuxt/module-builder` to generate `dist/` directory that contains the full build for the Nuxt module.
		- `--build` - Build module for distribution.
		- `--stub` - Stub `dist/` instead of actually building it for development.
		- `--prepare` - Prepare module for local development.

## Internals

### Contexts

- Nuxt operates with two distinct contexts that serve different purposes:

#### Build Context 

- The `nuxt` Interface + `hookable`
    - [[Node]]
- When `nuxt dev` or `nuxt build` commands are run, a shared `nuxt` context is created. 
    - This object holds normalized options merged with `nuxt.config`, some internal state, and a powerful hooking system (powered by `hookable`) allowing different components to communicate with each other.
- This context is globally available via `@nuxt/kit` composables, and only one instance of Nuxt is allowed to run per process.
    - Everything that customizes Nuxt mutates or reads this object.
- This is where modules are installed and run, configuration is read, and templates are generated. 
- It also emits the `.nuxt/` virtual files.
- [Modules](#modules) are used to extend the Nuxt interface and hook into different stages of the build process.
- Build-time hooks fire once during `dev` / `build`.

```
modules:before          → before any module
--- [modules run] ---
modules:done            → all modules have run
app:resolve             → app instance resolved
app:templates           → virtual files (.nuxt/) generated
app:templatesGenerated  → vfs compiled
build:before            → bundler (Vite) about to start
--- [Vite builds] ---
build:done              → bundler finished
close                   → Nuxt is shutting down
```

> [!note]
> When building for production, `nuxt build` generates a standalone build in `.output` that is entirely independent of `nuxt.config` and Nuxt modules, and with no `node_modules`. This is what gets used by the runtime.

#### Runtime Context

- The `nuxtApp` Interface + `hookable`
    - Browser & Server
- When rendering a page in the browser or on the server, a shared context is created called `nuxtApp`. 
    - This context keeps the Vue instance, runtime hooks, and internal states like `ssrContext` and `payload` for hydration.
        - Plugins run and Vue app lives in this context.
        - Payload (`nuxtApp.payload`) is serialized: server -> client. 
            - This object is how SSR data crosses the network boundary.
    - It manages the Vue application, plugin execution, and request handling.
- This context can be accessed using the `useNuxtApp()` composable within Nuxt plugins, `<script setup>` and Vue composables.
- Global usage is possible in the browser but **not on the server**, to avoid sharing context between users.
- [Plugins](#plugins) are used to extend the `nuxtApp` interface and hook into different stages or access contexts.
- Runtime hooks fire per request (server) / per navigation.
- The `nuxtApp` object gives you access to:
    - **`vueApp`** — the global Vue application instance
    - **`versions`** — Nuxt and Vue version info
    - **`hooks` / `hook` / `callHook`** — runtime hook system
    - **`ssrContext`** — server-only: `url`, `req`, `res`, `runtimeConfig`, `noSSR`
    - **`payload`** — data stringified and passed from server to client for hydration
    - **`provide`** — inject values into the app

> [!note]+ Build Context vs. Runtime Context
> - Nuxt builds and bundles the project using [[Node]] but also has a runtime side. 
> 
> - While both areas can be extended, the runtime context is **isolated** from build-time — they are not supposed to share state, code, or context other than runtime configuration (`runtimeConfig`).
> 
> |                  | Build Context                | Runtime Context                 |
> | ---------------- | ---------------------------- | ------------------------------- |
> | **Extended via** | `nuxt.config` + Nuxt Modules | Nuxt Plugins                    |
> | **When active**  | `nuxt dev` / `nuxt build`    | Page rendering (server/browser) |
> 
> - The *hooking system* is central to everything; it's how all of Nuxt's internal pipeline is wired together.
>     - Both the build context and the runtime context have their own independent `hookable` instances.
>     - It lets modules and plugins tap into lifecycle events at both build and runtime.
> - Modules extend the **builder** (what gets generated), plugins extend the **runtime** (how the app behaves).

### Server Engine

- Powered by Nitro.
- Knows nothing about `nuxt.config` at runtime.
- Has its own plugin / hook system & internally uses `h3`.

- During the build lifecycle, Nuxt modules can hook into `nitro:config` to modify Nitro's configuration before Nitro initializes (adding server plugins, virtual imports, storage drivers, or route rules). 
    - After that point, Nitro operates independently.
- Nitro produces a standalone server `dist` that is independent of `node_modules`. 
    - The output contains runtime code to run the Nuxt server in any environment, and serves static files.

```ts
// server/plugins/my-nitro-plugin.ts
export default defineNitroPlugin((nitroApp) => {
    // Modify raw HTML before sending to client
    nitroApp.hooks.hook('render:html', (html, { event }) => {
        html.head.push('<meta name="generator" content="my-module">')
    })
})
```

### Auto-Imports

- Under the hood, Nuxt uses `unjs/unimport` to:
    - **Scan directories** (`components/`, `composables/`, `utils/`, and server equivalents) at build time for exported identifiers.
    - **Build an import map**: a registry of identifier → file path.
    - **Generate a virtual module** (`#imports`) that re-exports everything.
    - **Transform source files** via a Vite/Webpack plugin that rewrites bare usage of known identifiers to explicit `import` statements before bundling.

### Virtual File System (VFS)

- When modules call `addTemplate()`, they're registering files that:
    - Don't exist on disk during development (they live in memory in Vite's dev server)
    - Are written to `.nuxt/` during builds
    - Are importable via the `#build/` alias from user code and plugins
- If a module needs to add a virtual file that can be imported into the app, `addTemplate` can be used.
    - The file is added to Nuxt's internal virtual file system and can be imported from `#build/my-module-feature.mjs`.

```ts
import { addTemplate, defineNuxtModule } from '@nuxt/kit';

export default defineNuxtModule({
    setup (options, nuxt) {
        const globalMeta = {
            charset: 'utf-8',
            viewport: 'width=device-width, initial-scale=1'
        };
        
        addTemplate({
            filename: 'meta.config.mjs',
            getContents: () => {
                return `export default ${JSON.stringify({ globalMeta })}`
            }
        });
    },
})
```

- This is how Nuxt generates its route file (`#build/routes.mjs`), its plugin registration file, and its auto-import declarations. 
- When a module calls `updateTemplates()`, it triggers a targeted HMR invalidation for just those virtual files rather than a full rebuild.
    - The `builder:watch` hook can be used to trigger a rebuild of a registered template when specific files change.

### SSR

- When Nuxt renders a page server-side:
    - `useAsyncData` / `useFetch` calls execute on the server and their results are stored in `nuxtApp.payload.data`.
    - The payload is serialized and embedded in the HTML as a JSON blob (`<script>` tag).
    - On the client, Nuxt re-creates the `nuxtApp`, reads the payload, and populates the same `useAsyncData` keys — preventing double-fetching.
    - Vue then hydrates the server-rendered DOM against the client-rendered VDOM.
- Since there are no dynamic updates and no DOM operations occur on the server, Vue lifecycle hooks such as `onBeforeMount`, `onMounted`, and subsequent hooks are NOT executed during SSR. 
    - By default, Vue pauses dependency tracking during SSR for better performance. There is no reactivity on the server side because Vue SSR renders the app top-down as static HTML.

### Layers

- A layer is essentially a partial Nuxt app. 
    - It can contribute its own `nuxt.config`, pages, components, composables, server routes, and modules. 
    - Since layers can be extended by other Nuxt apps, they can be useful for: monorepo base configurations, design system packages, and shared feature sets across multiple Nuxt apps.
- Nuxt's layer system is built on top of the same config-merge mechanism that modules use. 
- When Nuxt resolves `extends: ['some-layer']`, it merges that layer's config and virtual directory tree into the base app using `unjs/c12` (a library for config loading with inheritance).

### Lifecycle

- Nuxt is a hook-driven pipeline. 
    - The framework itself is built upon the same hooks and Kit utilities that are used by module authors.

```
nuxt dev / nuxt build
│
├─ 1. loadNuxtConfig()         → read + normalize nuxt.config.ts
├─ 2. initNuxt()               → create the `nuxt` object + hookable
├─ 3. hook: modules:before
├─ 4. [install modules]        → each module's setup() runs sequentially
├─ 5. hook: modules:done
├─ 6. hook: app:resolve        → resolve app/ dir structure
├─ 7. generateApp()            → run addTemplate getContents(), write .nuxt/
├─ 8. hook: app:templates
├─ 9. hook: app:templatesGenerated
├─ 10. hook: build:before
├─ 11. Vite builds client + server bundles
├─ 12. hook: build:done
│
│  [At request time — inside the output bundle]
│
├─ 13. Nitro receives HTTP request
├─ 14. createNuxtApp()         → create `nuxtApp` + runtime hookable
├─ 15. hook: app:created       → plugins initialize (server side)
├─ 16. hook: app:rendered      → SSR done, payload populated
├─ 17. HTML + payload sent to client
├─ 18. Client: nuxtApp restored from payload
├─ 19. hook: app:beforeMount
├─ 20. Vue mounts + hydrates
└─ 21. hook: app:mounted
```

> [!quote]- Nuxt Lifecycle
> ![Nuxt Lifecycle](nuxt.lifecycle.png)

- [Nuxt Lifecycle (Documentation) 📄](https://nuxt.com/docs/4.x/guide/concepts/nuxt-lifecycle)

### Miscellany

- **Performance**
    - [Nuxt Performance (Documentation) 📄](https://nuxt.com/docs/4.x/guide/best-practices/performance)





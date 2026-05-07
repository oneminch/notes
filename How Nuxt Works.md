## The Initialization Pipeline

- Everything starts with `loadNuxt`, which is the programmatic entry point (the CLI calls it too).

- **Step 1: Config loading**
    - `loadNuxt` calls `loadNuxtConfig` (which uses `c12` under the hood) to resolve `nuxt.config.ts`, merge layers, and produce a fully-resolved `NuxtOptions` object.

- **Step 2: Core modules are pushed onto `options._modules`**
    - Before the Nuxt instance is even created, the built-in modules are registered into `options._modules`. 
    - These are the modules that implement all the opinionated directory conventions:
        - `pagesModule` → scans `pages/`
        - `metaModule` → `@unhead` integration
        - `componentsModule` → scans `components/`
        - `importsModule` → auto-imports from `composables/`, `utils/`
        - `compilerModule` → Vue compiler transforms
        - `schemaModule` → runtime config schema

- **Step 3: `createNuxt` — the Nuxt instance**
    - `createNuxt` builds the core `Nuxt` object. 
    - The most important things it sets up are:
        - `nuxt.hooks` — a `hookable` instance (from the `hookable` library). Every lifecycle event goes through this.
        - `nuxt.vfs` — a plain `Record<string, string>` that is the **Virtual File System** (more on this below).
        - `nuxt.apps` — a registry of `NuxtApp` objects (one per app, usually just `default`).
        - `nuxt.ready()` — a method that triggers `initNuxt`.

- **Step 4: `initNuxt` — module installation**
    - When `nuxt.ready()` is called, `initNuxt` runs. 
    - This is where the real work happens:
        1. Registers user hooks from all layers.
        2. Fires `modules:before`.
        3. Calls `installModules(options._modules, ...)` — this runs every module's `setup()` function in order.
        4. Fires `modules:done`.
        5. Calls `bundleServer(nuxt)` to initialize Nitro.
        6. Fires `ready`.

## The Module System

- Every feature in Nuxt — pages, components, auto-imports — is implemented as a Nuxt module. Modules are defined with `defineNuxtModule` from `@nuxt/kit`.

```ts
defineNuxtModule({
    meta: { name: 'my-module', configKey: 'myModule' },
    setup(options, nuxt) {
        // hook into nuxt.hooks, add templates, add plugins, etc.
    }
})
```

- The `setup` function receives the resolved `nuxt` instance and can call any `@nuxt/kit` utility. 
- The key kit utilities are:
    - `addPlugin(src)` — registers a runtime plugin
    - `addTemplate({ filename, getContents })` — registers a virtual file
    - `addComponent(...)` — registers a component for auto-import
    - `addImports(...)` — registers composables for auto-import
    - `nuxt.hook(...)` — subscribes to lifecycle hooks

## Directory Structure ➡ Working App

- What essentially happens in Nuxt is: **modules scan directories, build data structures, then generate virtual files (templates) that the bundler imports**.

- **`resolveApp` — scanning the filesystem**
    - `generateApp` first calls `resolveApp`, which scans the filesystem to populate a `NuxtApp` object:
        - **`app.mainComponent`** → finds `app.vue` / `App.vue` across layers
        - **`app.rootComponent`** → finds `app.root.vue` or falls back to the built-in `nuxt-root.vue`
        - **`app.errorComponent`** → finds `error.vue` across layers
        - **`app.layouts`** → scans `layouts/**/*.vue` across all layers
        - **`app.middleware`** → scans `middleware/**/*.{ts,js}` across all layers
        - **`app.plugins`** → scans `plugins/**/*.{ts,js}` across all layers, merged with `nuxt.config.plugins`

- **Pages scanning (the `pagesModule`)**
    - The pages module uses the `unrouting` library to convert the `pages/` directory tree into a Vue Router route config. 
    - The flow is:
        1. `resolveFiles(pagesDir, '**/*.vue')` — glob all page files
        2. `buildTree(files)` — `unrouting` converts filenames to route segments (`[id].vue` → `/:id`, `[...slug].vue` → `/:slug(.*)*`)
        3. `toVueRouter4(tree)` — emits a `NuxtPage[]` array
        4. `augmentPages()` — reads each `.vue` file with `oxc-parser` to statically extract `definePageMeta(...)` calls
        5. Fires `pages:extend` and `pages:resolved` hooks so modules can modify routes

- **Components scanning (the `componentsModule`)**
    - The components module scans `components/` directories and registers each component for auto-import. 
    - It also handles the `components.islands.mjs` virtual file for server components.

- **Auto-imports (the `importsModule`)**
    - The imports module uses `unimport` to scan `composables/` and `utils/` directories and register their exports as auto-imports. 
    - At build time, `unimport` injects the necessary `import` statements into files that use those composables.

## The Virtual File System (VFS) and Templates

- This is the glue between the scanning phase and the bundler. 
- After scanning, Nuxt generates **virtual files** — JavaScript modules that exist only in memory (in `nuxt.vfs`) and are served to the bundler on demand.

- A template is an object with a `filename` and a `getContents` function:

```ts
// from packages/nuxt/src/core/templates.ts
export const clientPluginTemplate: NuxtTemplate = {
  filename: 'plugins.client.mjs',
  async getContents(ctx) {
    // generates: import foo from '/path/to/plugins/foo.ts'; export default [foo]
  }
}
```

- `generateApp` compiles all templates and stores their output in `nuxt.vfs`:

```ts
nuxt.vfs['#build/plugins.client.mjs'] = "import foo from '...'; export default [foo]"
```

| Virtual path                | What it contains                                                   |
| --------------------------- | ------------------------------------------------------------------ |
| `#build/plugins`            | All registered plugins (client or server depending on environment) |
| `#build/root-component.mjs` | Re-exports `nuxt-root.vue` (or user's `app.root.vue`)              |
| `#build/app-component.mjs`  | Re-exports `app.vue`                                               |
| `#build/routes.mjs`         | The full Vue Router route config (generated by pages module)       |
| `#build/css.mjs`            | All global CSS imports                                             |
| `#build/nuxt.config.mjs`    | Runtime config values baked in at build time                       |

- `VirtualFSPlugin` (an `unplugin`) intercepts module resolution in Vite/Webpack. 
    - When the bundler tries to resolve `#build/plugins`, the plugin looks it up in `nuxt.vfs` and returns the generated string as the module source.

## The Build Pipeline

- After `nuxt.ready()`, `build` (in `packages/nuxt/src/core/builder.ts`) is called:
    1. `createApp(nuxt)` — creates the `NuxtApp` data structure
    2. `generateApp(nuxt, app)` — runs `resolveApp` + compiles all templates into `nuxt.vfs`
    3. Fires `build:before`
    4. `bundle(nuxt)` — calls the active bundler's `bundle()` function (Vite, Webpack, or Rspack)
    5. Fires `build:done`
    6. Nitro's hook on `build:done` then runs `prepare(nitro)` + `build(nitro)` to produce the server output
- The bundler (e.g., Vite) resolves `packages/nuxt/src/app/entry.ts` as the entry point and builds two separate bundles: one for the **client** and one for the **server** (SSR).

## The Runtime Entry Point

- `packages/nuxt/src/app/entry.ts` is the actual application entry. 
- It imports from virtual files and bootstraps the Vue app:
    - **On the server** (`import.meta.server`):
        1. `createApp(RootComponent)` — creates a Vue app with `nuxt-root.vue`
        2. `createNuxtApp({ vueApp, ssrContext })` — wraps it in a Nuxt context
        3. `applyPlugins(nuxt, plugins)` — runs all registered plugins in order
        4. Returns the Vue app for `vue-bundle-renderer` to call `renderToString()` on
    - **On the client** (`import.meta.client`):
        1. Detects if the page was SSR'd (checks `window.__NUXT__.serverRendered`)
        2. `createSSRApp(RootComponent)` (for hydration) or `createApp(RootComponent)` (SPA)
        3. `applyPlugins(nuxt, plugins)`
        4. `vueApp.mount(vueAppRootContainer)` — mounts to `#__nuxt` (or configured container)

## The SSR Request Lifecycle (Runtime)

- At runtime, every HTTP request goes through Nitro → the SSR renderer:
    1. Nitro receives a request and routes it to the renderer handler (`packages/nitro-server/src/runtime/handlers/renderer.ts`)
    2. The renderer calls `getRenderer()` which imports the server bundle (`dist/server/server.mjs`)
    3. It creates a `NuxtSSRContext` (holds `event`, `payload`, `head`, `runtimeConfig`)
    4. Calls `renderer.renderToString(ssrContext)` — this invokes `entry.ts`'s server entry, which runs plugins and renders the Vue tree
    5. The `NuxtPayload` (data from `useAsyncData`, `useState`, runtime config) is serialized with `devalue` and injected as `<script type="application/json" id="__NUXT_DATA__">` into the HTML
    6. The client receives the HTML, reads `window.__NUXT__`, and hydrates

---

> *Disclaimer: The content on this page is AI-generated based on the [nuxt/nuxt](https://github.com/nuxt/nuxt) repo.*
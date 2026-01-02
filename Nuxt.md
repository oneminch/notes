## Lifecycle

- [Nuxt Lifecycle (Documentation) 📄](https://nuxt.com/docs/4.x/guide/concepts/nuxt-lifecycle)

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

## Commands

- `nuxt build`
	- Creates a `.output` directory with all the application, server and dependencies ready for production.
	- Creates an entry point that launches a ready-to-run [[Node]] server.
		- `node .output/server/index.mjs`
	- 

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

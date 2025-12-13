- NPM is a package manager for the JavaScript programming language.
- It is the default package manager for [[Node|Node.js]] and is included as a recommended feature in the Node.js install.
- It can manage packages that are local dependencies of a project, as well as globally-installed JavaScript tools.
- For a local project, all the dependencies of a project are managed through the `package.json` file, which specifies a range of valid versions using the semantic versioning scheme, allowing developers to auto-update their packages while at the same time avoiding unwanted breaking changes.
- NPM provides a CLI that can be used to download and install software.

## NPM scripts

- **NPM scripts** are a feature of NPM that allows defining custom scripts in a project's `package.json` file.
    - These scripts can be used to automate various development tasks, such as running tests, building a project, and starting a development server.
- They are easy to use and flexible with some built-in tasks included, such as `npm start` and `npm test`.

## `npx`

- [`npx`](https://www.npmjs.com/package/npx) stands for Node Package Execute.
- It allows us to run packages without having to install them globally.

## `package.json`

### Core

- **`name`** - A package's unique identifier on npm.
    - Scoped packages use `@scope/name format`.
- **`version`** - Follows semantic versioning (`major.minor.patch`).
    - Breaking changes bump major, new features bump minor, bug fixes bump patch.
- **`description`** - Shows up in search results and package listings.
- **`main`** - The default entry point for Node.js / CommonJS.
    - When someone does `require('lib-name')`, this file is loaded.
- **`types` / `typings`** - Points to a TypeScript declaration file (`.d.ts`).
    - Critical for TypeScript users.

### Modules

- **`type`** - Set to `"module"` for native ES modules, or omit/use `"commonjs"` for traditional Node.js.
- **`exports`** - The modern way to define entry points.
    - Replaces and supersedes `main`.
    - Order in conditional exports matters: `types` should come before `import`/`require` for proper TypeScript resolution.

```json
{
	"exports": {
		".": "./dist/index.js",
		"./utils": "./dist/utils.js"
	}
}
// "." → import lib from 'lib-name'
// "./utils" → import { something } from 'lib-name/utils'
```

```json
// Conditional Exports
// Lets bundlers pick the right format.
{
	"exports": {
		".": {
			"types": "./dist/index.d.ts",
			"import": "./dist/index.mjs",
			"require": "./dist/index.cjs"
		}
	}
}
```

- **`module`** - Points to the ES module version of a library.
    - Used by bundlers like webpack/rollup when `exports` isn't defined.

### Files & Distribution

- **`files`** - Whitelist of files/directories to include in the published package.
    - Typically `["dist"]`. Without this, source code, tests, etc. might accidentally get published.
- **`sideEffects`** - Helps bundlers with tree-shaking.
    - Set to `false` if code has no side effects (safe to eliminate unused exports). Or specify files that do have side effects:

```json
{
	"sideEffects": ["*.css", "./src/polyfill.js"]
}
```

### Metadata

- **`keywords`** - Array of search terms for `npm` discovery.
- **`repository`** - Where the source code lives.
    - Format: `{ "type": "git", "url": "https://github.com/..." }`
- **`author`** - Author name/email.
    - Can be string or object format.
- **`license`** - Use standard SPDX identifiers like `"MIT"`, `"Apache-2.0"`, etc.
- **`homepage`** - URL to project docs or homepage.
- **`bugs`** - Where users report issues: `{ "url": "https://github.com/.../issues" }`

### Dependencies

- **`dependencies`** - Packages the library needs at runtime.
    - These get installed when users install the library.
- **`devDependencies`** - Only needed for development (testing, building).
    - Not installed by consumers.
- **[[Peer Dependencies|`peerDependencies`]]** - Packages the consumer app must provide.
    - Common for plugins or framework libraries.
    - Helpful when the library needs a dependency to function, but shouldn't bundle it itself.
        - The consuming app typically has to bring its own installation of the peer dependency.
    - **`peerDependenciesMeta`** - Mark peer deps as optional.
        - `optional` can help prevent automatic installation by some package managers, and shows warnings instead of errors for unfulfilled conditions.

```json
{
	"name": "awesome-vue-components",
	"peerDependencies": {
		"vue": "^3.0.0"
	},
	"devDependencies": {
		"vue": "^3.0.0"
	}
}
```

```json
{
	"peerDependenciesMeta": {
		"react": { "optional": true }
	}
}
```

### Build & Tooling

- **`scripts`** - Build commands.
    - At minimum: `"build"`, `"test"`, `"prepublishOnly"` (runs before publishing).
- **`engines`** - Specify Node.js version requirements:

```json
{
	"engines": {
		"node": ">=20.0.0"
	}
}
```

- **`publishConfig`** - Controls publishing behavior:

```json
{
	"publishConfig": {
		"access": "public" // Required for scoped packages
	}
}
```

### Example

- For a dual-format library (ESM + CJS), a modern example setup might look like:

```json
{
	"name": "my-lib",
	"version": "1.0.0",
	"type": "module",
	"main": "./dist/index.cjs",
	"module": "./dist/index.mjs",
	"types": "./dist/index.d.ts",
	"exports": {
		".": {
			"import": {
				"types": "./dist/index.d.ts",
				"default": "./dist/index.mjs"
			},
			"require": {
				"types": "./dist/index.d.cts",
				"default": "./dist/index.cjs"
			}
		}
	},
	"files": ["dist"],
	"sideEffects": false
}
```

---

### Dependency Resolution & Overrides

- **`overrides`** (_npm_) / **`resolutions`** (_Yarn_) / **`pnpm.overrides`** (_pnpm_) - Force specific versions of nested dependencies across the entire dependency tree:
    - 👇 In the consuming app: all instances of `lodash` (even transitive deps) use the specified version. Useful for security patches or compatibility fixes.
    - Should be used in apps to control the dependency try and not in published libraries as it forces those resolutions on every consumer - potentially breaking their apps.

```json
{
	// npm
	"overrides": {
		"lodash": "^4.17.21",
		"package-name": {
			"vulnerable-dep": "^2.0.0"
		}
	},

	// Yarn
	"resolutions": {
		"**/@babel/core": "^7.20.0"
	},

	// pnpm
	"pnpm": {
		"overrides": {
			"dep": "^2.0.0"
		}
	}
}
```

- **`pnpm.onlyBuiltDependencies`** - Specifies which packages are allowed to run install scripts.
    - A security feature.
    - Only those packages can execute lifecycle scripts like `postinstall`.

```json
{
	"pnpm": {
		"onlyBuiltDependencies": ["sharp", "esbuild"]
	}
}
```

### Bundler & Build Tool Hints

- **`browser`** - Alternative entry point for browser environments.
    - Can be a string or object mapping:

```json
{
	"browser": "./dist/browser.js",

	// Or for replacing specific modules:
	"browser": {
		"./lib/server-only.js": "./lib/browser-shim.js",
		"fs": false
	}
}
```

- **`unpkg`** or **`jsdelivr`** - Entry point for CDN delivery:
    - When someone uses `https://unpkg.com/lib-name`, they get this file.

```json
{
	"unpkg": "./dist/my-lib.min.js",
	"jsdelivr": "./dist/my-lib.min.js"
}
```

- **`style`** - Main CSS file for the library:
    - Build tools can use this to automatically import styles.

```json
{
	"style": "./dist/styles.css"
}
```

### Miscellaneous

- **`workspaces`** - Define monorepo packages (`npm` / Yarn / `pnpm`):

```json
{
	"workspaces": ["packages/*", "apps/*"]
}
```

- **`funding`** - Shows funding info when users install:

```json
{
	"funding": {
		"type": "github",
		"url": "https://github.com/sponsors/username"
	},

	// Or multiple sources:
	"funding": [
		"https://github.com/sponsors/username",
		{ "type": "patreon", "url": "https://patreon.com/..." }
	]
}
```

- **`bin`** - Create CLI commands.
    - Maps command name to executable file.
        - File used when running `npx lib-name`.
    - When installed (either locally or globally), users can run `my-cli` → `lib-name/bin/cli.js` executes.

```json
{
	"bin": {
		"my-cli": "./bin/cli.js"
	},

	// For single command matching package name:
	"bin": "./bin/cli.js"
}
```

- **`packageManager`** - Declare which package manager and version to use (`Corepack`):
    - Corepack enforces this version automatically.

```json
{
	"packageManager": "pnpm@10.10.0"
}
```

- **`optionalDependencies`** - Dependencies that won't fail install if unavailable.
    - Useful for platform-specific packages:

```json
{
	"optionalDependencies": {
		"fsevents": "^2.3.0"
	}
}
```

- **`bundledDependencies`** - Array of dependencies to bundle with a package (ship their `node_modules`):
    - Rarely used
    - **Use cases**:
        - Including private libraries not available on npm.
        - Lock the exact dependency code used to prevent breaking if [[Semantic Versioning|semver]] changes.
        - No additional network requests needed for needed deps as they are in the tarball and not fetched from the registry.
        - Vendoring a forked dep with critical patches.

```json
{
	"bundledDependencies": ["internal-auth-lib"]
}
```

```
my-package-1.0.0.tgz
├── package.json
├── index.js
└── node_modules/
    └── internal-auth-lib/  ← Bundled
```

- **`private`** - Prevents accidental publishing:
    - Essential for monorepo root packages or internal libs.

```json
{
	"private": true
}
```

- **`config`** - Configuration values accessible in scripts.
    - Access via `npm_package_config_port` in scripts.

```json
{
	"config": {
		"port": "8080"
	}
}
```

## Publishing a Package

### Manual Process

1. Initialize a project with `npm init`.
2. Write package code.
3. Create a `README.md` file and add a license.
4. Test package locally.

```bash
npm install /path/to/package
```

5. Login to `npm` using `npm login`.
6. If package is written in [[TypeScript]], it needs to be compiled to [[JavaScript]].
7. Publish to `npm` and verify package is available online.

```bash
npm publish --access public
```

### Automated Process with GitHub Actions

- Requires `npm` token to be generated from profile.

```yml
# .github/workflows/publish.yml
name: Publish Package

on:
    push:
        branches:
            - main
        # OR
        # tags:
        #   - 'v*.*.*'

jobs:
    publish:
        runs-on: ubuntu-latest
        steps:
            - uses: actions/checkout@v2
            - name: Set up Node.js
              uses: actions/setup-node@v2
              with:
                  node-version: "14"
            - run: npm install
            - run: npm run build
            - run: npm publish
              env:
                  NODE_AUTH_TOKEN: ${{ secrets.NPM_TOKEN }}
```

### Versioning

- Before publishing, `npm`'s built-in versioning command can be used to manually update a package version.
- `npm version`
    - updates the version in `package.json`,
    - creates a commit with the message `vX.Y.Z`, and
    - tags the commit with the version number.

```bash
npm version patch  # for a patch update
npm version minor  # for a minor update
npm version major  # for a major update
```

- The process can be automated when using tools like `semantic-release`.
    - Using [[Git#Conventional Commits|conventional commits]] automatically determines the next version number.

- Read More 📄 - [Modules: Packages (Node.js Docs)](https://nodejs.org/docs/latest/api/packages.html#modules-packages)

---

## Further

### Videos 🎥

![How To Create And Publish Your First NPM Package](https://www.youtube.com/watch?v=J4b_T-qH3BY)

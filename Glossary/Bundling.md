---
aliases:
    - Bundlers
    - Build Tools
---

- **Bundlers** combine source code and dependencies into a single file (or a few files) for distribution.
    - They resolve dependencies, handle module formats (ESM, CommonJS, etc.), and output optimized (e.g. minified and tree-shaken) bundles.
    - Perform several key functions, such as
        - module concatenation,
        - minification,
        - resource management, and
        - dependency resolution.
    - Can also help manage other assets like stylesheets, images, and fonts, and track the dependencies between modules, enabling inclusion of only those parts of the code actually used in the project.
    - e.g. Rollup / Rolldown, esbuild and Webpack (at its core).

- **Build tools** are broader in scope.
    - Orchestrate the entire build process, from development to production.
    - Often include a bundler, but also perform several key functions, such as
        - [[Transpilers|transpilation]],
        - asset processing (images, CSS),
        - development server setup, and
        - hot module replacement (HMR).
    - Provide a more integrated development experience, sometimes using multiple bundlers under the hood for different tasks.
        - e.g. Vite uses esbuild for fast development and Rollup for production builds, while also providing a dev server, HMR, and plugin system.
    - Vite, Parcel, Turbopack for web development
    - tsup / tsdown, unbuild for library development

---

## Further

### Videos 🎥

![Module Bundlers Explained (YouTube)](https://www.youtube.com/watch?v=5IG4UmULyoA)

- A performance optimization technique used in modern JavaScript.
- Involves breaking code into smaller chunks (or "bundles") that can be loaded on demand, rather than all at once.
- **Benefits**:
    - _Faster Initial Load_ - Users download only the code needed for the current page or feature, reducing the initial bundle size.
    - _Efficient Caching_ - Smaller, focused bundles can be cached more effectively by browsers.
    - _Better Performance_ - Reduces memory usage and speeds up execution, especially for large applications or libraries.
    - _Tree-shaking_ (For Library Developers) - Bundlers can eliminate unused code, further reducing bundle size.
    - _Modular Design_ (For Library Developers) - Encourages writing smaller, focused modules.
- Not automatic by default.
- Bundlers like Vite and Webpack analyze code and automatically split it into chunks based on:
    - **Dynamic Imports:** `import()` can be used to specify which parts of code should be loaded asynchronously.
    - **Route-based Splitting:** Common in frameworks like React or Vue, where each route loads its own chunk.
    - **Library Splitting:** For library authors, code can be split into core and optional features, so users only import what they need.

```ts
// Bundlers will automatically create a separate chunk for `module.js`
const module = await import("./module.js");
```

- When certain dependencies are marked as "external," they won’t be bundled into a package.
    - They must be provided by the user.
- Helps avoid duplicate code and reduce [[Bundling|bundle]] size.

- **Example:** If a library uses `vue`, it can be marked as external so users can use their own [[Vue]] version.

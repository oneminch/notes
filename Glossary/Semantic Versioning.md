---
alias: SemVer
---

- Semantic Versioning (SemVer) is a versioning scheme for software that conveys meaning about the underlying changes through version numbers.
- It helps developers and users understand the nature and impact of changes in a release.
- It follows the format:

```
MAJOR.MINOR.PATCH
```

- **MAJOR Version (X)**
    - Incremented when there are incompatible changes.
    - Indicates breaking changes that require users to modify their code to adapt.
    - e.g. If the current version is `1.23.11`, a major change would be introduced in `2.0.0`
- **MINOR Version (Y)**
    - Incremented when new features or enhancements are added in a backward-compatible manner.
    - Indicates new functionality that does not break existing code.
    - e.g. If the current version is `1.23.11` and a minor feature is added, the new version would be `1.24.0`.
- **PATCH Version (Z)**
    - Incremented for backward-compatible bug fixes and minor improvements.
    - Indicates small changes that do not affect the software’s API.
    - Example: If the current version is `1.23.11` and a bug is fixed, the new version would be `1.23.12`.

- **Additional Labels**
    - **Pre-Release Versions**
        - Indicated by appending a hyphen and an identifier.
            - e.g., `1.0.0-alpha`, `1.0.0-beta.1`
        - For testing and may not be stable or fully compatible with the final release.
    - **Build Metadata**
        - Appended with a plus sign and additional data.
            - e.g., `1.0.0+build.123`
        - Ignored when determining version precedence.

> [!example]
>
> - Initial Release: `1.0.0`
> - Adding a New Feature: `1.1.0`
> - Fixing a Bug: `1.1.1`
> - Introducing Breaking Changes: `2.0.0`
> - Pre-release for Testing: `2.0.0-alpha.1`
> - Including Build Metadata: `2.0.0+build.20230725`

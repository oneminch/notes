---
alias: Hot Module Replacement
---

- A feature of [[bundlers]] and build tools that allows developers to update modules (like JavaScript, CSS, or other assets) in an application without a full page reload.
- Unlike a full refresh, HMR tries to preserve the state of an application (like Vue component state or Pinia store).
- When change to a file is made, the build tool:
    1.  Detects the change.
    2.  Recompiles only the changed module.
    3.  Swaps the old module with the new one in the running application.
- Tools like Vite support HMR.

---

![How Hot Module Replacement REALLY Works](https://www.youtube.com/watch?v=dgTGALM2REU)

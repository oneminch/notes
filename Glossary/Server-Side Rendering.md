---
alias: SSR
---

- **Context**
    - In single page applications (SPAs), the [[rendering]] process utilizes AJAX to update parts of a page without the need to refresh. This gives web apps the feel of interactive native apps. By default, this process is done in the browser.
- Server-side rendering (SSR) transfers this responsibility from the browser to the server.
- Static [[HTML]] markup is generated on the server (which uses a backend runtime like [[Node]]) by running the [[JavaScript]] code that builds the UI components, and the browser gets a fully rendered HTML page.
- In addition to improvements on core web vitals metrics and initial loading times, this approach also boosts [[Search Engine Optimization]] and [[Accessibility]]. But, it can be slower for dynamic content.
- SSR is also referred to as ==Universal Rendering== or ==Isomorphic Rendering==.

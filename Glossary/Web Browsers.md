## Standardization

- Web standards are the technologies used to build on the web. They exist as long technical documents called _specifications_ (_specs_), and are created by standards bodies (which are institutions that invite groups of people from different tech companies to come together and agree on how the technologies should work in the best way). The [[W3C]] is the best known body. Others include:
    - **WHATWG**: maintain the living standards of [[HTML]].
    - **ECMA**: publish standard for ECMAScript, which [[JavaScript]] is based on.
        - Early versions (ES1 to ES6) were named numerically, increasing by 1 for each new edition.
        - Starting with ES2015 (initially called ES6), the naming convention changed to reflect the year of release.
        - Since 2015, major versions of ECMAScript have been published annually in June.
        - The current naming format is "ECMAScript [YEAR]" or "ES[YEAR]" for short.
            - e.g. ECMAScript 2024 (ES2024) - 15th edition, released in June 2024
        - The term "ESNext" is used dynamically to refer to the next version of ECMAScript that is currently in development.
    - **Khronos**: publish technologies for 3D graphics, such as WebGL.
- Due to the open nature of web standards, the web remains a freely-available public resource.
- Specifications detail exactly how a technology should work, and are intended for use by software engineers to implement the technologies (usually in web browsers).
    - e.g. HTML Living Standard describes how HTML (elements, and their APIs as well as surrounding technologies) should be implemented.

## Rendering

- **The Flow**
    - HTML document is parsed into a 'content tree'.
    - Using styling information, the 'render tree' is constructed.
        - This contains the nodes with their respective visual attributes, and in the order they will be displayed on the screen.
    - The render tree goes thru the 'layout' process.
        - Each node is given the exact coordinates where it should appear on the screen.
    - In the next step, the render tree is 'painted'.
        - Nodes will be traversed and each one will be painted to the UI.
- For improved UX, this process is done in small chunks of the document. Content is displayed on the screen as soon as possible.

## DevTools

- In the context of JavaScript debugging, a **_breakpoint_** is a point in code where the debugger will automatically pause the execution.
    - The `debugger;` command also pauses code execution when the developer tools are open.
    - An error also paused execution.

---

### Books 📚

- [Web Browser Engineering](https://browser.engineering/)

### Reads 📄

- [How modern browsers work (Addy Osmani)](https://addyo.substack.com/p/how-modern-browsers-work) ⭐

- [How Browsers Work (web.dev)](https://web.dev/howbrowserswork/) ⭐

- [67 Weird Debugging Tricks Your Browser Doesn't Want You to Know (Alan Norbauer)](https://alan.norbauer.com/articles/browser-debugging-tricks)

### Resources 🧩

- [New to the Web Platform](https://web.dev/tags/new-to-the-web/)

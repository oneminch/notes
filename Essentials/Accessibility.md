---
alias: A11y
---

> [!quote] MDN
> Web accessibility involves ensuring that content remains accessible, regardless of who and how the web is accessed.

- **Web Content Accessibility Guidelines (WCAG)** is a document published by the [[W3C]] that includes a technology-agnostic and international set of guidelines for accessibility conformance.
    - It provides a single shared standard for web accessibility based on four guiding principles:
        - **Perceivable** - available to basic senses (either natively or thru assistive technologies (ATs)):
            - Adding alternative text to non-decorative images.
            - Adding text track to videos.
            - Ensuring color is not the only method used to convey meaning.
        - **Operable** - interactable through any accessible means:
            - Using more than one method to navigate a page.
        - **Understandable** - clear and unambiguous content:
            - Using clear and concise writing for content.
        - **Robust** - accessible by a wide range of user agents and ATs:
            - Ensuring content is accessible even as technologies used evolve.
- Different operating systems provide a set of special accessibility APIs that expose information useful for ATs.
    - Web browsers utilize these APIs to provide basic accessibility wherever semantic information (excluding styling information, and JavaScript) is used.
    - Such information is structured into an _accessibility tree_.
    - We can further improve accessibility using features from the WAI-ARIA specification, which add semantic information to the accessibility tree.
- The accessibility tree is created based on the [[DOM]] tree that contains A11y-related information for most HTML elements.
    - Each object in an accessibility tree has four properties:
        - **name**
        - **description**
        - **role**
        - **state**
    - Moreover, the accessibility tree contains information on the types of actions that can be performed with an element.
    - The Accessibility Object Model (AOM) is an initiative (work-in-progress) that aims to create a JavaScript API (similar to the [[CSSOM]]) to allow developers to modify / explore the accessibility tree for an HTML page.
        - These APIs allow developers to provide information to assistive technologies (ATs), and help them better understand the information provided to ATs by browsers.

## HTML

- In addition to the many other benefits (such as [[Search Engine Optimization|SEO]]), semantic markup is a good basis for accessibility.
- Use the right element for the right job.
    - This helps take advantage of underlying browser / OS accessibility APIs.
        - e.g. Using a `<button>` instead of a `<div>` for an interaction has the default added benefit of navigation and activation using a keyboard.
- Content structure matters just as much as using the right element for the right purpose.
    - Use the right tag hierarchy, specially with headings.
    - Only have one `<h1>` per page.
- Use clear and unambiguous language.
    - Make sure to avoid using language, characters, abbreviations or acronyms that are likely to get mispronounced by screen readers.
        - e.g. Write 4 to 8 instead of 4-8, Write February instead of Feb.
- Use the right techniques for content layout.
    - Don't use tables for content layout.
- Use labels that are contextual.
    - Avoid using labels such as "Click Here". Provide more context as these labels tend to be read out in isolation.
    - e.g. `<a href="/accessibility.html">Learn more about accessibility.</a>` ✅
- For media elements such as images, a descriptive alternative text should be provided.
    - If an `alt` text is not provided, screen readers will read out loud the image source URL.
    - To avoid this for decorative images,
        - an empty `alt` text should be provided, or
        - alternatively, a `role` attribute of `presentation` value should be specified to ensure screen readers skip reading out the alternative text.

```html
<img src="/path/to/image.png" alt="..." />

<img src="/path/to/image.png" alt="..." title="..." />

<img src="/path/to/image.png" aria-labelledby="img-label" />

<p id="dino-label">Image Description...</p>
```

- There is mixed reader support for implicitly associating a `<figcaption>` to a `<figure>`.
    - Using attributes, such as `aria-describedby`, creates this association.

```html
<figure>
	<img src="/path/to/image.png" alt="..." aria-describedby="image-desc" />
	<figcaption id="image-desc">Image Description...</figcaption>
</figure>
```

- UI controls, such as buttons, links and form controls, can be manipulated using keyboards by default.
    - Even though it's possible to build keyboard accessibility into any element, it's not advised to do so. It's better to use the right element for the right job.
- When using hyperlinks, leave clear signposts in link text when linking to external and/or non-HTML resources.

```html
<a href="link-to-video-stream"> Watch video (opens in new tab) </a>

<a href="link-to-download-item" download="default-save-filename">
	Download report (PDF, 5MB)
</a>
```

- Skip links (or skipnav) should be implemented to bypass repetitive content, such as navigation options, and to link to the main content on the page.
- Space should be added between interactive content and UI controls that are place in close proximity to each other.
    - This helps people who suffer from fine motor control issues.

## CSS

- It's not recommended to change default styles so much that they no longer look or behave as expected.
    - Select sensible text styles, such as font sizes and line heights.
        - Text color should also contrast well with its background color.
            - A high contrast ratio helps anyone using a device with a glossy screen to better read content in a bright environment, such as sunlight.
    - Headings should stand out, and list should look like lists.
    - There are recognized styling conventions, such as a dotted underline in the case of `<abbr>`, that we shouldn't significantly deviate from.
    - Feedback is essential when interacting with UI controls.
        - Even if slightly changed to match creative/brand needs, default feedback mechanisms like UI state styles (focus, hover, active), and pointer cursors should still remain.
- Using `visibility: hidden` or `display: none` hides content from screen readers.
- Users might want to override styles with their own custom styles for a variety of reasons:
    - to make text bigger,
    - to increase the contrast ratio.

## JavaScript

- JavaScript should be used to enhance an existing functionality, and not build it entirely.
    - An good example of this is providing client-side form validation using the Constraint Validation API. It's not necessary, but enhances the experience.
- When implementing functionalities using device-specific events (like mouse events, `mouseover` and `mouseout`), it's important to ensure the same functionalities can be activated by other means (`focus`, `blur` for keyboard users).

```js
thumbnail.onmouseover = showImg;
thumbnail.onmouseout = hideImg;

thumbnail.onfocus = showImg;
thumbnail.onblur = hideImg;
```

## WAI-ARIA

> Web Accessibility Initiative - Accessible Rich Internet Applications.

- A specification written by the [[W3C]] that defines a set of HTML attributes that can be applied to elements to provide semantic meaning.

> [!quote] W3C
> No ARIA is better than Bad ARIA.

- It's helpful to think of ARIA as a way of controlling the rendering of non-visual experience for screen reader users.
    - Bad ARIA can damage such experiences.
- It's used to make non-accessible controls accessible.
- ARIA should enhance accessibility for complex interfaces, but it should not replace semantic HTML.
- The spec has 3 main features:
    - **Roles** define what an element is or does.
        - By default, many semantic elements have a role, but it can be replicated using `role`.
        - For non-semantic elements, `role` can provide semantics.
            - e.g. `role="navigation"` (`<nav>`)
        - It can also be used to provide signposts to different components.
            - e.g. `role="search"` (Search `<form>`)
    - **Properties** define properties of elements to give them extra meaning.
        - e.g. `aria-required="true"` to specify that filling a form input is required
        - Screen readers can have difficulty with constantly changing content.
            - When an area of content is updated dynamically, `aria-live` can be used to signal that change.
    - **States** define the current conditions of elements.
        - Unlike properties, states can change throughout the lifecycle of an app.
        - They can be programmatically changed using JS.
        - e.g. `aria-disabled="true"`
- [**When should you use WAI-ARIA?**](https://developer.mozilla.org/en-US/docs/Learn/Accessibility/WAI-ARIA_basics#when_should_you_use_wai-aria)
    - **Answer**: _only when you need to_

## Multimedia

- Built-in media controls are not keyboard-accessible in most browsers.
- Since the HTML `<audio>` and `<video>` elements share an API (via `HTMLMediaElement`), we can use it to create custom controls and map their functionality in a way that is accessible.
- To better make media available to all users,
    - Provide transcripts and descriptions for audio content.
    - Include text tracks along with video content.
        - **Captions** - for deaf users who can't hear the audio track; provides transcript of words spoken and contextual information such as who is speaking, the mood, and the tone.
        - **Subtitles** - language translations of the audio dialog.
        - **Descriptions** - for visually impaired people; what is happening in the video.
        - **Chapter Titles** - markers for easier navigation.
- Video text transcripts need to be written in [[WebVTT]] file format, and are implemented using the `<track>` element.

## Mobile

- Following standard accessibility practices along with good UX design principles such as [[Responsive Web Design|responsive design]] can enhance user experience on mobile.
- Other considerations on mobile devices:
    - Always ensure zoom is enabled.
    - Ensure hidden menus are accessible.

## Best Practices

- **Avoid Unnecessary ARIA**
    - Use ARIA attributes only when necessary.
    - Prioritize native HTML elements with built-in a11y features.
    - If an element already has a clear semantic meaning, additional ARIA attributes may confuse assistive technologies.
- **Use Descriptive Labels**
    - For elements with no discernible text, use `aria-label`, `aria-labelledby`, or `aria-describedby` to provide context.
    - For example, an icon button should use `aria-label` to convey its function.
- **Dynamic Content Updates**
    - Use ARIA live regions (e.g., `aria-live="polite"`) to announce updates to dynamic content.
    - This ensures that screen readers notify users of changes without disrupting their current task.
- **Utilize ARIA Landmarks**
    - Implement ARIA landmarks (e.g., `role="banner"`, `role="main"`, `role="complementary"`) to define regions of a web page.
    - This helps users of assistive technologies navigate your site more efficiently by allowing them to jump to specific sections.

### Common Mistakes

- Low Color Contrast between Text and Background
- Missing Alternative Text for Images
- Missing or Poorly Used Form Field Labels
- Using Links and Buttons with no Discernible Text
- Missing Document Language
- Lack of Captions and Subtitles for Videos
- Inaccessible Keyboard Navigation
- Improper Heading Structure
- Inaccessible Multimedia Content

---

## Further

### Books 📚

- Accessibility for Everyone (Laura Kalbag)

- Inclusive Design Patterns (Heydon Pickering)

### Reads 📄

- [Accessibility essentials every front-end developer should know by Martijn Hols](https://martijnhols.nl/blog/accessibility-essentials-every-front-end-developer-should-know)

- [ARIA Live Regions (HTMHell)](https://www.htmhell.dev/adventcalendar/2023/22/)

- [How to Create a "Skip to Content" Link (CSS-Tricks)](https://css-tricks.com/how-to-create-a-skip-to-content-link/)

### Resources 🧩

- [ARIA Authoring Practices Guide (APG) (W3C)](https://www.w3.org/WAI/ARIA/apg/patterns/) ⭐

- [A Complete Guide To Accessible Front-End Components (Smashing Magazine)](https://www.smashingmagazine.com/2021/03/complete-guide-accessible-front-end-components/)

- [A11Y Checklist (The A11Y Project)](https://www.a11yproject.com/checklist/)

### Videos 🎥

![Why ARIA (YouTube)](https://www.youtube.com/watch?v=0AsaDbou4OQ)

---
alias: RWD
---

> A set of practices that allows web pages to alter their layout and appearance to suit different screen widths, resolutions, etc.

- It is a set of best practices and approaches to web design to create adaptive (responsive) layouts.

## Media Queries

- Enable the type of layout switch that could adapt to different screen resolutions using only CSS; originally this was only possible using [[JavaScript]] which detected the screen resolution and loaded the correct CSS. Either the CSS `@media` at-rule can be used in the stylesheet or the `media` attribute can be used in the markup to load a separate stylesheet.

- Media queries are introduced at certain points which leads to layout changes. These points are called **_breakpoints_**.

```css
@media media-type and (media-feature-rule) {
	/* CSS rules go here */
}
```

```html
<link
	rel="stylesheet"
	href="landscape.css"
	media="all and (orientation: landscape)"
/>
<link
	rel="stylesheet"
	href="portrait.css"
	media="all and (orientation: portrait)"
/>
```

### Media types

- can be any of `all`, `print` or `screen`.
    - These are optional; An omitted type implies a default value of `all`.

### Media feature rules

**Viewport width**

- When creating responsive designs, the change we detect most is the viewport width: `min-width`, `max-width`, and `width`.
- For instance, to change `color` to `blue` if the viewport _is 768 pixels or narrower_:

```css
@media screen and (max-width: 768px) {
	body {
		color: blue;
	}
}
```

- The following applies when two conditions are met:
    1. page is displayed as `screen` media (not print document)
    2. viewport is "at least" 768 pixels wide.

```css
@media screen and (min-width: 768px) {
	.wrapper {
		margin: 1.5em 3em;
	}
}
```

**`orientation`**

- allows to test for `portrait` or `landscape` orientation modes, which can help create layouts optimized for devices in either mode (like portrait for mobile or tablet devices).

**`hover`**

- `@media (hover: hover) {}` - indicates some sort of pointing device is being used; touchscreen and keyboard navigation are excluded.

**`pointer`**

- `none`- no pointing device, e.g. voice or keyboard navigation
- `fine` - mouse or trackpad
- `coarse` - finger on a touchscreen
- With this feature rule, interfaces can be designed for better interaction, e.g. one with larger hit areas if the user is interacting with a touchscreen device.

> [!note]
> Feature rules can be used in combination using logic: `and`, `not`, `,` (for _or_)

```css
/* and logic */
@media screen and (min-width: 600px) and (orientation: landscape) {
	body {
		color: blue;
	}
}

/* or logic */
@media screen and (min-width: 600px), screen and (orientation: landscape) {
	body {
		color: blue;
	}
}

/* not logic */
@media not all and (orientation: landscape) {
	body {
		color: blue;
	}
}
```

- **Mobile-first design** uses media queries to create a single-column layout for mobile devices, then checks for larger screens and implements a multi-column layout. It's quite often the best approach to follow.

## Flexible grids

- Layout methods such as Multi-column, Flexbox, and Grid can be used along with Media Queries to achieve responsive design.

## Responsive images

```css
/* simplest approach to responsive images */
img {
	max-width: 100%;
}
```

- More about responsive images in [[HTML#images-and-multimedia]].

## Responsive typography

> ...describes changing font sizes within media queries to reflect lesser or greater amounts of screen real estate.

> [!note]
> Text size should _never_ be set using viewport units (`vw`) alone due to issues with zooming. However, interesting results can be achieved when used together with other relative units (`em` & `rem`) and `calc()`; even more control can be achieved using `clamp()`.

## Viewport `<meta>` tag

```html
<meta name="viewport" content="width=device-width,initial-scale=1" />
```

> ...tells mobile browsers that they should set the width of the viewport to the device width, and scale the document to 100% of its intended size.

## Internationalization

- Logical properties update according to the writing mode and content flow. It refers to the contextual starting margin of a content: left in a left-to-right language and right in a right-to-left language. A property like `margin-left` is directional, while `margin-inline-start` is logical.

- Modern [[CSS]] layout techniques like Flexbox and Grid use logical properties by default.

```css
p {
	text-align: right; /* ⛔ */
	text-align: end; /* ✅ */
}
```

- Identify the language of a page using the `lang` attribute on the root (`<html>`) element. The attribute can go on any element to identify the content's language.
- Identify the language of a linked document using the `hreflang` attribute on the anchor (`<a>`) and link (`<link>`) elements.

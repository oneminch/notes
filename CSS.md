## Introduction

- CSS - Cascading Style Sheets
- Comments: `/* ... */`
- `selector { property : value; }`
    - Declaration: `property : value;`
    - Declaration Blocks: Two or more declarations
    - Ruleset / Rule: `selector { property : value; }`

## Specifications

- CSS is developed by a group within the [[W3C]] called the CSS Working Group. New features are developed, or specified, by this group.
- An external stylesheet contains CSS in a separate file with a `.css` extension.
- An internal stylesheet resides within an HTML document inside a `<style>` element inside the `<head>`.
- Inline styles are CSS declarations that affect a single HTML element, contained within a `style` attribute.
- **Order of Priority for Styles**: Inline > Internal > External > Browser Default

> [!note]
> An internal stylesheet will lose its priority if the external stylesheet declaration is placed at after the internal CSS inside the [[HTML]].

- If a property is unknown, or if a value is not valid for a given property, the declaration is processed as _invalid_. It is completely ignored by the browser's CSS engine.
- A function consists of the function name, and parentheses to enclose the values for the function. e.g. `calc()`, `rotate()`
- CSS @rules provide instruction for what CSS should perform or how it should behave. e.g. `@media`, `@import`
- Shorthand properties set several values in a single line. e.g. `background`, `border`. A value not specified in CSS shorthand reverts to its initial value; This means an omission in CSS shorthand can override previously set values.
- Just as browsers ignore white space in [[HTML]], browsers ignore white space inside CSS. The value of white space is how it can improve readability. Though white space separates values in CSS declarations, property names never have white space.

## Selectors

- Element(s) which are selected by the selector are rta _the subject of the selector_.
- _Type selectors_ match elements by node name. e.g. `a`
- _Attribute selectors_:
    - `[attr]` - matches elements with an attr attribute (whose name is the value in square brackets).
    - `[attr=value]` - matches elements with an `attr` attribute whose value is exactly value — the string inside the quotes.
    - `[attr^=value]` - matches elements with an `attr` attribute whose value begins with value.
    - `[attr$=value]` - matches elements with an `attr` attribute whose value ends with value.
    - `[attr*=value]` - matches elements with an `attr` attribute whose value contains value anywhere within the string.
    - `[attr~=value]` - matches elements with an `attr` attribute whose value is exactly value, or contains value in its (space separated) list of values (like classes).
    - `[attr|=value]` - matches elements with an `attr` attribute whose value is exactly value or begins with value immediately followed by a hyphen.
    - `i` can be added to attribute selectors before the closing bracket to match character case-insensitively. e.g. `li[class^="active" i]`
- _Pseudo-classes_ style certain states of an element. e.g. `:hover`
- _Pseudo-elements_ select a certain part of an element rather than the element itself; They typically start with a double colon `::`. e.g. `::first-line`, `::marker`, `::selection`
    - `::before` and `::after` are used along with the `content` property to insert content into documents using CSS.
        - `::before` or `::after` can only be inserted to an element that accepts child elements; it won't work on elements such as `<img />`, `<video>` and `<input>` (with the exception of `input[type="checkbox"]`).
        - Inserting strings of text from CSS isn't really something that's done very often on the web however, as it affects [[Accessibility]] with some screen readers and might be hard for someone to find and edit in the future.
        - A more valid use of these pseudo-elements is to insert an icon.
    - [Selector References](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Selectors/Pseudo-classes_and_pseudo-elements#reference_section)

> [!note]
> Some early pseudo-elements used single colon syntax.

- _Combinators_ combine other selectors in order to target elements within documents. They combine other selectors in a way that gives them a useful relationship to each other and the location of content in the document.
    - **Descendant Combinator** ('` `' / single space) combines two selectors such that elements matched by the second selector are selected if they have an ancestor element matching the first selector. e.g. `div p`
    - **Child combinator** (`>`) matches elements who are direct children of ancestor element. e.g. `div > p`
    - **Adjacent sibling combinator** (`+`) matches the next adjacent sibling of an element. e.g. `h1 + p`
    - **General sibling combinator** (`~`) matches all siblings of an element, not necessarily ones who are adjacent.

## Cascade & Specificity

- Later styles replace conflicting styles that appear earlier in the stylesheet. This is the **cascade** rule. The way the cascade behaves is key to understanding CSS.
- Parent elements can't be targeted; the cascade can only be downwards, selecting child elements.

For the cascade, there are 4 stages to consider, listed in order of importance (Later ones overrule earlier ones):

- **Source Order**: If you have more than one rule of exactly the same weight, the one that comes last in the CSS wins.
    - This also takes in consideration the order of `<link>` tags, declarations in embedded `<style>` tags, and inline CSS defined in the `style` attribute of an element.
    - Unless another ruleset has an `!important` declaration, an inline CSS defined in the `style` attribute of an element overrides all other CSS.
    - If declarations in embedded `<style>` tags appear in the source **_after_** `<link>` tags that contain similar declarations, the embedded declarations are used.
- **Specificity**: If an earlier conflicting rule is applied, the earlier rule has a higher specificity. It is important to note that only the properties which are the same are overwritten, not the whole ruleset.
    - **How does the browser calculate specificity?** Essentially, a value in points is awarded to different types of selectors, and adding these up gives you the weight of that particular selector, which can then be assessed against other potential matches.
        - [Specificity Calculation](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Cascade_and_inheritance#specificity_2)
    - When in conflict between a class selector and an element selector, the class prevails. A class is rated as being more specific, as in having more **specificity** than the element selector, so it cancels the other conflicting style declaration.
    - **Specificity** is how the browser decides which rule applies if multiple rules have different selectors, but could still apply to the same element. It is basically a measure of how specific a selector's selection will be, despite any cascading rules:
        - An element selector is less specific.
        - A class selector is more specific.

> [!note]
> The universal selector (`*`), combinators (`+`, `>` , `~`, `' '`), and negation pseudo-class (`:not`) have no effect on specificity.

- **Origin**: The cascade takes into account the origin of the CSS which could be the user agent base styles (browser's internal CSS), local user styles (CSS from browser extensions or OS) and authored CSS.

![css-cascade-origins.svg](cascade-origins.svg)
**Source**: [web.dev](https://web.dev)

- **Importance**: `!important` is used to make a particular property and value the most specific thing, thus overriding the normal rules of the cascade. It's strongly recommended to never use it unless absolutely necessary.
    - The only way to override this `!important` declaration would be to include another !important declaration on a declaration with the same specificity later in the source order, or one with higher specificity.
    - **Order of importance** from least to most:
        - Normal rules (`font-size`, `color`, ...) -> `animation` -> `!important` -> `transition`

- **Inheritance**: Some CSS properties on child elements are inherited from property values set on parent elements. Example, `color` and `font-family`.
    - Every CSS property accepts these four special universal property values for controlling inheritance:
        - `inherit`: sets the property value to be the same as the parent's value for that property.
        - `initial`: sets the property value applied to a selected element to the initial value of that property.
            - The property will revert to the value that is specified in the CSS standard for that property, which can differ from the default styles applied by the browser's user-agent stylesheet.
            - The initial value should not be confused with the value specified by the browser's style sheet.
        - `unset`: resets the property to its natural value, which means that if the property is naturally inherited it acts like `inherit`, otherwise it acts like `initial`.
        - `revert`: resets the property to its inherited value if it inherits from its parent or to the default value established by the user agent's stylesheet (or by user styles, if any exist). It has limited browser support.
    - The CSS shorthand property `all` can be used to apply one of these inheritance values to (almost) all properties at once.

> [!important]
> The three concepts - **Cascade**, **Specificity**, and **Inheritance** - together control which CSS applies to what element.

## How CSS Works

- The browser loads the HTML (e.g. receives it from the network).
- It converts the HTML into a [[DOM|Document Object Model]]. The [[DOM]] represents the document in the computer's memory.
- The browser then fetches most of the resources that are linked to by the HTML document, such as embedded images and videos etc and linked CSS! JavaScript is handled a bit later on in the process.
- The browser parses the fetched CSS, and sorts the different rules by their selector types into different "buckets", e.g. element, class, ID, and so on. Based on the selectors it finds, it works out which rules should be applied to which nodes in the [[DOM]], and attaches style to them as required (this intermediate step is called a render tree).
- The render tree is laid out in the structure it should appear in after the rules have been applied to it.
- The visual display of the page is shown on the screen (this stage is called painting).

![css-rendering.png](rendering.png)
**Source**: [CSS Process](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps/How_CSS_works/rendering.svg)

## Layout

- _Block direction_ - the direction in which block elements are laid out.
    - It runs vertically in a language which has a horizontal writing mode (e.g. English).
    - It runs horizontally in any language with a vertical writing mode (e.g. Japanese).
- _Inline direction_ the direction in which inline contents run.
- Ways to change CSS layout:
    - `display`
    - `float`
    - `position`
    - Multi-column layout

### The CSS Box Model

> [!note]
> Everything in CSS is a box.

- If a box is a `block`,
    - The box will break onto a new line.
    - The box will extend in the inline direction to fill the space available in its container. In most cases this means that the box will become as wide as its container, filling up 100% of the space available.
    - The `width` and `height` properties are respected.
    - Padding, margin and border will cause other elements to be pushed away from the box.
- If a box is `inline`,
    - The box will not break onto a new line.
    - The `width` and `height` properties will not apply.
    - Vertical padding, margins, and borders will apply but will not cause other inline boxes to move away from the box.
    - Horizontal padding, margins, and borders will apply and will cause other inline boxes to move away from the box.
- If a box is `inline-block`,
    - `width` and `height` will be respected, and
    - `padding`, `margin`, and `border` will cause other elements to be pushed away.
    - It doesn't, however, break onto a new line.
- Block and inline are considered as _outer_ display types, but boxes can have _inner_ display types, which dictates how elements inside that box are laid out (properties like `flex` and `grid`).

- Components of a block box:
    - **Content box**: The area where your content is displayed, which can be sized using properties like `width` and `height`.
    - **Padding box**: The padding sits around the content as white space; its size can be controlled using `padding` and related properties. Overflow scrollbars occupy this space when visible.
    - **Border box**: The border box wraps the content and any padding. Its size and style can be controlled using `border` and related properties.
    - **Margin box**: The margin is the outermost layer, wrapping the content, padding and border as whitespace between this box and other elements. Its size can be controlled using `margin` and related properties.
        - If two vertically adjacent elements both have a set margin and the margins touch, the smaller margin collapses and the larger one remains. This event is only relevant to the vertical direction and it's known as **_margin collapsing_**.
        - `outine` and `box-shadow` occupy this space but don't affect the size of the box.

> [!quote]
> ![box-layout.png](box-layout.png)
>
> **Source**: MDN

- The margin is not counted towards the actual size of the box — it affects the total space that the box will take up on the page, but only the space outside the box. The box's area stops at the border — it does not extend into the margin.
- By default, browsers use the standard box model (`box-sizing: content-box;`). An alternative box model can be turned on using: `box-sizing: border-box;`
    - `content-box` - When dimensions like `width` and `height` are set, they will be applied to the _content box_ - the content area;
        - If `padding` and `border` are set, the values will be added on top of these dimensions to the content box's size.
        - The rendered element will be larger than the specified dimensions.
    - `border-box` - Dimensional values are applied to the _border box_; If `padding` and `border` are then set, they get _pushed in_ and the rendered box size doesn't exceed the set dimensions.

> [!important]
> **Margin Collapsing**: If you have two elements whose margins touch, and both margins are positive, those margins will combine to become one margin, which is the size of the largest individual margin. If one or both margins are negative, the amount of negative value will subtract from the total.
>
> - Read more - [Margin Collapsing - MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

### Display

- Main methods of achieving layouts in CSS make use of the `display` property.
- Everything in the normal flow has a default `display` value: e.g. `<div>` -> `block`, `<a>` -> `inline`.

#### Table

- Before technologies like Flexbox and Grid were available, developers used `table` displays to layout pages. This practice is now not recommended as table layouts are inflexible, very markup heavy, difficult to debug, and semantically wrong.

#### Flexbox

- Flexible Box Layout (Flexbox) designed for one-dimensional layout: either horizontally _or_ vertically.
- When a declaration of `display: flex;` is set on a container element, the initial values for `flex-direction` and `align-items` becomes `row` and `stretch` respectively.
- `flex-flow` serves a shorthand for `flex-direction` and `flex-wrap`.
    - `flex-wrap` sets whether flex items are allowed to wrap onto multiple lines or are forced onto a single line.

##### The Flex Model

![the-flex-model.png](the-flex-model.png)
**Source**: [Mozilla Developer Network](https://developer.mozilla.org/en-US/)

- Parent element with `display` property of `flex` is the _flex container_.
- Children of a flex container affected by `display: flex` property are _flex items_.
- The **main axis** is the direction in which the flex items are laid out in starting from _main start_ to _main end_. This direction is specified by `flex-direction`, and has an initial value of `row`.
- The **cross axis** is the direction perpendicular to the main axis starting from cross start to _cross end_.
- `flex` is a shorthand property for the 3 properties below:
    - The `flex-grow` property takes a unitless number value that determines how much available space along the main axis each flex item takes up relative to other flex items.
    - The `flex-shrink` property comes into play when flex items start overflowing. It specifies how much a flex item will shrink to prevent overflow.
    - The `flex-basis` property sets the minimum size for flex items.

```css
article {
	flex: 1 1 250px;
}
```

- Horizontal and vertical alignment can be accomplished by applying the `align-items` and `justify-content` properties on the flex container.
    - `align-items` controls where flex items sit on the cross axis; it has default value of `stretch`.
        - Individual flex items can override this property using `align-self`.
    - `justify-content` controls where flex items sit on the main axis; it has default value of `flex-start`.
    - `justify-items` is ignored in a flexbox layout.
- The `order` of flex items can be changed without changing their source order. By default this value is `0`. The higher this value, the later the element will appear in the display relative to siblings. The value can be negative as well.

#### Grid

- _Grid Layout_ is designed for two-dimensional layout: both horizontally _and_ vertically.
- A grid typically has columns, rows, and gaps between each row and column, commonly referred to as _gutters_.

![css-grid.png](grid.png)
**Source**: MDN

```html
<div class="parent">
	<div class="child"></div>
	<div class="child"></div>
	<div class="child"></div>
	<div class="child"></div>
	<div class="child"></div>
	<div class="child"></div>
</div>
```

```css
.parent {
	display: grid;
	grid-template-columns: 1fr 1fr 1fr;
	/* grid-template-columns: (3, 1fr) */
	grid-template-rows: 100px 100px;
	gap: 10px;
}
```

![grid-css-layout.png](grid-layout.png)

- `fr` unit is used to
    - distribute space proportionally.
    - achieve flexibility / responsiveness.
- `grid-auto-columns` and `grid-auto-rows` properties specify the size of an implicitly-created grid columns or rows respectively, i.e. when `grid-template-columns` and `grid-template-rows` props are not set.
- `grid-auto-rows: minmax(m, n)` - sets a minimum height of `m` and maximum of `n` for rows.
- Property values can be combined to achieve useful patterns.
    - `grid-template-columns: repeat(auto-fill, minmax(200px, 1fr))`
- Grid items can also be placed using lines. Grid lines are numbered from 1 (left edge of container) to the right-most edge of the container, i.e. `x` columns will have `x+1` lines.
    - `grid-column-start`, `grid-column-end`, `grid-row-start`, and `grid-row-end` properties can be used to achieve this behavior.
    - Shorthand properties, `grid-column` and `grid-row`, can also be used.

```css
header {
	grid-column: 1 / 3;
	grid-row: 1;
}

article {
	grid-column: 2;
	grid-row: 2;
}

aside {
	grid-column: 1;
	grid-row: 2;
}

footer {
	grid-column: 1 / 3;
	grid-row: 3;
}
```

- Alternatively, template areas can be set using `grid-template-areas`.

```css
.container {
	display: grid;
	grid-template-areas:
		"header header"
		"sidebar article"
		"footer footer";
	grid-template-columns: 1fr 3fr;
	gap: 10px;
}

header {
	grid-area: header;
}

article {
	grid-area: article;
}

aside {
	grid-area: sidebar;
}

footer {
	grid-area: footer;
}
```

- Some `grid-template-areas` rules:
    - Areas must be rectangular, e.g. can't be L-shaped.
    - Use `.` to leave a cell empty.

### Floating

> Floating changes the behavior of that element and the block level elements that follow it in normal flow.

- Possible values for `float`: `left`, `right`, `none`, `inherit`
- To stop an element from wrapping around a floated element, `clear` can be used. [^3] [^4]

### Positioning

- Positioning helps manage and fine-tune the position of specific items on a page.
- `position` can be any of:
    - `static` - the default position of every element.
    - `relative` - an element's position relative to its position in normal flow.
    - `absolute` - an element's position relative to the edges of its closest _explicitly positioned_ ancestor (which is `<html>` if no other ancestors are positioned). Margin affects positioned elements.
    - `fixed` - an element's position relative to the browser viewport, not another element; similar to `absolute` positioning.
    - `sticky` - makes an element act like `position: relative` until it hits a defined offset from the viewport, at which point it acts like `position: fixed`; newer positioning method.

### Multi-column layout

- Multi-column layouts (often referred to as _multicol_) provide a way to lay out content in columns, similar to the way newspaper text flows.
- It's the oldest of the layout methods like Flexbox and Grid. Hence, it can be used as a fallback if necessary.
- It makes use of either the `column-count` or the `column-width` property.

```html
<div id="wrapper">
	<h1>Lorem ipsum dolor</h1>
	<p>Lorem ipsum dolor sit amet, consectetur...</p>
	<p>Lorem ipsum dolor sit amet, consectetur...</p>
</div>
```

```css
#wrapper {
	column-count: 3;
	column-gap: 20px;
	column-rule: 4px dotted rgba(0, 0, 0, 0.25);
}
```

![multicol.png](multicol.png)

## Properties

- `background-image`
    - Gradients: `conic-gradient()`, `linear-gradient()`, `repeating-linear-gradient()`, `radial-gradient()`, `repeating-radial-gradient()`
    - `url()`
- `font-size` - standard value across browsers is set to `16px`.
- `object-fit` - defines how the content of a replaced element like `<img>` fits inside its container.
    - `cover` & `fill` fill out the container, the former maintaining the image's aspect ratio and the latter not.
    - `contain` scales down image to fit it inside its container.
- `table-layout`
    - `fixed` - defines the size of columns according to the width of their headings, rather than based on their content.

## Units + Values

- **`em` vs. `rem`**
    - `em` - "my parent's font-size"
    - `rem` - "the root element's font-size"
- When using `margin` and `padding` properties with percentage values, the value is calculated from the _inline size_ of the parent block (i.e. the width in a horizontal language).

> [!tip]
> A common use of `max-width` is to cause images to scale down if there is not enough space to display them at their intrinsic width while making sure they don't become larger than that width. [^2]
>
> `img`s don't stretch / scale up to fill their parent but they do scale down to fit their container accordingly. It makes the images responsive. #rwd

- **Viewport units**: `vh`, `vw`
    - `1vh` = 1% of viewport height.
    - `1vw` = 1% of viewport width.

## Typography

- `text-overflow` specifies how hidden content is shown. It truncates text at the point of overflow.
    - `clip` (default)
    - `ellipsis` - displays an ellipsis (`...`).
- While the CSS `direction` property exists for text direction (`ltr` & `rtl`), the [[HTML]] attribute `dir` is recommended.
- `text-align`
    - `start` and `end` are logical alignments that represent the location of the start and end of a line of text in the current writing mode.
        - e.g. `start` maps to `left` in English, and to `right` in Arabic script which is written right to left (RTL).
- `writing-mode` changes the way text flows: `horizontal-tb` (default), `vertical-lr` or `vertical-rl`.
- **Pseudo-Elements**
    - `::first-letter`
    - `::first-line`
    - `::selection`

### Web fonts

- `@font-face` is used to define custom fonts.
    - The `local()` function can be used to search for a font on the user's device, and potentially reduces the need for an internet connection.

```css
@font-face {
	font-family: "myFont";
	src:
		local("myFont"),
		url("myFont.woff2") format("woff2"),
		url("myFont.woff") format("woff");
	font-weight: normal;
	font-style: normal;
}

html {
	font-family: "myFont", serif;
}
```

- **Variable fonts** allow several variations of a typeface to be incorporated into a single file, rather than having separate font files for every width, weight, or style.

> [!note]
> Only a certain number of fonts are generally available and can be used w/o worry across all systems. These are known as ==web safe fonts==, and most typically support font weights of 400 (normal) and 700 (bold).

## Browser Support

> Feature queries allow you to test whether a browser supports any particular CSS feature.
> They can be combined using logical operators: `and`, `or`, `not`

**Syntax**

```css
@supports (property: value) {
	/* CSS declarations */
}

@supports (...) and (...) {
	/* CSS declarations */
}

@supports (...) or (...) {
	/* CSS declarations */
}
```

**Example**

```css
#app {
	display: flex;
}

/* Use grid if it's supported */
@supports (display: grid) {
	#app {
		display: grid;
	}
}
```

- This can be achieved in [[JavaScript|JS]] using the [[CSSOM]] method `supports()`.

```js
if (!CSS || !CSS.supports("display", "grid")) {
	/* CSS grid not supported */
}
```

## Responsive Web Design (RWD)

> [!abstract]- RWD
> ![[Responsive Web Design]]

## CSS Processors

### Preprocessors

- A CSS preprocessor is a scripting language that extends the basic functionalities of CSS, enabling the use of additional features:
    - Variables
    - Nesting
    - Mixins
    - Functions and Operations
    - Inheritance
- Popular tools: Sass, LESS, Stylus

```scss
// Define variables
$primary-color: #3498db;
$font-stack: "Helvetica Neue", sans-serif;

// Mixin for button styles
@mixin button-styles($bg-color) {
	background-color: $bg-color;
	color: white;
	&:hover {
		opacity: 0.9;
	}
}

// Base styles
body {
	font-family: $font-stack;
	header {
		background-color: $primary-color;
		text-align: center;
		h1 {
			margin: 0;
		}
	}
}

// Button styles using mixin
.button-primary {
	@include button-styles($primary-color);
}

.button-secondary {
	@include button-styles($secondary-color);
}
```

### Postprocessors

- Process standard CSS files to output CSS that is cross-browser compatible.
- PostCSS is a popular tool that relies on plugins to perform specific tasks.
    - Autoprefixing
    - Linting
    - Minification

```js
module.exports = {
	plugins: [require("autoprefixer")],
};
```

```bash
# CLI Command
npx postcss styles.css -o output.css
```

```css
/* styles.css */
.example {
	display: flex;
	transition: all 200ms ease;
}

/* output.css */
.example {
	display: -webkit-box; /* Old versions of Safari */
	display: -ms-flexbox; /* IE10 */
	display: flex;
	-webkit-transition: all 200ms ease;
	transition: all 200ms ease;
}
```

## Methodologies

### Object-Oriented CSS (OOCSS)

- Emphasizes creation of reusable and modular components.
- Styles are defined in a way that they can be applied across different contexts without modification.
- Key principles:
    - _Single Responsibility_ - Each component should have one clear purpose.
    - _Separation of Concerns_ - Styles should be independent of the HTML structure, allowing for greater flexibility and reusability.

```css
.button {
	box-sizing: border-box;
	height: 50px;
	width: 100%;
}

.grey-btn {
	background: #eee;
	border: 1px solid #ddd;
	color: #555;
}
```

### Block Element Modifier (BEM)

- A naming convention that helps developers create reusable components with a clear structure.
- Breaks down the user interface into three parts:
    - **Block**: The top-level component (e.g., `menu`, `button`).
    - **Element**: A part of the block that has no standalone meaning (e.g., `menu__item`).
    - **Modifier**: A flag on a block or element that changes its appearance or behavior (e.g., `button--primary`).

```html
<button class="button button--primary">
	<span class="button__icon">✔️</span>
	<span class="button__text">Submit</span>
</button>
```

```css
.button {
	display: inline-flex;
	align-items: center;
	border: none;
}

.button__icon {
	margin-right: 0.25rem;
}

.button--primary {
	background-color: blue;
	color: white;
}

.button--secondary {
	background-color: gray;
	color: white;
}
```

- Other methodologies: SMACSS, Atomic CSS, and ITCSS.

## Modern CSS

- **Custom Properties**
    - [Using CSS Custom Properties (variables) (MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_cascading_variables/Using_CSS_custom_properties)
- **Container Queries**
    - [CSS Container Queries (MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_containment/Container_queries)
- **Nesting**
    - [Using CSS Nesting (MDN)](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_nesting/Using_CSS_nesting)

## CSS Best Practices

- When placing text on background images and colors, there should be enough contrast for readability. #a11y
- When styling tables,
    - use `table-layout: fixed` to create a predictable layout.
    - use `border-collapse: collapse` to border of table elements collapse into one.
    - use semantic [[HTML]]: `<thead>`, `<tbody>`, `<tfoot>`.
    - use _zebra striping_ of rows or columns to increase readability.
- Reserve underlining for links, but not for other things.

### Organization

- Follow a certain coding style guide.
- Keep things consistent and readable.
- Comment your code: add a block of comment between logical sections of a stylesheet.
- Create logical sections in your stylesheet. For example,
    - Write common/general styles first thing: e.g. `body { /* … */ }`, `h1, h2, h3, h4 { /* … */ }`
    - Write utility classes next: e.g. `.nobullets { list-style: none; }`
    - Write sitewide styles next: e.g. `.main-nav { /* … */ }`
    - Write specific styles later in the stylesheet: e.g. `.product-link { /* … */ }`
- Avoid overly-specific selectors.
- Break down large stylesheets into several small ones.
- Adopt well known and tested methodologies: e.g. BEM, OOCSS, SMACSS, Atomic CSS
- Utilize pre-processors (like Sass) and post-processors (like PostCSS).

### Styling Form Elements

- **Inheritance**: In some browsers, form elements don't inherit font styles by default. Expected behavior can be accomplished using:

```css
select,
button,
input,
textarea {
	font-family: inherit;
	font-size: 100%;
}
```

- **`box-sizing`**: Form elements use different `box-sizing` rules for different elements across browsers.

---

> [!question]- Interview Emphasis Points
>
> > Concepts / sections to focus on when reading
>
> - Specificity
> - Box Model
> - Layout & Display
> - Positioning
> - [[Responsive Web Design|RWD]] - Media Queries
> - Colors - Hex v. RGB v. RGBA
> - Units
> - Psuedo-selectors & Psuedo-elements
> - Advanced
>     - Transitions - Timing Functions & Bezier Curves
>     - Combinators

---

## Further

### Resources 🧩

- [CSS Almanac](https://css-tricks.com/almanac/)

- [Defensive CSS - Tips](https://defensivecss.dev/tips/)

[^1]: [Clearfix - CSS Tricks](https://css-tricks.com/clearfix-a-lesson-in-web-development-evolution/)

[^2]: [Clearfix - MDN](https://developer.mozilla.org/en-US/docs/Learn/CSS/CSS_layout/Floats#clearing_boxes_wrapped_around_a_float)

[^3]: [Sizing items in CSS](https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Sizing_items_in_CSS#min-_and_max-_sizes)

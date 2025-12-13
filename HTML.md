## Introduction

- [[Interpreted Language]]
- Parsed permissively unlike other "real programming languages".

## Fundamentals

- Element: `<tag>Content</tag>`
    - Tags are case-insensitive
- **Inline elements** are contained within block-level elements.
- **Block-level elements** are usually structural elements on a page. They wouldn't be nested inside an inline element.
- **Empty / void / self-closing elements** consist of a single tag, which is typically used to insert/embed something in the document. e.g. `<img>`
- **Attributes** contain extra information about the element that won't appear in the content.
    - Boolean attributes can only have one value, which is generally the same as the attribute name. e.g. `<input type="..." disabled="disabled">`

**Anatomy of an HTML Document**

```html
<!DOCTYPE html>
<html lang="en-US">
	<head>
		<meta charset="utf-8" />
		<title>My test page</title>
	</head>
	<body>
		<p>This is the body of the page</p>
	</body>
</html>
```

- `<head>` - contains non-content related information
- `<meta charset="utf-8">` - specifies character set for the document, in this case to UTF-8.
- `<body>` - contains all the content on the page.

> [!note]
> Primary language of the document can be set here using the `lang` attribute on the root element, but can also be used to set the language for subsections of a page.
>
> ```html
> <span lang="es">Cómo estás</span>
> ```

- Whitespace (including line breaks) used inside an HTML element content is reduced to a single space by the parser.
- Characters like `>`, `<`, `'`, `"` and `&` are part of the HTML syntax, and hence special. To include them in a text in an HTML doc, character references can be used.

> Character references are special codes that represent characters in the above context. They start with an `&` and end with an `;`.

- **Comments**: `<!-- COMMENT -->`

**Metadata**

- `<meta>` is a way of adding metadata to a document. Many `<meta>` elements contain the `name` and `content` attributes, which are used to specify the type of info that element contains and the actual meta content respectively.

```html
<meta name="author" content="oneminch" />
<meta name="description" content="This website is a personal blog." />
```

- Due to abuse by spammers which caused biased search results, many features of `<meta>` are no longer used.

```html
<!-- Ignored by search engines -->
<meta name="keywords" content="list, of, keywords, here" />
```

- Some proprietary forms of metadata are used across the web to provide users with a richer experience.
    - e.g. Facebook's [Open Graph Data](https://ogp.me/) and [Twitter Cards](https://developer.twitter.com/en/docs/twitter-for-websites/cards/overview/abouts-cards) are ways of providing specific pieces of info to be used by their respective platforms to display richer links.

**Icons**

```html
<link rel="icon" href="../path/to/favicon.ico" type="image/x-icon" />
```

- Different resolution icons can be provided to be used under different contexts, like, for instance, if a website is added to an iPad's home screen as a bookmark.

> [!important]
> CSP applies to the favicon and the header's `img-src` directive can prevent access to the icon if expected value is not set.

- [[CSS]] & [[JavaScript]] are commonly applied to a page using `<link>` and `<script>` respectively.

```html
<!-- Should always go inside <head> -->
<link rel="stylesheet" href="../path/to/style.css" />

<!-- Should also go inside <head> using `defer` which 
postpones the execution of the script til HTML is parsed -->
<script src="../path/to/app.js" defer></script>
```

**Structure**

- Content structure / hierarchy is an important part of writing HTML. Giving content some structure makes it:
    - easier to skim for readers.
    - perform better in terms of SEO as search engines consider the contents of headings as important keywords for influencing search rankings.
    - more accessible as it helps screen readers generate useful outlines for the page.

## Semantic HTML

- It is a good idea to use the appropriate HTML element for the job.
    - e.g. Using `<h1>` to indicate a top level heading rather than a styled `<span>` element.
- **Presentational Elements** (like `<b>`, `<i>` and `<u>`) only affect presentation and not semantics. They should no longer be used because, semantics is an important factor for [[Accessibility]], & [[Search Engine Optimization]]. e.g. Consider using `<strong>`, `<em>`, `<mark>`, or `<span>` to indicate emphasis instead of `<b>`, `<i>` and `<u>`.

- Websites can be structured using these semantic elements:
    - **header:** `<header>`; for group of introductory content depending on the context (`<body>`, `<article>` or `<section>`).
    - **navigation bar:** `<nav>`
    - **main content:** `<main>`; for content unique to a page. Content subsections can be represented by:
        - `<article>` - encloses a block of content that stands on its own.
        - `<section>` - for grouping together single pieces of functionality of a page.
        - `<div>`
    - **sidebar:** `<aside>`; often placed inside `<main>`; contains info that's not directly related to the main content. Typically used for auxiliary / additional info.
    - **footer:** `<footer>`;

- Elements like `<div>` and `<span>` are non-semantic; they should be only when there is no semantic alternative.

- To provide a semantic container that links figures with their captions, use `<figure>` and `<figcaption>`.

```html
<figure>
	<img src="../path/to/image" alt="...alternative text..." />

	<figcaption>caption for image</figcaption>
</figure>
```

> [!important]
> A figure is an independent unit of content that doesn't necessarily have to be an image.

## Elements

![periodic-table-of-html-elements.png](/assets/images/html.periodic-table-of-html-elements.png)
**Source**: Josh Duck

### Lists

```html
<ol>
	<li>list item 1</li>
	<li>list item 2</li>
	<li>
		<ul>
			<li>nested list item 1</li>
			<li>nested list item 2</li>
		</ul>
	</li>
</ol>
```

- The `type` attribute is used on `<ol>` to set the numbering type:
    - `1` - numbers (default)
    - `a` - lowercase letters
    - `A` - uppercase letters
    - `i` - lowercase Roman numerals
    - `I` - uppercase Roman numerals

### Hyperlinks

- Elements with an `id` value can be used as navigation points to specific part of an HTML document using hyperlinks; these are known as ==_document fragments_==.

```html
<!-- contacts.html -->
<h3 id="email-address">email@example.com</h3>

<!-- index.html -->
<p>
	Send a message to
	<a href="index.html#email-address">our email address</a>
</p>
```

### Description Lists

```html
<dl>
	<dt>Coffee</dt>
	<dd>The fuel that keeps the world running.</dd>
</dl>
```

### Quotations

**Blockquotes**

```html
<blockquote cite="quote-source-url">
	<p>This is a block quote</p>
</blockquote>
```

**Inline Quotes**

```html
<p>This is <q cite="quote-source-url">an inline quote</q></p>
```

- The `cite` attribute is not useful in terms of [[Accessibility]]. An alternative way is to use the `<cite>` element:

```html
<p>
	According to
	<a href="quote-source-url"><cite>the MDN page on HTML</cite> </a>:
</p>
<blockquote cite="quote-source-url">quote text...</blockquote>
```

### Abbreviations

- `<abbr title="Doctor">Dr.</abbr>`
    - `<acronym>` element was removed in favor of `<abbr>`.

### Contact

- `<address>`, should be used within the context of the nearest `<article>` or `<body>` to include contact details related to that page or its content.

### Time and Dates

```html
<time datetime="2016-01-20T19:30"> 7.30pm, 20 January 2016 </time>
```

- No matter how the information between the tags is presented, using the attribute provides a machine-readable way of providing time information.

### Breaks

- Line break - `<br>`
- Horizontal rule - `<hr>`; denotes a thematic change in the piece of text.

## Images and Multimedia

### `<img>`

- Using `srcset` and `sizes` can be solved to save the _resolution switching_ problem.

```html
<!-- Different sized images -->
<img
	srcset="./path/to/image-480w.jpg 480w, ./path/to/image-800w.jpg 800w"
	sizes="(max-width: 600px) 480px, 800px"
	src="./path/to/image-800w.jpg"
	alt="..."
/>

<!-- Same size images, different resolutions -->
<!-- Image width is set using CSS -->
<img
	srcset="
		./path/to/image-320w.jpg 1x,
		./path/to/image-480w.jpg 1.5x,
		./path/to/image-640w.jpg 2x
	"
	src="./path/to/image-640w.jpg"
	alt="..."
/>
```

- Using `<picture>` solves the _art direction_ problem.

```html
<picture>
	<source media="(max-width: 799px)" srcset="zoomed-img-480w.jpg" />
	<source media="(min-width: 800px)" srcset="wide-img-800w.jpg" />
	<img src="wide-img-800w.jpg" alt="..." />
</picture>
```

> [!note]
> `media` attribute should only be used to make use of art direction.

- The `type` attribute can also be used in `<picture>` to supply MIME types so that browsers can reject unsupported filetypes.

```html
<picture>
	<source type="image/svg+xml" srcset="img.svg" />
	<source type="image/webp" srcset="img.webp" />
	<img src="img.png" alt="..." />
</picture>
```

- Because images are preloaded before any styles or scripts are loaded and interpreted, using the solutions like shown above is a better alternative than using [[CSS]] or [[JavaScript]].

#### Image Types

- **Raster Images** - defined using a grid of pixels, where the file holds information on the location and color of each pixel. e.g. `.bmp`, `.png`, `.jpg` & `.gif`

- **Vector Images** - defined using algorithms where the file contains definitions of a shape and a path. The computer can use this to figure out the image; unlike raster counterparts, they are much lighter and highly scalable. e.g. [[SVG]]
    - **Pros**
        - Text inside vector images is [[Accessibility|accessible]], and [[Search Engine Optimization]]-friendly.
        - When used inline, they save loading time and can be easily styled (using [[CSS]]) and scripted (using [[JavaScript]]).
    - **Cons**
        - They can get complicated; this affects browser performance due to significant processing time.
        - Creating them can be difficult
        - They lack older browser support
            - How to troubleshoot this problem? [^1]
    - Using inline [[SVG | <svg>]],
        - makes for resource-intensive maintenance due to duplication
        - increases HTML file size
        - removes the benefit of browser-caching (unlike with `<img>` loaded [[SVG]])

### `<video>`

```html
<video src="../path/to/video" controls>
	<!-- Fallback text or content -->
</video>
```

> [!info]
> Media elements like `<img>` and `<video>` are sometimes known as _replaced elements_ because their content and size are defined by the external resources they link to and not by the elements themselves.

- To allow support for multiple formats:

```html
<video controls>
	<source src="../path/to/video.webm" type="video/webm" />
	<source src="../path/to/video.mp4" type="video/mp4" />
	<!-- Fallback text or content -->
</video>
```

- Browser goes thru `<source>` elements and play first one that it supports.
- `<video>` supports other features thru attributes like `autoplay`, `loop`, `muted`, `poster` and `preload`.

> [!note]
> Due to patents, browsers pay typically large licensing fees to add support for several popular media formats.

#### Video Transcripts

- To display video text transcripts, [[WebVTT]] file format and the `<track>` element are used.

```html
<!-- Example -->
<track kind="subtitles" src="subtitles_es.vtt" srclang="es" label="Spanish" />
```

- The `<track>` element is placed inside `<video>` after `<source>` elements.
- `kind` can be any one of: `subtitles`, `captions`, `descriptions`.
- `label` is used to help readers look for a language.
- Since search engines make use of text a lot, text tracks help with [[Search Engine Optimization]]; They also allow search engines to link directly to a point in the video.

### `<audio>`

- works just like `<video>` and supports same features.
- `width`, `height` and `poster` aren't supported on this element as it has no visual component.

## Embedding

### `<iframe>`

```html
<iframe src="..." allowfullscreen sandbox>
	<!-- Fallback content -->
</iframe>
```

- To avoid any security risks with `<iframe>`s (like [[clickjacking]]):
    - Only embed when it's necessary.
    - Use HTTPS.
    - Always use the `sandbox` attribute.
    - Configure the appropriate CSP directives.

### `<embed>` and `<object>`

- are used as general purpose embedding tools.
- Although, these are no longer used that often and pose [[Accessibility]] issues.

## Tables

- used to represent tabular data.
- In the past, they were also used to layout a page as [[CSS]] support wasn't good; This is considered a bad idea now because:
    - it reduces [[Accessibility]] for visually impaired users
    - it makes code harder to maintain
    - it isn't automatically responsive
- Table headers (`<th>`)
    - make it easier to find data
    - aid [[Accessibility]] when used along with `scope`
        - `scope` - added to the `<th>` to tell screen readers whether the header is for rows (`row` / `rowgroup`) or columns (`col` / `colgroup`).
        - Alternatively, [`id` and `headers` attributes](https://developer.mozilla.org/en-US/docs/Learn/HTML/Tables/Advanced#the_id_and_headers_attributes) can be used.
- `colspan` and `rowspan` attributes allow table cell and headers to span across multiple columns and rows respectively.
- It's useful to give more complex tables more structure: `<thead>`, `<tfoot>`, `<tbody>`; this aids the layout and styling aspects of the table but doesn't impact [[Accessibility]] in any way nor provides any visual enhancement.
    - `<tfoot>` - always rendered at the bottom of the table even if precedes `<tbody>`.
    - `<tbody>` - always included in every table implicitly even if it's not specified in code.

### Styling

- `<col>` & `<colgroup>` can be used as a styling template to style columns as opposed to styling each cell; but this method is limited to few properties.

```html
<table>
	<caption>
		caption that describes the table
	</caption>

	<colgroup>
		<col />
		<col style="background-color: lightsalmon" />
		<col />
		<col style="background-color: lavender" />
	</colgroup>

	<thead>
		<tr>
			<th scope="colgroup" colspan="2">Data 1</th>
			<th scope="colgroup" colspan="2">Data 2</th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<th scope="rowgroup" rowspan="2">Item</th>
			<td>Item 1</td>
			<th scope="rowgroup" rowspan="2">Item</th>
			<td>Item 3</td>
		</tr>
		<tr>
			<td>Item 2</td>
			<td>Item 4</td>
		</tr>
	</tbody>

	<tfoot>
		<tr>
			<td colspan="2">Item 5</td>
			<td colspan="2">Item 6</td>
		</tr>
	</tfoot>
</table>
<!-- plus some CSS -->
```

![html.tables.png](/assets/images/html.tables.png)

> [!note]
> Providing one `<col>` element in `<colgroup>` implies that styling should be applied to all the columns.

## Web Forms

> [!important]
> Nesting forms is strictly forbidden.

### Elements

- `<label>` - to formally define a label for an HTML form widget.
    - important for [[Accessibility]].
    - clickable.
    - Nesting a form control (e.g. `<input>`) inside a `<label>` implicitly associates it.
    - A `<label>`'s `for` attribute creates an association with the form control element of the same `id` value.
- `<fieldset>` - to semantically create groups of widgets sharing the same purpose; useful for styling.
    - `<legend>` - included inside to provide a label for the group.

```html
<form>
	<fieldset>
		<legend>Legend</legend>
		<!-- Form Controls -->
	</fieldset>
</form>
```

- `<input>` fields share common functionalities thru attributes: `disabled`, `placeholder`, `size`, `readonly`, `spellcheck`.
    - `<input type="radio" />` can be grouped together by giving each one the same vale for the `name` attribute.
- `<select>`
    - If the `value` attribute is omitted, the content of the `<option>` element is used.
    - The `multiple` provides multiple choice from a list.
    - `<option>`s can be grouped in a dropdown using `<optgroup>`:

```html
<select multiple>
	<optgroup label="Gasoline">
		<option value="Audi" selected>Audi</option>
		<option value="BMW">BMW</option>
	</optgroup>
	<optgroup label="Electric">
		<option value="Rivian">Rivian</option>
		<option value="Tesla">Tesla</option>
	</optgroup>
</select>
```

- `<datalist>` is used to provide autocomplete suggestions:

```html
<datalist id="datalist">
	<option>Audi</option>
	<option>BMW</option>
	<option>Rivian</option>
	<option>Tesla</option>
</datalist>

<input type="text" list="datalist" />
```

- `<progress>` and `<meter>` bars represent numeric values visually.

### Attributes

> [!note]
> Every `<button>` element inside a form is a submit button, unless `type='button'` is used on the element. e.g. show/hide password button.
>
> If the submit button has a `formmethod` or `formaction` attributes those values override the `method` and `action` values on the `<form>` element.

- The `name` attribute identifies the data entered in a form control. e.g. `query=html`
- A `<form>` with no `action` attribute sends data to the same page.
- By default, form data is sent as a `GET` request, with the data added to the URL. The data can be accessed from the URL in any context: frontend or backend. e.g. search engine forms.
- If `POST` is used as the method, the data is included in the request body; it can only be accessed using a script. The data will be encrypted if [[HTTP|HTTPS]] is enabled. e.g. Login forms
- `inputmode` provides the type of data that might be entered on a user input element to allow the browser to display the appropriate virtual keyboard. e.g. `inputmode='url'` might display a virtual keyboard with the `/` key in the forefront.
- `enterkeyhint` defines what label (or icon) to display on the enter key on virtual keyboards. For instance, a value of:
    - `enter` -> new line
    - `done` -> collapse keyboard
    - `next` -> go to the next input field
- Sending files is considered a special case; unlike other form data which is text, file data is binary, and [[HTTP]] is a text protocol.
    - The following attributes must be set on the `<form>`:
        - `method` -> `POST`
        - `enctype` -> `multipart/form-data`
- The `autocomplete` attribute can be used to improve user experience and accessibility.
    - It allows the user agent to predict the value based on previously entered or saved values.

### Security

- Security needs to be considered whenever sending data to a server.
    - **_Always use [[HTTPS]]._**
    - **_Always sanitize user data._**
        - Perform backend validations.
        - Escape dangerous characters.
        - Sandbox uploaded files.

### Validation

#### Client-side Validation

> [!note]
> Client-side validation should only be used to improve user experience or provide feedback.

- Form controls provide built-in client-side validation using HTML attributes.
    - e.g. `type`=< `email` | `tel` | `url` >, `min`, `max`.
    - The `pattern` attribute can be used to enforce constraints using RegEx.
        - A `title` attribute can be used in combination with `pattern` to add a custom error message / feedback.
- An alternative to built-in HTML validation is to use [the Constraint Validation API](https://developer.mozilla.org/en-US/docs/Learn/Forms/Form_validation#the_constraint_validation_api) using [[JavaScript]].
    - This API makes certain properties (such as `validity`) and methods (such as `setCustomValidity`) available on certain form elements.
    - It provides a more powerful way of handling constraints, and providing feedback.
        - Among other things, the default error message of an invalid form control can be customized.

```js
const email = document.getElementById("mail");

email.addEventListener("input", (event) => {
	if (email.validity.typeMismatch) {
		email.setCustomValidity("I am expecting an email address!");
	} else {
		email.setCustomValidity("");
	}
});
```

### Styling Form Controls

- The `appearance` property allows control over OS-level styles; defaults can be removed by setting the property to `none`.
    - In some cases, removing / resetting default appearance is necessary to apply custom styles.
- `:valid` / `:invalid` pseudo-classes are used to target form controls with constraints.
    - e.g. `required` controls with no value -> `:invalid`, `<input type="email">` with non-email value -> `:invalid`, controls with no constraint validation -> `:valid`
- `:indeterminate` and `:default` match radios / checkboxes that are neither checked nor unchecked, and ones that are checked by default (on page load), respectively.

### Sending Data

- When sending form data using [[JavaScript]], the `FormData` constructor can be used to get the data from a form element or to build the data, and then to manage its transmission; it's a JS Map of key/value pairs that represent form fields and their respective values.

## HTML Best Practices

- `<main>` should be used for unique content to the page, only once per page.
- Each `<section>` should begin with a heading.
- Always define a language for the current document (using `lang`).
- Text direction should be set in markup using the `dir` attribute instead of the [[CSS]] `direction` property.

```html
<body dir="rtl">
	<!-- Content -->
</body>
```

- Preferably use a single `h1` per page.
- Headings should be used in the correct order.
- Aim to use no more than three headings per page, unless it is necessary.
- Why add alternative text (`alt`) to your images?
    - screen readers #a11y
    - [[Search Engine Optimization]]
    - File or path name might be spelled wrong
    - Support for text-only browsers
    - Images may be turned off to reduce data usage
    - Should be empty for decorative images (if `<img>` is ever used).
- Use [[CSS]] background images for decorative images as these images will have no semantic meaning and are not accessible to screen readers. #a11y
- For images that provide significant info, provide the same info in the main text to be readable by anyone; don't write redundant `alt` text.
    - `alt` text should also be different from caption text. In the absence of an image, both texts are displayed which becomes redundant.
- Use [[CSS]] to alter image size.
- Include `aria-label` attribute for screen readers to read out loud information that's important. #a11y
- Add a `<caption>` to tables to aid all readers but particularly visually-impaired users. #a11y
- It's considered best practice to set the `for` attribute on a `<label>`.
- Always include the meta tag below in the `head` of HTML documents:

```html
<meta name="viewport" content="width=device-width,initial-scale=1" />
```

- Use [[Responsive Web Design]]

### Forms

- Disable the submit button once the form has been submitted in order to prevent multiple requests to the server.

### Hyperlinks

- It's a good idea to use keywords in link text to describe what the link points to and to define the context; Good for [[Accessibility]], readability and [[Search Engine Optimization]].
- Don't be redundant: don't use the [[URL]] as part of the link text and don't use phrases like "links to" or "click here" in the link text.
- Link text should be kept short.
- For image links, provide accessible text: inside `<a>` or inside the image's `alt`. #a11y
- Reduce the number of instances where the same link text is used for different links. e.g. "click here"
- Leave clear signposts in link text when linking to non-HTML resources. e.g.

```html
<a href="link-to-video-stream"> Watch video (opens in new tab) </a>

<a href="link-to-download-item" download="default-save-filename">
	Download report (PDF, 5MB)
</a>
```

- To improve speed, it's considered a good practice to set an `<iframe>`'s `src` attribute using [[JavaScript]] after main content has finished loading. This indirectly impacts [[Search Engine Optimization]].

### Security

- To avoid any security issues with `<iframe>`s (like clickjacking):
    - Only embed when it's necessary.
    - Use [[HTTP|HTTPS]].
    - Always use the `sandbox` attribute.
    - Configure the appropriate CSP directives.

---

> [!question]- Interview Emphasis Points
>
> > Concepts / sections to focus on when reading
>
> - Semantic HTML
> - [[Accessibility|A11y]] (ARIA)
> - DOM traversal & manipulation
> - Form validation & submission

---

[^1]: [Troubleshooting and cross-browser support - MDN](https://developer.mozilla.org/en-US/docs/Learn/HTML/Multimedia_and_embedding/Adding_vector_graphics_to_the_Web#troubleshooting_and_cross-browser_support)

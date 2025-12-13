---
alias: WPO
---

> [!quote] MDN
> Web performance refers to how quickly site content **loads** and **renders** in a web browser, and how well it responds to user interaction.
>
> It is the objective measurement and perceived user experience of a website or application.

## Why

- Good web performance is important for accessibility, and correlates powerfully with the overall effectiveness of the website.
- Improving web performance by reducing download and render times of a site leads to better conversion rates and user retention.
- Building web apps with performance in mind is an integral part of the user experience.

## Optimization

- The major areas of performance optimization:
    - **Reducing overall load time**: correlates with latency, file size, number of files and other factors
        - Reducing file size and the number of HTTP requests, and utilizing preloading can be a good general strategy.
    - **Making sites usable as soon as possible**: involves prioritizing parts of the website so that users can interact with content quickly, and loading other assets in the background or when they are needed (lazy loading).
        - This aspect of performance is measured by the Time To Interactive (TII) metric.
    - **Smoothness & interactivity**: utilizing best practices to give smooth animations/transitions a smooth feel improves perceived performance.
    - **Perceived performance**: matters as much, or perhaps more than, any objective measurement.
        - Providing a quick response / feedback and regular status updates (e.g. via a loading animation) improves perceived performance.
        - Tips for improving perceived performance:
            - Minimizing initial load
            - Reducing content reflows
            - Using progressive enhancement
            - Avoiding font file delays
        - Some metrics can be helpful indicators of perceived performance:
            - First paint
            - First Contentful Paint (FCP)
            - First Meaningful Paint (FMP)
            - Largest Contentful Paint (LCP)
            - Speed index
            - Time to interactive
    - **Performance measurements**: include both actual (objectively measured) and perceived (subjectively experienced) speeds.
        - Tools that can help measure and improve performance:
            - **PageSpeed Insights / WebPageTest** - websites for getting useful performance reports
            - **Network & Performance Monitor** (Firefox) - browser built-in tools
            - **Performance API** - to build custom tools

## Multimedia

- For the average website, multimedia accounts for 75% of its bandwidth.
- It's important to consider data usage, and device memory usage of users.
    - Downloaded images are stored in device memory.

### Images

- [[Lazy Loading]] images defers the loading of images until they are needed, specifically when they enter the user's viewport, and can greatly improve performance.
    - Can help decrease in the overall page size, thus improving loading times, and reduce data transfer expenses by not loading images that users may never see.
- Native lazy loading can be implemented using the `loading` attribute of `<img>`.

```html
<img src="image.jpg" loading="lazy" alt="Description" />
```

- For more control, developers can use the Intersection Observer API. This allows for lazy loading images based on their visibility in the viewport.

```js
const images = document.querySelectorAll("img[data-src]");
const options = {
	root: null,
	rootMargin: "0px",
	threshold: 0.1,
};

const imageObserver = new IntersectionObserver((entries, observer) => {
	entries.forEach((entry) => {
		if (entry.isIntersecting) {
			const img = entry.target;
			img.src = img.dataset.src; // Set the actual image source
			observer.unobserve(img); // Stop observing the image
		}
	});
}, options);

images.forEach((image) => {
	imageObserver.observe(image);
});
```

- Other strategies for optimizing images:
    - **Using an optimal format**
    - **Compression**
    - **Serving optimal sizes**
        - Serving lower resolution images for smaller screens and vice versa.
- Images added using `<img>` have a higher loading priority than ones using CSS `background-image`. So, it's important to use `<img>` / `<picture>` for content images.
    - Loading priority can also be controlled using the experimental `fetchPriority` attribute.
        - An example use case can be a carousel where the first image has higher priority than subsequent ones.
- As part of a good UX design, it's important to consider reflow / layout shifts when loading multimedia.
    - Setting the `width` and `height` attributes on an image can reserve space in the layout to avoid shifts when the image finally loads.
    - Once the image has loaded, the image's intrinsic aspect ratio is used, instead of the aspect ratio from the attributes. Even if the attribute dimensions are not accurate, the image is displayed in its correct aspect ratio.
- For `background-image` images, it's important to add a `background-color` value so if there is any overlaid content, it's still readable before the image has loaded.
- Use placeholders such as a low-resolution version or a solid color while the actual image loads.
    - This improves perceived performance and UX.
- Prioritize above-the-fold images.
    - Avoid lazy loading images that are visible in the initial viewport to enhance the first impression of the site.

### Video

- To better optimize video delivery:
    - Compress all videos.
    - Prioritize `<source>` order - from smallest to largest.
    - Ensure `loop`ing background videos have `autoplay` set.
        - Additionally, set `muted` for mobile and `playsinline` for Safari.
    - Remove audio for video that doesn't need audio (e.g. looping hero videos).
        - Audio consumes bandwidth. Remove it if it's not needed.
    - Control preloading using `preload`.
    - Consider streaming.

## Performance Optimizations

![Rendering Process](rendering.png)

### HTML

- Use `srcset` to provide different resolutions for images.
- Use `<picture>` to provide different sources for multimedia.
- Lazy load images: `loading="lazy"`.
- `<iframe>`s are expensive. Use them sparingly.
    - Utilize `loading="lazy"` similar to images.
- Preload high-priority resources using `<link>` with `rel="preload"`.

### CSS

- Remove unnecessary styles.
- Preload critical assets such as CSS files, fonts and media.
- Keep style modular to optimize for render blocking.
    - Split CSS into separate modules and use the `media` attribute to load only what is needed.

```html
<link rel="stylesheet" href="main.css" />

<link rel="stylesheet" href="print.css" media="print" />

<link
	rel="stylesheet"
	href="mobile.css"
	media="screen and (max-width: 480px)"
/>
```

- Minify and compress CSS files.
- Avoid writing super-specific and complex selectors.
- Utilize CSS sprites.
- Reduce unnecessary animations.
    - If needed, use CSS animations.
        - Performance can be further improved by animating on the GPU.
    - Avoid using properties that, when animated, trigger a reflow (and therefore a repaint).
- Pre-optimize element changes using the `will-change` CSS property.

### JavaScript

- Write less JavaScript.
    - Consider not using a framework for fairly static experiences.
- Clean up unused code.
- Split code into modules and load each part on demand.
- Take advantage of built-in browser features.
    - e.g. Form validation
- Minify and compress files.
- For critical JS, use `<link>` with `rel="preload"` or `rel="modulepreload"` for modules to ensure JS is fetched without blocking rendering.

```html
<head>
	...
	<link rel="preload" href="important-js.js" as="script" />
	<link rel="modulepreload" href="important-module.js" />
	...
</head>

<!-- Can be includeded wherever in the page -->
<script src="important-js.js"></script>
```

- For non-critical JS, consider deferring its loading / execution using `async` and `defer`.
- For essential animations, use CSS over JS.
- To optimize event performance, remove event listeners that are no longer needed.
    - The less event listeners there are to keep track of, the better the performance.
- Minimize [[DOM]] manipulation.
    - For better performance, batch DOM changes together.
- Remove unnecessary elements from the UI (the DOM tree).
- Optimize loops.
- Take advantage of async JS, WebGPU and web workers to run code off the main thread.

### Fonts

> [!important]
> A font is only loaded when it is applied to an element using `font-family`.

- Using strategies like `font-display: swap` can reduce layout shifts by ensuring fallback fonts are used until the desired font is fully loaded.
- Preload essential fonts.
    - Using `<link rel="preload">` can help load critical fonts earlier in the page load process.
    - Use `rel="preconnect"` to make an early connection to an external font provider.
- Load only the glyphs needed using the `unicode-range` `@font-face` descriptor.
- Self-hosting fonts can reduce latency caused by external requests.
    - By including the `@font-face` declaration directly in [[CSS]], the loading process can be streamlined and the overhead associated with third-party font services eliminated.
- Utilizing modern formats like WOFF2 provides better compression and faster loading times compared to older formats like TTF or OTF.
- Font subsetting involves creating a custom font file that includes only the characters used on your website.
    - This can drastically reduce file size and improve loading times, as the browser does not have to download unnecessary glyphs.
- Since fonts are typically static resources that don’t change often, implementing a robust caching strategy improves performance on subsequent visits

## Resource Optimization

#### Preload

- Resource prioritization technique similar to `async` and `defer`.
- Instructs the browser to load critical resources before they are needed for the initial rendering of the current page.
- Particularly useful for resources such as CSS, JavaScript, and images that are essential for displaying content quickly.
- Ensures that resources are cached in the browser.
- Overuse can lead to bandwidth issues and unnecessary consumption of resources.
- Applied using the `<link rel="preload">` tag in the HTML `<head>`.

```html
<link rel="preload" href="styles.css" as="style" />
<link rel="preload" href="main.js" as="script" />
```

#### Prefetch

- Allows the browser to load resources that are anticipated to be needed in the near future, typically for subsequent pages.
- Done using the `<link rel="prefetch">` tag.
- Operates at a lower priority than preloading.
    - It will not interfere with the loading of critical resources for the current page.
- Should be used for resources that are genuinely expected to be needed.
    - Unnecessary prefetching can waste bandwidth and resources.

```html
<link rel="prefetch" href="next-page.js" as="script" />
```

#### Preconnect

- Allows the browser to establish early connections to important third-party origins.
- Performs a DNS lookup, establishes a TCP handshake, and, for https, sets up a secure connection in advance.
    - This reduces latency when the actual request is made.
- Should be used sparingly only to preconnect to critical domains.

```html
<link rel="preconnect" href="https://fonts.example.com" />
```

## Core Web Vitals

- **Largest Contentful Paint (LCP)**
    - Measures the render time of the largest content element visible within the viewport.
    - This could be an image, video, text block, or any other element that takes up a significant portion of the screen.
    - A good LCP score is considered to be 2.5 seconds or less.

- **Cumulative Layout Shift (CLS)**
    - Analyzes the stability of the page layout and how much the content shifts unexpectedly during the page load.
    - Calculates the sum of all individual layout shift scores that occur during the entire page load.
    - A good CLS score is considered to be 0.1 or less

- **Interaction to Next Paint (INP)**
    - Measures the delay between a user's action (like a click or touch) and the browser's response.
    - Introduced as a replacement for FID
    - Google recommends an INP of 100 milliseconds or less.

### Other Metrics

- **First Input Delay (FID)**:
    - Measures the time it takes for a page to respond to the first user interaction, such as a click or tap.
    - Captures the delay between the user's action and the browser's response to that action.
    - A good FID score is considered to be 100 milliseconds or less.
- **Time to First Byte (TTFB)**:
    - Measures the time it takes for the browser to receive the first byte of the response from the server after making an HTTP request.
    - Provides an indication of the server's responsiveness and the efficiency of the backend infrastructure.
    - A lower TTFB value generally indicates better server performance.
- **First Contentful Paint (FCP)**:
    - Measures the time it takes for the browser to render the first piece of content from the DOM, such as text, image, or SVG.
    - Represents the point at which the user starts seeing content on the screen.
    - A good FCP score is typically considered to be within 1 to 2 seconds.
- **Speed Index**:
    - Calculates the average time at which visible parts of the page are displayed during the loading process.
    - Provides a single metric that represents the visual progression of page load.
    - A lower Speed Index value indicates faster rendering and better user perception of speed.
- **Time to Interactive (TTI)**:
    - Measures the time it takes for a page to become fully interactive and capable of responding to user input reliably.
    - Considers factors such as network activity, DOM changes, and event handlers.
    - A good TTI score means the page is ready for user interaction without delays or jankiness.
- **Total Blocking Time (TBT)**:
    - Measures the total amount of time that a page is blocked from responding to user input, such as clicks or scrolls.
    - Captures the duration between First Contentful Paint (FCP) and Time to Interactive (TTI).
    - A lower TBT value indicates a more responsive and usable page.
- **Page load time**:
    - Measures the total time it takes for a web page to load and display all its content, including images, scripts, and stylesheets.
    - Provides an overall indication of the page's loading performance.
    - A faster page load time generally leads to a better user experience.
- **Bounce rate**:
    - Indicates the percentage of visitors who leave a website after viewing only one page, without interacting further with the site.
    - A high bounce rate may suggest that users are not finding what they are looking for or are dissatisfied with the website's performance or content.

## CDNs

- Benefits:
    - **Reduced Latency**:
        - Minimize the distance data must travel by serving content from the nearest edge server to the user.
    - **Caching**
        - Reduce the load on the origin server by storing cached versions of content on edge servers.
    - **Load Balancing**
        - Distribute incoming traffic across multiple servers, preventing bottlenecks during peak usage times.
    - **Improved Scalability**
        - Allow websites to handle sudden spikes in traffic without performance degradation.
    - **Enhanced Security**
        - Possibly offer built-in security features such as DDoS protection and SSL/TLS encryption.
    - **SEO Advantages**
        - Faster loading times positively impact search engine rankings, as search engines prioritize quick-loading websites.

## Best Practices

- Optimize the [[Critical Rendering Path]].

### Common Mistakes

- Poor Image Optimization
    - Using appropriate formats, compressing them, and ensuring they are sized correctly helps mitigate this issue.
- Third-Party Script Overuse
- Not Utilizing Browser Caching
- Not Compressing Static Assets
    - Implementing Gzip compression can reduce file sizes significantly, improving transfer speeds.
- Ignoring Critical CSS
    - Loading essential styles for above-the-fold content first can enhance perceived performance and UX.
- Improper Font Loading Strategy
    - Using strategies like `font-display: swap` can reduce layout shifts by ensuring fallback fonts are used until the desired font is fully loaded.
- Not Leveraging a Content Delivery Network (CDN)
    - CDNs can reduce latency and speed up access.
- Lack of Performance Measurement
- Slow Server Response Times
    - It's crucial to ensure that the server is adequately resourced and optimized to handle requests efficiently.
- Failing to Manage Dependencies
    - Avoid unnecessary addition of dependencies (e.g. frameworks) for minor features, which can bloat the website.

---

## Further

### Reads 📄

- [Why does speed matter?](https://web.dev/why-speed-matters/)

- [Getting started with Web Performance 🚀 (HTMHell)](https://www.htmhell.dev/adventcalendar/2023/14/)

- [Psychology of Speed: A Guide to Perceived Performance](https://calibreapp.com/blog/perceived-performance)

### Resources 🧩

- [Web Performance Guide (SpeedCurve)](https://www.speedcurve.com/web-performance-guide/)

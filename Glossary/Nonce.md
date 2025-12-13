- Nonce (number used once) is a random, unique value generated on the server for each request.
- It is used to mark specific inline scripts or styles as trusted.
- It is dynamic and change with each request, while a [[Hashes|hash]] is static for a given script content.
- It requires server-side logic to generate and include in both the CSP and HTML.
- The browser will only execute inline scripts that have a matching nonce attribute.

```js
// server.js
const crypto = require("crypto");
const nonce = crypto.randomBytes(16).toString("base64");

/* ... */
res.setHeader(
	"Content-Security-Policy",
	`script-src 'nonce-${nonce}' 'strict-dynamic'; object-src 'none'; base-uri 'none';`,
);
```

```html
<script nonce="<%= nonce %>">
	console.log("This inline script is allowed to execute");
</script>
```

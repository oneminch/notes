- In the context of CSP, hashes allow specific inline scripts or styles to execute securely.
- When specified in CSP headers, the browser only executes inline scripts whose content matches the hash.
- The browser calculates the hash of each inline script it encounters and compares it to the allowed hashes in the CSP.
- If there's a match, the script is executed. Otherwise, it's blocked.
- Any change to the script content, even a single character, will change the hash and require updating the CSP. Therefore, hashes are not suitable for dynamic content that changes frequently.
- Best practices:
    - Use hashes for small, static inline scripts that don't change often.
    - Use [[Nonce|nonces]] for dynamic content or when there are many inline scripts.
    - Combine hashes with nonces for flexibility.

```js
// server.js
const crypto = require("crypto");
const script = "console.log('Hello, CSP!');";
const hash = crypto.createHash("sha256").update(script).digest("base64");

/* ... */
res.setHeader(
	"Content-Security-Policy",
	`script-src 'sha256-${hash}' 'strict-dynamic'; object-src 'none'; base-uri 'none';`,
);
```

```html
<script>
	console.log("Hello, CSP!");
</script>
```

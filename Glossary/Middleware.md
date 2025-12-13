- Software that acts as a bridge between different applications, systems, or components.
- In web development, middleware is often used to intercept and process [[HTTP]] requests and responses.
    - Can be used to examine and modify incoming HTTP requests before they reach a certain application logic.
        - e.g. Parse cookies, validate [[Auth]] tokens, logging
    - Can alter outgoing HTTP responses.
        - e.g. Adding security headers, compressing data, formatting JSON responses

```javascript
// Log Incoming Requests:
app.use((req, res, next) => {
	console.log(`Request received: ${req.method} ${req.url}`);
	next();
});

// Authenticate users:
app.use((req, res, next) => {
	if (req.headers.authorization) {
		// Verify token
		next();
	} else {
		res.status(401).send("Unauthorized");
	}
});
```

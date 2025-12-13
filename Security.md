---
alias: Web Security
---

## Cross-Origin Resource Sharing (CORS)

- CORS is a crucial security mechanism that allows web servers to specify which origins can access their resources.
- Implemented by browsers to relax the Same-Origin Policy in a controlled manner.
    - It's not a server-side restriction, but a client-side one enforced by browsers.
- Implemented through HTTP headers.
    - When a browser makes a cross-origin request, it expects specific CORS headers in the response (e.g., Access-Control-Allow-Origin).
    - If these headers are missing or incorrect, the browser blocks the request, even if the request succeeds at the network level and the server successfully processed it.
- When a web app from one origin tries to access resources from a different origin, the browser sends a preflight request to check if the server allows such requests.
- Best Practices:
    - Always specify allowed origins rather than using a wildcard (`*`) in production.
    - Store allowed origins in environment variables for easy configuration across different environments.
    - When using credentials, ensure cookies are secure and use `SameSite` attributes.
    - Only allow the necessary HTTP methods and headers.
    - Implement proper error handling for CORS-related issues on both client and server sides.
- Common CORS Headers:
    - `Access-Control-Allow-Origin` - Specifies which origins can access the resource.
    - `Access-Control-Allow-Methods` - Specifies the allowed HTTP methods.
    - `Access-Control-Allow-Headers` - Indicates which headers can be used in the actual request.
    - `Access-Control-Allow-Credentials` - Indicates whether the response can be shared when the credentials flag is true.

```javascript
app.use((req, res, next) => {
	res.header("Access-Control-Allow-Origin", "http://example.com");
	res.header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE");
	res.header("Access-Control-Allow-Headers", "Content-Type, Authorization");
	res.header("Access-Control-Allow-Credentials", true);
	next();
});
```

```javascript
// Express setup using the `cors()` middleware
const express = require("express");
const cors = require("cors");
const app = express();

app.use(
	cors({
		origin: "http://example.com",
	}),
);

app.get("/api/data", (req, res) => {
	res.json({ message: "This is accessible from http://example.com" });
});
```

- **Same-Origin Policy**
    - Browsers enforce security policies based on the headers they receive from the server.
    - They restrict how a document or script from one origin can interact with resources from another origin, which prevents malicious sites from accessing sensitive data on other sites.
    - CORS allows servers to relax this restriction for specific requests.
    - When making requests from one server to another, the Same-Origin Policy and CORS don't apply.
        - Servers can freely make requests to any other server without these restrictions.

## Content Security Policy (CSP)

- Allows you to specify which sources of content browsers should allow to load.
- Helps prevent XSS and other injection attacks.
- Implemented through [[HTTP]] headers.
- Typically handled at the server level, so there are challenges with SPAs.
    - Libraries like [[React]] often use inline scripts for performance.
        - Use [[Server-Side Rendering|SSR]] to inject [[nonce|nonces]] for inline scripts.
        - Use a build system that calculates hashes for inline scripts and styles.
    - Because of CSS-in-JS tools and external resources as well, CSP might need to be adjusted.
- Best practices:
    - Start with a strict policy and relax as needed.
    - Use `'strict-dynamic'` for script sources when possible.
    - Avoid `'unsafe-inline'` and `'unsafe-eval'` in production.

```js
const app = express();

app.use((req, res, next) => {
	res.setHeader(
		"Content-Security-Policy",
		"default-src 'self'; " +
			"script-src 'self' https://trusted-cdn.com; " +
			"style-src 'self' https://trusted-cdn.com; " +
			"img-src 'self' data: https://images.example.com;",
	);
	next();
});

app.get("/", (req, res) => {
	res.send(`
        <!DOCTYPE html>
        <html>
            <head>
                <title>CSP Example</title>
                <script src="https://trusted-cdn.com/script.js"></script>
                <link rel="stylesheet" href="https://trusted-cdn.com/styles.css">
            </head>
            <body>
                <h1>Hello, CSP!</h1>
                <img src="https://images.example.com/picture.jpg" alt="Example Image">
                <script>
                    console.log('This inline script is allowed by CSP');
                </script>
            </body>
        </html>
    `);
});
```

## HTTP Strict Transport Security (HSTS)

- A web security policy mechanism that helps protect websites against protocol downgrade attacks and cookie hijacking.
- Tells web browsers to only interact with a website using secure [[HTTP#HTTPS|HTTPS]] connections, never [[HTTP]].
- Implemented through an HTTP response header.

```js
const app = express();

app.use((req, res, next) => {
	if (req.secure) {
		res.setHeader(
			"Strict-Transport-Security",
			"max-age=31536000; includeSubDomains; preload",
		);
	} else {
		return res.redirect(301, `https://${req.headers.host}${req.url}`);
	}
	next();
});

// Your routes
app.get("/", (req, res) => {
	res.send("Hello, secure world!");
});

const credentials = {
	key: fs.readFileSync("path/to/your/private-key.pem", "utf8"),
	cert: fs.readFileSync("path/to/your/certificate.pem", "utf8"),
};

const httpsServer = https.createServer(credentials, app);

httpsServer.listen(443, () => {
	console.log("HTTPS Server running on port 443");
});

// Create HTTP server (for redirection)
const httpServer = http.createServer(app);
httpServer.listen(80, () => {
	console.log("HTTP Server running on port 80");
});
```

## Permissions-Policy

- Allows website administrators to control which browser features and APIs can be used by a page and its embedded content.
- Operates on the principle that embedded content should not automatically have access to powerful web features, while granting such access to top-level documents.

> [!example]
>
> > Ensure only the main site and a trusted payment processor can access payment information:
>
> ```
> Permissions-Policy: payment=(self "https://secure-payments.example.com")
> ```
>
> > Disable geolocation for all third-party content (embedded ads or social media widgets can't access user location):
>
> ```
> Permissions-Policy: geolocation=(self)
> ```

```js
const app = express();

app.use((req, res, next) => {
	res.setHeader(
		"Permissions-Policy",
		'camera=(self), payment=(self "https://secure-payments.com")',
	);
	next();
});

app.get("/", (req, res) => {
	res.send("Hello! Check response headers for Permissions Policy.");
});
```

## Common Vulnerabilities

### Cross-Site Scripting (XSS)

- Occur when malicious scripts are injected into trusted websites.
- Allows attackers to inject malicious scripts into web pages viewed by other users.
- DOM-based XSS occurs when the client-side script writes user input to the DOM in an unsafe way.
    - Particularly relevant in React applications with `dangerouslySetInnerHTML` method.
- To prevent XSS:
    - Always validate and sanitize user input before storing or rendering it.
    - When rendering user-generated content, always encode it properly.
    - Implement a Content Security Policy to restrict the sources of content that can be loaded and executed on your site.
    - Set the `HttpOnly` and `Secure` flags on cookies to prevent client-side access and ensure they're only sent over HTTPS.

> [!example]
> If someone submits this into a form (`<img src onerror="alert('HACKED!')" />`) and the input isn't encoded properly (`?query=<img+src+onerror%3D"alert%28%27Hacked!%27%29">`), navigating to the URL from somewhere else can execute the injected script.

### SQL Injection

- Involves inserting malicious SQL code into application queries.
- To mitigate:
    - Use parameterized queries or prepared statements
    - Validate and sanitize user inputs
    - Implement least privilege database access

### Cross-Site Request Forgery (CSRF)

- Allows attackers to trick users into performing unwanted actions on a web application where they're authenticated.
- Primarily concerned with state-changing requests (like POST, PUT, DELETE).
- Bypasses the Same-Origin Policy (SOP) because SOP primarily restricts cross-origin reads, not cross-origin writes.
- Attack mechanism:
    - User logs into a legitimate website (e.g., a bank).
    - User's session is maintained through cookies.
    - Without logging out, the user visits a malicious website.
    - Malicious site tricks the user's browser into sending a request to the legitimate site.
    - The legitimate site processes the request, thinking it came from the authenticated user.
- Prevent by:
    - Using anti-CSRF tokens in forms.
        - Always use HTTPS to prevent token theft through man-in-the-middle attacks.
        - Regularly rotate CSRF tokens, especially after user authentication.
    - Set the `SameSite` attribute on cookies.
        - `Strict` offers robust defense against CSRF.
    - Verifying origin and referrer headers for non-GET requests.
        - It's important to ensure that the application doesn't use GET requests for modifying resources.
    - Implement proper session management, including secure session IDs and timeouts.
    - Implement proper error handling to avoid leaking sensitive information.

> [!note]

```js
const app = express();
app.use(cookieParser());
app.use(express.json());

const generateCsrfToken = () => {
	return crypto.randomBytes(32).toString("hex");
};

app.use((req, res, next) => {
	if (!req.cookies.csrfToken) {
		const token = generateCsrfToken();
		res.cookie("csrfToken", token, { httpOnly: true, secure: true });
	}
	next();
});

app.get("/getCSRFToken", (req, res) => {
	res.json({ CSRFToken: req.cookies.csrfToken });
});

const validateCsrfToken = (req, res, next) => {
	const tokenFromHeader = req.headers["x-csrf-token"];
	const tokenFromCookie = req.cookies.csrfToken;

	if (!tokenFromHeader || tokenFromHeader !== tokenFromCookie) {
		return res.status(403).json({ error: "Invalid CSRF token" });
	}
	next();
};

app.post("/api/data", validateCsrfToken, (req, res) => {
	res.send("Data received securely.");
});
```

```jsx
export const MyComponent = () => {
	const [csrfToken, setCsrfToken] = useState("");

	useEffect(() => {
		const fetchCsrfToken = async () => {
			const response = await axios.get("/getCSRFToken", {
				withCredentials: true,
			});
			setCsrfToken(response.data.CSRFToken);
		};
		fetchCsrfToken();

		/* OR */

		const parts = `; ${document.cookie}`.split("; csrfToken=");

		if (parts.length === 2) {
			setCsrfToken(parts.pop().split(";").shift());
		}
	}, []);

	const handleSubmit = async (event) => {
		event.preventDefault();

		try {
			await axios.post("/api/data", "Some Data", {
				headers: {
					"X-CSRF-Token": csrfToken,
				},
				withCredentials: true,
			});
			console.log("Success!");
		} catch (error) {
			console.error("Error!", error);
		}
	};

	return (
		<form onSubmit={handleSubmit}>
			<input type="hidden" name="_csrf" value={csrfToken} />
			<button type="submit">Submit</button>
		</form>
	);
};
```

### Clickjacking

- An attacker tricks a user into clicking on something different from what the user perceives, potentially leading to unintended actions.
- Attack mechanism:
    - The attacker typically overlays a transparent `<iframe>` containing a target website over a decoy website.
    - The user thinks they're interacting with the decoy site, but they're actually clicking on elements of the target site.
- Prevention
    - The primary defense against clickjacking is the `X-Frame-Options` HTTP header.
        - `DENY`: The page cannot be displayed in a frame.
        - `SAMEORIGIN`: The page can only be displayed in a frame on the same origin as the page itself.
        - `ALLOW-FROM uri`: The page can only be displayed in a frame on the specified origin.
    - Use CSP header on your server.
    - Use HTTPS to prevent man-in-the-middle attacks that could inject iframes.
    - Be cautious when allowing your site to be framed by third-party sites.
    - Only embed when it's necessary.
    - Use [[HTTP|HTTPS]].
    - Always use the `sandbox` attribute to restrict an `iframe`'s capabilities.

## OWASP

- Open Web Application Security Project (OWASP)
- An open-source project that focuses on improving the security of software.
- Provides resources, tools, and guidelines to help developers build secure applications.
- [The OWASP Top Ten](https://owasp.org/www-project-top-ten/) is a widely recognized list of the most critical security risks to web applications.

## Best Practices

> [!important]
> Never trust data from the browser.

- Always use HTTPS to encrypt data in transit between clients and servers.
    - Enable HTTP Strict Transport Security (HSTS) to force HTTPS connections.
- Implement Strong Authentication
    - Enforce strong password policies
    - Use multi-factor authentication
    - Implement account lockouts after failed attempts
- Practice Secure Session Management
    - Generate strong, random session IDs
    - Set secure and `HttpOnly` flags on cookies
    - Implement proper session timeouts and invalidation
- Apply the Principle of Least Privilege
    - Only grant the minimum permissions necessary for users and processes to function.
- Regularly Update Dependencies using tools like `npm audit`
- Implement proper logging and monitoring to detect and respond to security incidents. (e.g. `winston` in [[Node]])

### Server-Side Security

- Keep Software Updated
    - Regularly update all software, including the web server, application frameworks, and libraries to patch known vulnerabilities.
- Secure Server Configuration
    - Configure web servers to use HTTPS and HTTP Strict Transport Security (HSTS).
    - Disable unnecessary services and modules
    - Use a web application firewall (WAF)
    - Implement proper error handling and logging
- Input Validation and Sanitization
    - Always validate and sanitize all user inputs on the server-side, even if client-side validation is in place.
- API Security
    - Implement proper authentication and authorization for all API endpoints
    - Use rate limiting to prevent abuse and DDoS attacks
    - Validate and sanitize all input data
    - Implement proper error handling to avoid information leakage
- Secure File Uploads
    - Validate file types and sizes
    - Scan uploaded files for malware
    - Store uploaded files outside the web root
    - Use randomized filenames to prevent unauthorized access

### Database Security

- Encrypt sensitive data at rest and in transit
- Use strong encryption algorithms and key management practices
- Implement proper access controls for encrypted data
- Use prepared statements to prevent SQL injection
- Implement least privilege access for database users
- Regularly audit and monitor database access

---

## Further

### Books 📚

- Grokking Web Application Security (Malcolm McDonald) ⭐

- Web Application Security (Andrew Hoffman) ⭐

### Reads 📄

- [Defend Your Cookies with Essential Web Security Tactics](https://maggieappleton.com/websecurity)

- [OWASP Top Ten](https://owasp.org/www-project-top-ten/)

### Resources 🧩

- [OWASP Cheat Sheet Series](https://cheatsheetseries.owasp.org/index.html) ⭐

### Videos 🎥

![Your App Is NOT Secure If You Don’t Use CSRF Tokens - YouTube](https://www.youtube.com/watch?v=80S8h5hEwTY)

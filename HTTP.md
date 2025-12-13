---
aliases:
    - HyperText Transfer Protocol
    - HTTPS
---

- HTTP is a technique of transmitting data in a particular format between a server (destination) and a client (source).
- Considered stateless i.e. once a request and response has completed, the server doesn't retain any data about the request.
- Capable of transmitting hypermedia documents as well as hypertext documents.
- Composed of two parts:
    - **Request**
        - created by a client
        - contains the request method, the target URL, the HTTP version, optional information (headers), and a body (for some methods)
    - **Response**
        - created by a server
        - contains the HTTP version, a status code and message the reflects the request outcome, optional information (headers), and a body (for some methods)
- In a URL, using a _fragment identifier_ `#text` is like a bookmark on the page or resource allowing the browser to scroll or navigate to that specific location on a page; It's never sent to the server with the request.
- Semantic URLs use human-readable words.

## Methods

- **GET**
    - Used to retrieve data at a specified resource.
    - Considered a safe and idempotent method.
- **POST**
    - Used to send data to a server to create or update a resource.
    - Non-idempotent.
- **PUT**
    - Used to send data to the API to update or create a resource.
    - Idempotent i.e. unlike POST, calling the same PUT request multiple times will always produce the same result.
- **PATCH**
    - Similar to POST & PUT
    - Only partial modifications are applied to the resource.
    - Non-idempotent.
- **DELETE**
    - Used to delete the resource at the specified URL.
- **HEAD**
    - Almost identical to GET, but without the response body.
    - Useful to check what a GET request will return before actually making a GET request.
- **OPTIONS**
    - Returns data describing what other methods and operations the server supports at the given URL.
    - Good candidate to test for fatal API errors.
- **TRACE**
    - Used to echo the received request back to the client, allowing the client to see what changes or additions have been made by intermediate servers.
    - Used primarily for diagnostic purposes and is not commonly used in regular web development.

## Headers

- Allow the client and server to exchange additional information with an HTTP request or response.
- Formatted as `name: value`
    - `name` is case-insensitive.
    - Whitespace before the value is ignored.
- Examples:
    - **Request**
        - `Accept: application/json, text/html`
        - `Content-Type: application/json`
    - **Response**
        - `Cache-Control: max-age=3600, public`
        - `Content-Type: text/html`
        - `Server: Apache/2.4.39 (Ubuntu)`
- Custom headers can also be created, and they are typically prefixed with `X-`.
    - `X-Custom-Header: custom-value`

### [[Caching#HTTP Headers|Caching]]

- HTTP headers can provide instructions to browsers and intermediaries (like proxies and CDNs) on how to store and serve cached content.
    - `Cache-Control`
    - `Expires`
    - `ETag`
    - `Last-Modified` & `If-Modified-Since`

```http
Cache-Control: public, max-age=31536000
```

```http
Expires: Sun, 03 May 2025 23:02:37 GMT
```

## Status Codes

- Help identify the cause of the problem when a web page doesn't load properly.
- A status consists of a numeric status code and an HTTP reason phrase.
    - e.g. `500: Internal Server Error`

- `100` – `199`
    - Informational responses
- `200` – `299`
    - Successful responses
- `300` – `399`
    - Redirection messages
- `400` – `499`
    - Client error responses
- `500` – `599`
    - Server error responses

- Learn more - [HTTP Status Codes Explained In 5 Minutes (YouTube)](https://www.youtube.com/watch?v=qmpUfWN7hh4)

## Ports

- **HTTP** - 80
- **HTTPS** - 443

## Cookies

- Set by web servers for browser-based applications.
    - Using one or more `Set-Cookie` response headers.
- Automatically sent with every HTTP request to the domain that set them.
- Accessible by both client-side JavaScript and server-side code.
- Have limited size (typically 4KB per cookie).
    - In addition to that, [[Web Browsers|browsers]] typically limit the number of cookies per domain (usually around 50).
- Often used for session management, user preferences, and tracking.
- Because cookies are repeatedly sent with every request, it consumes bandwidth and impacts performance.
- **Types**:
    - _Session cookies_ have no `Max-Age` or `Expires` attribute, and are deleted when an HTTP session is completed or the browser closes.
    - _Permanent cookies_ remain until expiration date or they are manually deleted.
- **Attributes**:
    - `Secure` - ensures the cookie is only sent over HTTPS.
    - `HttpOnly` - prevents client-side access to the cookie.
    - `Domain` - specifies which domains can receive the cookie.
    - `Path` - limits the cookie to specific paths on the server.
    - `Expires` or `Max-Age` - sets the cookie's lifespan.
    - `SameSite` - controls how cookies are sent with cross-site requests.

> [!important]
> By default, cookies are not sent with cross-origin requests. To allow this, the server must specify allowed origins.
>
> ```js
> // Client
> fetch("https://api.acme.org/", { credentials: "include" });
>
> // Server
> app.use(
> 	cors({
> 		origin: "https://acme.org",
> 		credentials: true,
> 	}),
> );
> ```
>
> It's important to note that:
>
> - Allowing credentials in cross-origin requests can pose security risks.
> - When using credentials, the `Access-Control-Allow-Origin` header cannot be set to `*`. The exact origin must be specified.
> - Many browsers are implementing restrictions on 3rd-party cookies, which can affect cross-origin requests with credentials.
> - Cross-origin cookie issues are tricky to debug.

- **Best Practices**:
    - Implement proper expiration times.
    - Be cautious with sensitive data storage.
    - Inform users about cookie usage.
    - Obtain consent when required by regulations (e.g., GDPR, CCPA).
    - Provide options to opt-out of non-essential cookies.
    - Consider other storage options like `localStorage`, `sessionStorage`, or `IndexedDB` for client-side data that doesn't need to be sent to the server.

```js
app.use(cookieParser());

// Setting Cookies
app.get("/", (req, res) => {
	res.cookie("user_id", "12345", {
		maxAge: 25200,
		httpOnly: true,
		secure: true,
	});
	res.send("Cookie has been set.");
});

// Reading Cookies
app.get("/read-cookie", (req, res) => {
	const userId = req.cookies.user_id;
	res.send(`User ID from cookie: ${userId}`);
});
```

- _Cookie prefixes_ are a security feature that allows servers to indicate certain security requirements for cookies. They are enforced by most modern browsers.
    - `__Secure-` prefix:
        - Must be set with the Secure flag.
        - Must be set from a secure origin (HTTPS).
    - `__Host-` prefix:
        - Must have all the requirements of `__Secure-`.
        - Must not have a `Domain` attribute.
        - Must have `Path` set to `/`.

```js
res.cookie("__Secure-ID", "123", {
	secure: true,
	httpOnly: true,
});

res.cookie("__Host-ID", "456", {
	secure: true,
	httpOnly: true,
	path: "/",
});
```

## HTTPS

- An extension of HTTP that uses encryption to secure the communication between a client and a server.
- Uses SSL (Secure Sockets Layer) or TLS (Transport Layer Security) protocols to encrypt the data transmitted, ensuring confidentiality, integrity, and authenticity.

### Implementation using [[Express]]

- Create Self-Signed Certificates (for Development Purposes)

```bash
openssl req -nodes -new -x509 -keyout private-key.pem -out certificate.pem
```

- Create an HTTPS Server

```js
const app = express();

const credentials = {
	key: fs.readFileSync("private-key.pem", "utf8"),
	cert: fs.readFileSync("certificate.pem", "utf8"),
};

app.get("/", (req, res) => {
	res.send("Hello, HTTPS world!");
});

const httpsServer = https.createServer(credentials, app);

httpsServer.listen(443, () => {
	console.log("HTTPS Server running on port 443");
});
```

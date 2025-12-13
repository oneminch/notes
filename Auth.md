---
alias: Authentication
---

- **Authentication** (AuthN)
    - Verifies _identity_ - WHO a user is.
    - On authentication failure, response code is 401 (Unauthorized)
- **Authorization** (AuthZ)
    - Determines _permission_ - WHAT a user can access.
    - On authorization failure, response code is 403 (Forbidden)

## Strategies

> [!quote]- REST API Authentication Methods (ByteByteGo)
> ![REST API Authentication Methods](assets/images/auth.methods.png)
>
> - Source: [ByteByteGo](https://blog.bytebytego.com/p/ep91-rest-api-authentication-methods)

### Basic Auth

- Involves sending a username and password with each request, encoded using Base64.
- Stateless since the server doesn't maintain any session information.
- **Flow**
    - Browser tries to access some protected URL.
    - If the server doesn't find the `Authorization` header in the request, it responds with a `401 Unauthorized` status and the header `WWW-Authenticate` with value set to `Basic` and an optional `realm` attribute.
    - Browser shows login popup.
    - When user signs in, it sends back the request to the server with the `Authorization` header set to `Basic` along with login credentials (`username:password`) encoded using Base64.
        - `Authorization: Basic <base64_encoded_credentials>`
    - Server verifies credentials, and responds with success.

```js
// Basic Auth Middleware
const basicAuth = (req, res, next) => {
	const authHeader = req.headers["authorization"];

	if (!authHeader) {
		res.setHeader("WWW-Authenticate", "Basic");
		return res.status(401).send("Authentication required.");
	}

	const [username, password] = decodeCredentials(authHeader).split(":");

	if (username === "admin" && password === "p4ssw0rd") {
		next();
	}

	res.status(401).send("Invalid credentials.");
};
```

> [!important] Security Risks
>
> - Credentials are only Base64 encoded, not encrypted, so they are vulnerable to interception if not sent over HTTPS.
> - There's no built-in mechanism to log out users or expire credentials.
> - In addition to that, there is no support for MFA or more advanced features.

### Session Based

- Unlike with token-based authentication, it is stateful.
    - State of the user session is handled on the server.
- Typically uses cookies to store session ID on the client.
- **Flow**
    - A user logs in to the server via a form. e.g. `POST` request to `/login` using username and password.
    - The server verifies the user's credentials against a database.
        - If valid, the server creates a session, which is a record that stores user-specific data (e.g., user ID, permissions).
        - This session is stored on the server, often in memory, a database, or a cache.
    - The server generates a unique session ID and sends it back to the client, typically as a cookie in the HTTP response.
        - Example: `Set-Cookie: sessionId=abc123; HttpOnly; Secure`
    - For subsequent requests, the client automatically includes the session ID cookie.
        - The server uses this session ID to retrieve the session data and authenticate the user.
        - The server checks if the session ID is valid and corresponds to an active session.
            - If valid, it retrieves the corresponding session data from the session store using the session ID.
                - When using the `express-session` middleware, session data is loaded into `req.session` object.
                - Now the server knows who the user is and can respond accordingly.
            - If invalid, it may return a `401 Unauthorized` response.
    - When the user logs out, the server destroys the session, and the session ID is invalidated.
        - The session cookie is typically deleted from the client's browser.

```js
const session = require('express-session');

const app = express();

app.use(session({
    secret: 'secret-key',
    cookie: {
        maxAge: 1000 * 60 * 60 * 24, // 24 hours
        secure: true
    },
    resave: true,
    saveUninitialized: false,
}));

app.get('/dashboard', (req, res) => {
    if (!req.session.userid) {
        return res.redirect('/login');
    }
    /* Render Dasboard (Protected) */
});

app.get('/login', (req, res) {
    if (req.session.userId) {
        return res.redirect('/dashboard');
    }
    /* Render Login Form */
});

app.post('/login', (req, res) {
    const { username, password } = req.body;

    if (username === 'admin' && password === 'p4ssw0rd') {
        req.session.userId = username;
        return res.redirect('/dashboard');
    } else {
        res.status(401).send('Invalid credentials');
    }
});

app.post('/logout', (req, res) => {
    req.session.destroy(err => {
        if (err) {
            return res.status(500).send('Error logging out');
        }
        return res.redirect('/login');
    });
});
```

> [!note] Disadvantages of Session Based Auth
>
> - Use a persistent and distributed session store in production.
>     - Maintaining sessions on the server can become challenging as the number of users grows.
> - Since cookies are automatically sent with requests, it is susceptible to [[Security#Cross-Site Request Forgery (CSRF)|CSRF]] attacks.
> - The workflow conflicts with the stateless nature of RESTful services.

### Token Based

- Stateless using tokens (e.g. JWTs).
- Operates on the principle of issuing a unique token after a user successfully logs in.
    - Tokens are sent with each request instead of credentials.
- Eliminate the need for maintaining sessions.
- **Flow**
    - The user logs in using their credentials (e.g., username and password).
    - Upon successful authentication, the server generates a token that contains encoded data about the user, including claims such as user ID and roles.
    - The token is sent back to the user’s client application typically as a cookie.
    - For each subsequent request, the client sends the token, usually in the HTTP header.
    - The server validates the token and processes the request based on the claims within the token.

```js
app.post("/login", (req, res) => {
	const { username, password } = req.body;

	if (username === "user" && password === "password") {
		const token = jwt.sign({ username }, "my_secret_key", {
			expiresIn: "1h",
		});

		res.cookie("token", token, {
			httpOnly: true,
			secure: true,
			maxAge: 3600000,
		});

		return res.json({ message: "Login Successful" });
	}

	res.status(401).send("Invalid credentials");
});

app.get("/protected", (req, res) => {
	const token = req.cookies.token;

	if (!token) {
		return res.status(403).send("Token is required");
	}

	jwt.verify(token, "my_secret_key", (err, decoded) => {
		if (err) {
			return res.status(401).send("Invalid token");
		}

		res.json({ message: "Protected Data", user: decoded });
		// OR (if inside a middleware)
		// req.body.username = decoded.username;
		// next();
	});
});

app.post("/logout", (req, res) => {
	res.clearCookie("token");
	res.json({ message: "Logout Successful" });
});
```

> [!note] JWT Components
>
> - **Header** - token metadata, such as signing algorithm.
> - **Payload** - claims, such as user info and token expiration.
> - **Signature** - to ensure integrity.

> [!quote] The downside of JWTs for authentication
> Once a JWT is signed, there is no way to invalidate the JWT or update the information contained within it. Provided the signature is valid and the expiry timestamp has not passed, the JWT will be considered valid by any process leveraging it for authorization decisions.
>
> If a user requests to log out of all devices, there is no practical way to honor this request through local validation before the natural expiry of all currently issued JWTs. In theory, JWTs can also be invalidated by revoking the secret key used to sign the JWT but that would invalidate all JWTs that used that key and would require handling to evict any cached validation keys, making secret key revocation an unsustainable option for something as simple as a user log out.
>
> Similarly, in the case where the JWT contains role-based authorization information (such as “admin” vs “member”), if the user is downgraded to a lower role that reduces the scope of what they are allowed to access, this change in authorization permissions would not be reflected until their existing JWTs expired.
>
> **Source**: [JWTs vs. sessions: which authentication approach is right for you?](https://stytch.com/blog/jwts-vs-sessions-which-is-right-for-you/)

- **Bearer authentication** involves security tokens called bearer tokens, which is a cryptic strings, usually generated by the server after a login request.
    - The client sends this token in the `Authorization` header when making requests.
    - This form of authentication should only be done over HTTPS.

```http
Authorization: Bearer <bearer_token>
```

### Open Authorization (OAuth)

- Open standard protocol that allows users to grant third-party apps limited access to their resources without sharing passwords.
- **Key Concepts**
    - **Resource Owner**: The user who owns the data
    - **Client**: The application requesting access
    - **Authorization Server**: Issues access tokens (e.g., Google, GitHub)
    - **Resource Server**: Hosts the protected resources (e.g., Google Drive API)
        - e.g. OAuth is the underlying mechanism that securely powers a service asking for permission to access a Google account to retrieve calendar events.
- Primarily about authorization (granting access to resources), not authentication.
- Uses access tokens to grant permissions.
    - The authorized app receives a token that allows it to interact with user data without the need for a password.
- Also uses scopes to specify the permissions requested by the client, which can be fine-grained or coarse-grained.

> [!example]
> When a developer installs a third-party application from the GitHub Marketplace, OAuth is used to authorize the app to access their GitHub repositories, allowing it to perform tasks without requiring the developer to log in multiple times.

### OpenID Connect

- An authentication layer built on top of OAuth 2.0.
- Allows clients (e.g. web apps) to verify the identity of a user based on the authentication performed by an authorization server.
- Uses ID tokens for authentication along with access tokens.
    - The ID token contains user information, such as their name and email address.
- Allows applications to obtain user profile information from the identity provider, such as name and email, in a secure manner.

> [!example]
> When users select "Sign in with Google" as an authentication option, they are redirected to Google’s login page. After entering their credentials and granting permission, Google sends back an ID token that the website can use to authenticate the user and retrieve their profile information.
>
> Organizations often use OpenID Connect for SSO across multiple applications. Employees can log in once using their corporate identity provider (like Azure Active Directory) and gain access to various internal applications without needing to log in again. This streamlines the user experience and enhances security by centralizing authentication.

### API Key Authentication

- Involves using a unique identifier, known as an API key, which is assigned to the client application.
- Can also be used as a means of authorization.
- Enables API providers to track usage patterns and monitor for abuse or unauthorized access.

```js
const API_KEY = "your_api_key_here";

// API Key Middleware
const apiKeyMiddleware = (req, res, next) => {
	const apiKey = req.headers["x-api-key"];

	if (!apiKey || apiKey !== API_KEY) {
		return res.status(403).json({ message: "Forbidden: Invalid API key" });
	}

	next();
};
```

```bash
curl -H "x-api-key: your_api_key_here" http://localhost:3000/data
```

## Authorization (AuthZ)

- Unlike authentication, which deals with verifying identity (WHO a user is), authorization determines permission (WHAT a user can access).

```js
const users = [
    { id: 1, username: 'admin', password: 'adminpass', role: 'admin' },
    { id: 2, username: 'user', password: 'userpass', role: 'user' }
];

// Authorization Middleware
const authorize = (roles) => (
    (req, res, next) => {
        const token = req.cookies.token;

        if (!token) {
            return res.status(403).json({ message: 'No token provided' });
        }

        jwt.verify(token, 'your_secret_key', (err, decoded) => {
            if (err) {
                return res.status(401).json({ message: 'Unauthorized' });
            }

            if (!roles.includes(decoded.role)) {
                return res.status(403).json({ message: 'Forbidden' });
            }

            req.user = decoded;
            next();
        });
    );
};
```

```js
// Server
app.post("/login", authMiddleware);

app.get("/admin", authorize(["admin"]), (req, res) => {
	res.json({ message: "Welcome to the Admin area!" });
});

app.get("/user", authorize(["user", "admin"]), (req, res) => {
	res.json({ message: "Welcome to the User area!" });
});
```

## Best Practices

- Never store passwords in plain text.
    - Store passwords securely using hashing algorithms such as bcrypt, Argon2, or scrypt.
    - Always use salting to protect against rainbow table attacks.
- Implement proper password policies (complexity, expiration, etc.).
    - Users should be encouraged to change their passwords periodically and especially after any suspected data breaches.
- Use HTTPS for all authentication requests.
- Use secure session management techniques.
- Protect against common attacks (XSS, [[Security#Cross-Site Request Forgery (CSRF)|CSRF]], etc.).
- Consider implementing MFA for sensitive operations.
- Use secure password reset mechanisms
- Regularly audit and update authentication systems.
- Implement measures such as rate limiting, CAPTCHA, and monitoring for unusual login attempts to defend against automated bots and brute force attacks.

### Common Mistakes

- Not enforcing password complexity can lead to easy exploitation through brute force or dictionary attacks
- Not implementing secure session management practices, such as not expiring sessions or using insecure cookies, can lead to session hijacking
- Not securing the account recovery process can allow attackers to bypass authentication.
    - Recovery processes should include additional verification steps.
- Relying on password-based authentication without implementing additional security measures like MFA.
- Implementing overly complex authentication processes can frustrate users impacting UX.
- Not logging authentication attempts and monitoring for suspicious activity can prevent timely detection of breaches or unauthorized access.

---

## Further

### Reads 📄

- [How to Secure the Web: A Comprehensive Guide to Authentication Strategies for Developers (DEV)](https://dev.to/ma7moud3bas/how-to-secure-the-web-a-comprehensive-guide-to-authentication-strategies-for-developers-48od)

### Resources 🧩

- [Lucia Auth Guide](https://lucia-auth.com/) ⭐

- [The Copenhagen Book](https://thecopenhagenbook.com/) ⭐

### Videos 🎥

![Auth Does NOT Have To Be Hard](https://www.youtube.com/watch?v=mL8EuL7jSbg)

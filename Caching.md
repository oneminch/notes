## Client-Side

- Property set HTTP cache headers such as `Cache-Control`.
    - Use shorter durations for dynamic content.

```js
// Set a one-year cache for static assets
app.use((req, res, next) => {
	res.set("Cache-Control", "public, max-age=31536000");
	next();
});
```

- Service Workers provide powerful caching capabilities for Progressive Web Apps.

## Server-Side / API

- Use in-memory caching for frequently accessed computations or data.

```js
const NodeCache = require("node-cache");

const myCache = new NodeCache({ stdTTL: 100, checkperiod: 120 });

function getCachedData(key, fetchFunction) {
	const value = myCache.get(key);
	if (value) {
		return Promise.resolve(value);
	}

	return fetchFunction().then((result) => {
		myCache.set(key, result);
		return result;
	});
}
```

- For database-heavy applications, query caching can significantly improve performance.

```js
const { Pool } = require("pg");
const pool = new Pool();

const queryCache = new Map();

async function cachedQuery(sql, params) {
	const cacheKey = `${sql}-${JSON.stringify(params)}`;

	if (queryCache.has(cacheKey)) {
		return queryCache.get(cacheKey);
	}

	const result = await pool.query(sql, params);
	queryCache.set(cacheKey, result);

	return result;
}
```

- Utilize tools like Redis for distributed systems.

```js
const redis = require("redis");
const client = redis.createClient();

function getCachedData(key, fetchFunction) {
	return new Promise((resolve, reject) => {
		client.get(key, (err, data) => {
			if (err) reject(err);

			if (data != null) {
				resolve(JSON.parse(data));
			} else {
				fetchFunction().then((result) => {
					client.setex(key, 3600, JSON.stringify(result));
					resolve(result);
				});
			}
		});
	});
}
```

- For RESTful APIs, caching responses can reduce server load.

```js
const { middleware: cache } = require("apicache");

app.get("/api/data", cache("5 minutes"), (req, res) => {
	/* ... */
});
```

## HTTP Headers

### `Cache-Control`

- An [[HTTP]] header that dictates how web content should be cached and managed by browsers and intermediary servers (such as CDNs and proxy servers).
- Instructs browsers and other caching mechanisms on how to handle the caching of web resources.
- **Primary Goals**:
    - Optimizing [[Web Performance]] by reducing unnecessary server requests
    - Ensuring content freshness
    - Balancing between serving up-to-date content and reducing server load
- **Directives**:
    - `no-store` - instructs that the response should not be stored in any cache.
        - The most restrictive directive, often used for sensitive data.
    - `no-cache` - despite its name, this doesn't prevent caching.
        - It requires the browser to revalidate the content with the server before serving it from the cache.
    - `max-age` - specifies how long (in seconds) a resource can be considered fresh before requiring revalidation.
    - `public` vs. `private`:
        - `public` - allows the response to be stored in shared caches (proxies).
        - `private` - restricts storage to private user caches (client) only.

```javascript
app.use((req, res, next) => {
	res.set("Cache-Control", "no-store");
	next();
});
```

> [!note]
> While `Cache-Control` can significantly enhance web performance, it's crucial to balance performance gains with security considerations, especially for sensitive data.

- **Best Practices**
    - Use `no-store` for sensitive information.
    - Implement `max-age` strategically for static resources.
    - Consider using `must-revalidate` for critical resources.

### `E-Tag` (Entity Tag)

- Unique identifiers for specific versions of a resource.
- Allow the server to determine if the client's cached version is still valid.
- Can cause issues when working with load balancers.
- Has been used to track users (without using cookies).
- Modern web browsers automatically handle ETags.
    - If the client already has that resource cached (and it still has the ETag), the browser will send an `If-None-Match` header with the ETag value in subsequent requests.
    - If the ETag hasn't changed on the server side, the server can respond with a **304 Not Modified** status, avoiding sending the entire resource again.
- On the server-side, custom implementation is required to generate and manage ETags, especially for dynamic content or resources that change frequently.

```js
// Server-side
app.get("/api/data", (req, res) => {
	const data = { message: "Hello, World!" };
	const jsonData = JSON.stringify(data);

	// Generate ETag based on the content
	const etag = crypto.createHash("md5").update(jsonData).digest("hex");

	// Check if the client's ETag matches
	if (req.headers["if-none-match"] === etag) {
		return res.status(304).send();
	}

	res.setHeader("ETag", etag);
	res.json(data);
});
```

```js
// Client-side (Custom Implementation)
const cachedETag = localStorage.getItem("dataETag");

try {
	const response = await fetch("/api/data", {
		headers: cachedETag ? { "If-None-Match": cachedETag } : {},
	});

	if (response.status === 304) {
		// Data unmodified, use cached version
		return JSON.parse(localStorage.getItem("cachedData"));
	} else {
		const data = await response.json();
		const newETag = response.headers.get("ETag");

		localStorage.setItem("dataETag", newETag);
		localStorage.setItem("cachedData", JSON.stringify(data));

		return data;
	}
} catch (error) {
	console.error("Error fetching data:", error);
}
```

### `Last-Modified`

- Simpler caching mechanism based on timestamps.
- If the client (browser) has a cached version of a resource, it will automatically send the `If-Modified-Since` header with the **timestamp** it received from the `Last-Modified` header in the previous response.
    - If the resource hasn't changed since that timestamp, the server will respond with a `304 Not Modified` status, and the client will continue to use its cached version.
- On the server-side, the server needs to generate and include the `Last-Modified` header in the response, typically based on the **last modification time** of the resource.
    - The server also needs to handle the `If-Modified-Since` request header (check whether the resource has changed since the timestamp provided by the client).
    - If the resource hasn't changed, the server should respond with a **`304 Not Modified`** status.

```js
// Server-side
let lastModified = new Date();
let data = { message: "Hello, World!" };

app.get("/api/data", (req, res) => {
	const ifModifiedSince = req.get("If-Modified-Since");

	if (ifModifiedSince && new Date(ifModifiedSince) >= lastModified) {
		return res.status(304).send();
	}

	res.setHeader("Last-Modified", lastModified.toUTCString());
	res.json(data);
});
```

```js
// Client-side
const cachedLastModified = localStorage.getItem("dataLastModified");

try {
	const response = await fetch("/api/data", {
		headers: cachedLastModified
			? { "If-Modified-Since": cachedLastModified }
			: {},
	});

	if (response.status === 304) {
		// Data unmodified, use cached version
		return JSON.parse(localStorage.getItem("cachedData"));
	} else {
		const data = await response.json();
		const newLastModified = response.headers.get("Last-Modified");

		localStorage.setItem("dataLastModified", newLastModified);
		localStorage.setItem("cachedData", JSON.stringify(data));

		return data;
	}
} catch (error) {
	console.error("Error fetching data:", error);
}
```

## CDNs

- Utilize Content Delivery Networks (CDNs) for static assets.
    - Use versioned URLs.

## Strategies

### Cache-Aside (Lazy Loading)

- Ideal for data that's expensive to compute or retrieve but doesn't change frequently.
- It's called "lazy" because the cache is populated only when a cache miss occurs.

```js
const cache = new Map();

async function getCachedData(key) {
	// Cache Hit
	if (cache.has(key)) {
		return cache.get(key);
	}

	// Cache Miss
	const data = await expensiveFetch(key);
	cache.set(key, data);
	return data;
}

async function expensiveFetch(key) {
	await new Promise((resolve) => setTimeout(resolve, 2000));
	return `Data for ${key}`;
}
```

### Write-Through

- Ensures that data is written to both the cache and the underlying storage system simultaneously.
- Useful when write performance is less critical than read performance and data consistency is important.

```js
const cache = new Map();

async function setData(key, value) {
	await writeToDB(key, value);
	cache.set(key, value);
}

async function getData(key) {
	if (cache.has(key)) {
		return cache.get(key);
	}
	const value = await readFromDB(key);
	if (value !== undefined) {
		cache.set(key, value);
	}
	return value;
}

async function writeToDB(key, value) {
	await db.insert(/* ... */);
}

async function readFromDB(key) {
	await db.select(/* ... */);
}
```

## Cache Replacement Policies

- Determine how data is evicted from the cache when it reaches capacity:
    - **First In First Out (FIFO)**: Evicts the oldest data first.
        - Used in systems with limited computational resources
    - **Least Recently Used (LRU)**: Removes the least recently accessed items first.
        - e.g. Web browsers caching recently visited pages, CDNs, DBMS query result caching
    - **Last In First Out (LIFO)**: Evicts the most recently added data first.
    - **Least Frequently Used (LFU)**: Evicts the least frequently accessed items.
    - **Most Recently Used (MRU)**: Removes the most recently used items first.

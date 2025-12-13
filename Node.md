---
alias: Node.js
---

## What is Node.js?

- An asynchronous event-driven runtime environment.
- Allows [[JavaScript]] to be run outside the browser.
- Single-threaded
    - A Node.js app runs without creating a new thread for every request.
- Execution stops as soon as there is no more tasks left in the event loop.
    - `http.createServer()` event never finishes running by default.
    - The event loop is only used to determine what to do next when the execution of a task finishes.
    - e.g. In the snippet below, the `while` loop is not interrupted by `setTimeout`, because the event loop is only checked for new tasks once an ongoing execution is complete.

```js
setTimeout(() => {
	console.log("Timeout");
}, 1000);

console.log("Enter Loop");

let i = 0;
while (new Date().getTime() < start.getTime() + 4000) {
	i++;
}
console.log("Exit Loop");

/*
Output:
Enter Loop
Exit Loop
Timeout
*/
```

- Non-blocking I/O model using the _event loop_ (events and callbacks)
    - Code is not necessarily executed in the order that it is written.
    - Every I/O operation takes a callback. When performing I/O, control is passed back to the event loop. The callback will be run once the data from the I/O operation is available.

```js
fs.readFile("/path/to/file.txt", (err, data) => {
	console.log(data);
});
```

- Built on the V8 [[JavaScript|JS]] engine
- Ideal use-cases:
    - APIs utilizing databases
    - streaming data
    - real-time data transfer (e.g. chat apps)
    - server-side web apps
- Not ideal for server-side processing that's CPU-intensive (e.g. file conversion)
    - For a CPU-intensive workloads, the only approach is to either optimize the algorithm to use less CPU or to scale to multiple cores and machines to utilize more CPUs.
- By default, request data is transferred in chunks and needs to be parsed (Streams & Buffers)
    - Streams are an event-driven way to handle data in Node.js that arrives in chunks, rather than all at once.

> [!note]
> Due to non-blocking design of Node.js, code may not execute in the order it's written.

## Node.js vs. the Browser

- A single language is used to on both the frontend and the backend, but the ecosystem is different.
- [[DOM]] objects such as `document` and `window` don't exist in Node.js.
- The browser doesn't have access to APIs like ones that allow filesystem access.
- Node.js allows you to control the environment it runs in like the version of Node.js used. In browsers, the option of choosing the browser environment visitors will used doesn't exist.
    - We can therefore use the latest supported [[JavaScript|JS]] syntax without worrying about compatibility.
- Node.js supports both CommonJS (`require()`/`module.exports`) and ES module systems (`import`/`export`), while the browser is still getting implementation of the ES modules standard (`import`/`export`).

## Global Objects

### `globalThis`

- A built-in JavaScript object that provides a standard way to access the global object across different environments, such as browsers, web workers and Node.js.
    - In browsers, `globalThis` is equivalent to `window`.
    - In Node.js, `globalThis` is equivalent to `global`.
    - In web workers, `globalThis` is equivalent to `self`.

### `process`

- The `process` object provides information about and control over the current Node.js process.
- It is a global object that can be accessed from anywhere in a Node.js application without requiring it.
- It offers various data sets about the program's runtime providing properties that allow for managing the Node.js process effectively like `process.versions`, `process.release`, and methods like `process.exit()` for exiting the event loop.
- Furthermore, it facilitates interactions with the environment through properties like `process.env` and enables access to command-line arguments via `process.argv`.

```js
const server = http.createServer((req, res) => {
	console.log(req);
	process.exit();
});
```

- **Properties** & **Methods**
    - `process.abort()` - exits the Node.js process immediately.
    - `process.cwd()` - returns the current working directory from which the Node.js process was started.
    - `process.argv` - returns an array of the command-line arguments passed when the Node.js process was launched.

> [!tip]
> `__filename` is a variable in Node.js that contains the full path to the currently executing file.
>
> `__dirname` provides the path to the directory that contains that file.

## Core Modules

- Node.js provides a number of built-in modules that can be used without having to install them separately:

- `fs`
    - provides an API for reading and writing files, as well as working with directories.
    - can be used to perform various file system operations such as creating, deleting, renaming, and modifying files.
    - `readFile()` & `readFileSync()`

```js
import fs from "node:fs";

fs.readFile("/Users/joe/test.txt", "utf8", (err, data) => {
	if (err) {
		console.error(err);
		return;
	}
	console.log(data);
});
```

> [!note]
> All read methods of the `fs` module read the full content of a file in memory before returning the data. Therefore, for big files, the process can be memory-intensive and slow. It's recommended to read file contents using _streams_.
>
> ```js
> async function readFile(filePath) {
> 	const readStream = fs.createReadStream(filePath, { encoding: "utf8" });
>
> 	try {
> 		for await (const chunk of readStream) {
> 			console.log("--- File chunk start ---");
> 			console.log(chunk);
> 			console.log("--- File chunk end ---");
> 		}
> 		console.log("Finished reading the file.");
> 	} catch (error) {
> 		console.error(`Error reading file: ${error.message}`);
> 	}
> }
> ```

- `path`
    - provides utilities for working with file and directory paths.
        - `dirname()`: gets the parent folder of a file
        - `basename()`: gets the filename part
        - `extname()`: gets the file extension
    - It can be used to join multiple path segments together, resolve relative paths, and parse path strings into their components.

```js
import path from "node:path";

const notes = "/users/jdoe/file.md";

path.dirname(notes); // /users/jdoe
path.basename(notes); // file.txt
path.extname(notes); // .md

path.resolve("notes", "file.md");
// '/Users/jdoe/notes/file.md' if run from home (/) folder
```

- `os`
    - provides information about the operating system on which Node.js is running.
    - can be used to retrieve information such as the hostname, CPU architecture, and amount of free memory.
- `events`
    - can be used to create custom events and handle them.
- `crypto`
    - provides cryptographic functionality that can be used to generate hash values, encrypt and decrypt data, and generate random numbers.

- `http` & `https` modules allow us to launch a server among other things.
    - `https` launches an SSL server.
    - Data send thru a `POST` request is sent as a stream which has to be buffered in to the desired data.

```js
const http = require("node:http");

const createPage = (text) => `
  <html>
    <head>
      <title>Welcome to Node.js</title>
    </head>
    <body>
      <h1>${text}</h1>
    </body>
  </html>
`;

const reqHandler = (req, res) => {
	const { headers } = request;
	const userAgent = headers["user-agent"];

	res.setHeader("Content-Type", "text/html");

	if (req.url === "/") {
		res.write(createPage("Hello, Node!"));
		return res.end();
	}

	res.write(createPage("404 - Not Found!"));
	res.end();
};

const server = http.createServer(reqHandler);
// OR
const server = http.createServer();
server.on("request", reqHandler);

server.listen(2345);
```

> [!important]
> It's important to set the status and headers _before_ writing chunks of data to the body.

## Debugging

- `node --inspect`
    - enables inspector agent
    - listens on default address and port (127.0.0.1:9229)
- `node inspect script.js`
    - spawn child process to run script under `--inspect` flag
    - uses main process to run CLI debugger.

## Async Programming

- Synchronous code is said to be _blocking_ can only execute after the one before has finished executed. e.g. `fs.readFileSync()` and `fs.writeFileSync()`

```js
const fs = require("fs");

const textIn = fs.readFileSync("input.md", "utf-8");
console.log(textIn);

const textOut = "Overwritten text.";
fs.writeFileSync("input.md", textOut);
```

- Node.js uses callbacks to make execution asynchronous / non-blocking. e.g. `fs.readFile()` and `fs.writeFile()`

```js
const fs = require("fs");

fs.readFile("input.md", "utf-8", (err, data) => {
	console.log(data);
});
console.log("Reading file...");
```

> [!note]
> Because of its event-driven architecture, some operations rely on callbacks that get executed during a certain event. The `return` keyword is often used before a function call to prevent further execution of code.

- Node.js also provides Promise-based versions of many core APIs, especially ones where asynchronous operations were traditionally handled using callbacks.
    - This reduces the risk of "callback hell."
    - e.g. `fs` -> `fs.promises` (or import from `node:fs/promises`)

### Event Emitters

- `EventEmitter`s serve as the foundation for event-driven architecture in Node.js.

> [!quote] Architecture
> Much of the Node.js core API is built around an idiomatic asynchronous event-driven architecture in which certain kinds of objects (called "emitters") emit named events that cause `Function` objects ("listeners") to be called.

- All objects that emit events are instances of the `EventEmitter` class.
    - They expose an `eventEmitter.on()` function that allows one or more functions to be attached to named events emitted by the object.
    - When an object emits an event, all listeners attached to that specific event are called _synchronously_ to avoid race conditions and logic errors.
        - Any values returned values by the called listeners are _ignored_ and discarded.
        - Listener functions can be made to run asynchronously using the `setImmediate()` or `process.nextTick()` methods.

```js
import { EventEmitter } from "node:events";

class OrderSystem extends EventEmitter {
	placeOrder(order) {
		this.emit("order", order);
	}
}

const orders = new OrderSystem();

// Process Payment
orders.on("order", (order) => {
	console.log(`Processing payment for ${order.amount}`);
});

// Inventory update
orders.on("order", (order) => {
	console.log(`Updating inventory for ${order.items.length} items`);
});

orders.placeOrder({ amount: 99.99, items: ["Shirt", "Mug"] });
// Processing payment for 99.99
// Updating inventory for 2 items[7][6]
```

### Scheduling

> [!example]- 🎥 JavaScript Visualized - Event Loop, Web APIs, (Micro)task Queue (YouTube)
> ![JavaScript Visualized - Event Loop, Web APIs, (Micro)task Queue ⭐](https://www.youtube.com/watch?v=eiC58R16hb8)

- Node.js provides other mechanisms for scheduling tasks in the event loop.

- **`queueMicrotask`**
    - used to schedule a microtask (a lightweight task that runs after the script that's currently executing but before any other I/O events/timers).
    - typically used for Promise resolutions.
    - should be favored over `process.nextTick`.

```js
queueMicrotask(() => console.log("Logs Next."));

console.log("Logs First.");
```

- **`process.nextTick`** (Legacy)
    - used to schedule a callback to be executed immediately after the current operation completes, but before moving to the next phase in the event loop.
    - useful to ensure that a callback is executed as soon as possible, but still after the current execution context.
        - callback is executed on the current iteration of the event loop, after the current operation ends. It will always execute before `setTimeout` and `setImmediate`.
    - has the highest priority among asynchronous callbacks since its queue is always processed before the microtask queue within each cycle of the event loop.
    - order of execution with `queueMicrotask` is inconsistent across CommonJS and ES modules.

```js
process.nextTick(() => {
	console.log("Second.");
});

console.log("First.");
```

- **`setImmediate`**
    - executes a callback after the current event loop cycle is finished and all I/O events have been processed.
    - callbacks run after any I/O callbacks and in the next iteration of the event loop, but before timers.
    - equivalent to `setTimeout(() => {}, 0)`

```js
setImmediate(() => {
	console.log("Immediate callback");
});

console.log("Synchronous task executed");
```

> [!important]
> Scheduled tasks execute outside of the current synchronous flow. Therefore, any uncaught exceptions inside the callbacks will not be caught by `try/catch` blocks and may crash the application if not properly managed.

### Timers

- `setTimeout` can be used to delay the execution of a function. With a timeout delay of `0`, the callback function of a `setTimeout` call will be executed as soon as possible, but after the current function execution.

```js
setTimeout(() => {
	console.log("After");
}, 0);

console.log("Before");

/* 
Logs:
    Before
    After
*/
```

- This can be useful to avoid blocking on CPU-intensive tasks.
    - The callback function is added to the queue in the scheduler and other functions can be executed beforehand.
- `setTimeout` and `setInterval` are global functions available in Node.js thru the `timer` module; They don't require imports.
- Timer functions in Node.js implement a similar API as the ones provided by [[Web Browsers]] but use a different internal implementation that is built around the Node.js Event Loop.

## Express

> [!cite]- Express
> ![[Express]]

## Testing

### Unit Testing

> [!example] Example: Testing a Node server with Jest & Supertest

```js
// app.test.js
const request = require("supertest");

describe("Product API", () => {
	describe("GET /api/products", () => {
		it("should return all products", async () => {
			const res = await request(app).get("/api/products");
			expect(res.statusCode).toBe(200);
			expect(res.body.length).toBeGreaterThan(0);
			expect(res.body[0]).toHaveProperty("id");
			expect(res.body[0]).toHaveProperty("name");
			expect(res.body[0]).toHaveProperty("price");
		});
	});

	describe("GET /api/products/:id", () => {
		it("should return a product if valid id is passed", async () => {
			const res = await request(app).get("/api/products/1");
			expect(res.statusCode).toBe(200);
			expect(res.body).toHaveProperty("id", 1);
		});

		it("should return 404 if invalid id is passed", async () => {
			const res = await request(app).get("/api/products/9999");
			expect(res.statusCode).toBe(404);
		});
	});

	describe("PUT /api/products/:id", () => {
		it("should update an existing product", async () => {
			const res = await request(app).put("/api/products/1").send({
				name: "Updated Name",
				price: 69.99,
			});
			expect(res.statusCode).toBe(200);
			expect(res.body.name).toBe("Updated Name");
			expect(res.body.price).toBe(69.99);
		});
	});

	describe("DELETE /api/products/:id", () => {
		it("should delete an existing product", async () => {
			const res = await request(app).delete("/api/products/2");
			expect(res.statusCode).toBe(204);
		});

		it("should return 404 if product not found", async () => {
			const res = await request(app).delete("/api/products/9999");
			expect(res.statusCode).toBe(404);
		});
	});
});
```

### E2E Testing

```js
// app.test.js
const puppeteer = require("puppeteer");

let server;
let browser;

beforeAll((done) => {
	server = http.createServer(app);
	server.listen(3000, () => {
		console.log("Test server running on port 3000");
		done();
	});
});

afterAll((done) => {
	server.close(done);
});

beforeEach(async () => {
	browser = await puppeteer.launch();
});

afterEach(async () => {
	await browser.close();
});

test("should display 'Hello, Node!'", async () => {
	const page = await browser.newPage();
	await page.goto("http://localhost:3000");

	const content = await page.content();
	expect(content).toContain("Hello, world!");
});
```

## TypeScript

- Prior to v22, Node.js didn't have built-in TypeScript support.
- `.ts` will have to be compiled into `.js` files before running them with Node.

```bash
npx tsc -p .
```

- During development, tools like `ts-node` can be used along with `nodemon` to automatically build and run TypeScript files.
- The Node.js ecosystem provides many TypeScript-first libraries and tools:
    - Prisma
    - NestJS
    - TypeORM
    - AdonisJS

- Starting from v22, Node provides some level of native but experimental support for TypeScript using flags:

```bash
# Enabled by default since v23.6.0
node --experimental-strip-types app.ts

node --experimental-transform-types app.ts
```

## Miscellany

### The JavaScript Module System

> [!cite]- JS Module System
> ![[JS Module System]]

- By default, Node.js will treat a file as a CommonJS module:
    - via the `.cjs` file extension,
    - via a `.js` extension when the nearest parent `package.json` file contains a top-level field `"type"` with a value of `"commonjs"`, or
    - via a `.js` extension or without an extension, when the nearest parent `package.json` file doesn't contain a top-level field `"type"` or there is no `package.json` in any parent folder
- Node.js interprets JavaScript as an ES module:
    - via the `.mjs` file extension,
    - via the `package.json` `"type"` field with a value `"module"`, or
    - via the `--input-type` flag with a value of `"module"`.
    - These are explicit markers of code being intended to run as an ES module.
- The `__filename` or `__dirname` CommonJS variables are not available in ES modules.
    - Their use cases can be replicated via `import.meta.filename` and `import.meta.dirname`.
- `await` may be used in the top-level body of an ECMAScript module.

### Built-In Modules

#### `AsyncLocalStorage`

- One way for managing context in asynchronous Node.js apps.
    - Allow storing data throughout the lifetime of a web request or any other asynchronous duration.
- Creates stores that stay coherent through asynchronous operations.

```js
import express from "express";
import { AsyncLocalStorage } from "node:async_hooks";

const asyncLocalStorage = new AsyncLocalStorage();

function authMiddleware(req, res, next) {
	const user = { id: 1, name: "John Doe" };

	// Runs the rest of the request handler
	// within the context of AsyncLocalStorage
	asyncLocalStorage.run(user, () => {
		next();
	});
}

function logUserMiddleware(req, res, next) {
	const user = asyncLocalStorage.getStore();
	console.log(`User: ${user.name}`);
	next();
}

const app = express();

app.use(authMiddleware);
app.use(logUserMiddleware);

app.get("/user", (req, res) => {
	const user = asyncLocalStorage.getStore();
	res.send(`Hello, ${user.name}!`);
});

app.listen(3000, () => {
	console.log("Server is running on http://localhost:3000");
});
```

#### Streams

- The `stream` module provides a set of classes for working with streaming data, such as reading or writing from files and network requests, without compromising performance.
- Useful for large files.
- Includes implementations of readable, writable, duplex, and transform streams.
- For instance, in a request handler callback for `http.createServer`, the `request` object implements the `ReadableStream` interface and the `response` object implements the `WritableStream` interface.
    - The `data` & `end` events can be listened to on the `request` object get the data from the stream.
    - The chunk of data emitted in each `data` event is a `Buffer`.
- When reading from or writing to a stream, the data is not processed all at once.
    - Instead, it is handled in chunks, which are stored in a buffer.
    - The size of the buffer is controlled by the `highWaterMark` option, which determines how much data can be stored before the stream pauses to prevent memory overload (a mechanism called _backpressure_)

```js
http.createServer((request, response) => {
	const { headers, method, url } = request;
	let body = [];
	request
		.on("error", (err) => {
			console.error(err);
		})
		.on("data", (chunk) => {
			body.push(chunk);
		})
		.on("end", () => {
			body = Buffer.concat(body).toString();
		});
}).listen(8080);
```

```js
http.createServer((req, res) => {
	if (req.method === "POST" && req.url === "/echo") {
		req.pipe(res);
	} else {
		res.statusCode = 404;
		res.end();
	}
}).listen(8080);
```

#### Buffers

- Objects that are used to handle raw binary data directly.
- Provide a way to manage a temporary, fixed-size chunk of memory outside of V8's heap.
- Used internally by streams to hold data temporarily until it can be processed or transferred.
- Useful for small, complete data in memory.
- Particularly useful for dealing with streams of binary data, such as reading from or writing to files, network communication, and image processing.

```js
import { writeFile, readFile } from "fs/promises";

async function readFileIntoBuffer(filePath) {
	try {
		// Read the file into a buffer
		const buffer = await readFile(filePath);

		// Convert the buffer to a string
		const content = buffer.toString("utf-8");

		console.log("File content:", content);
	} catch (error) {
		console.error("Error reading file:", error);
	}
}

async function writeBufferToFile(filePath, data) {
	try {
		// Create a buffer from a string
		const buffer = Buffer.from(data, "utf-8");

		// Write the buffer to a file
		await writeFile(filePath, buffer);

		console.log("File written successfully");
	} catch (error) {
		console.error("Error writing file:", error);
	}
}

// Usage
readFileIntoBuffer("example.txt");
writeBufferToFile("output.txt", "Hello, world!");
```

#### Other Modules

- `node:child_process`
    - provides the ability to spawn subprocesses (primarily using the asynchronous `spawn()` method).
- `node:cluster`
    - creates multiple worker processes (child processes) to handle incoming requests, enabling your app to utilize multiple CPU cores.
    - Multiple processes with separate memory.
    - solves Node.js' default single-threaded limitation for CPU-bound tasks.
- `node:dns`
    - enables name resolution.
    - e.g. use it to look up IP addresses of host names.
- `node:querystring`
    - provides utilities for parsing and formatting URL query strings.
    - more performant than `URLSearchParams`, but is not standardized.
- `node:worker_threads`
    - enables the use of threads that execute JavaScript in parallel for CPU-intensive tasks.
    - can share memory (via `SharedArrayBuffer`) unlike `child_process` or `cluster`.
    - The `Worker` class represents an independent JavaScript execution thread.
        - Most Node.js APIs are available inside of it.
- `node:zlib`
    - provides compression functionality implemented using Gzip, Deflate/Inflate, Brotli, and Zstd.

### Command Line Scripts

```js
#!/usr/bin/node
/* OR */
#!/usr/bin/env node

// JS Code
```

- To run a node file as a script, it requires an executable permission.

```bash
chmod u+x app.js
```

- Node can also run tasks defined in `package.json` using the `--run` flag.

```bash
node --run test
```

- To pass arguments to a command, the `-- --another-arg` syntax is used.

```bash
node --run -- --watch
```

- Environment variables can be set from the command line as well.
    - Since Node.js 20, there's also an experimental support for reading one or more `.env` files using the `--env-file` flag (or `--env-file-if-exists` for optional reading).

```bash
USERNAME=jdoe API_KEY=a1b2c3 node app.js

node --env-file=.env app.js
```

---

## Further

## Reads 📄

- [How Node.js Works Behind the Scenes](https://www.deepintodev.com/blog/how-nodejs-works-behind-the-scenes)

### Videos 🎥

- [JavaScript Automated Testing (Playlist)](https://www.youtube.com/playlist?list=PL0X6fGhFFNTd5_wsAMasuLarx_VSkqYYX)

![Node.js: The Documentary](https://www.youtube.com/watch?v=LB8KwiiUGy0)

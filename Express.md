---
aliases:
    - Express.js
---

## Introduction

- a [[Node]] framework.
- abstracts away a lot of Node.js functionality.

> [!note]
> Express is all about [[middleware]]. Incoming requests are automatically funneled thru functions and processed on the way.
>
> To be able to use multiple middleware functions, the `next()` function needs to be called at the end of each one.

```javascript
const express = require("express");

const app = express();

// Middleware
app.use("/", (req, res, next) => {
	console.log("Always Runs!");
	next();
});

app.use("/express", (req, res, next) => {
	console.log("Middleware!");
	res.send("<h1>Hello, Express!</h1>");
});

app.use("/", (req, res, next) => {
	console.log("Another Middleware!");
	res.send("<h1>Hello, World!</h1>");
});

app.listen(3000);
// OR
// http.createServer(app).listen(3000);
```

- Express doesn't parse request body (`req.body`) by default. We can use parser packages like `body-parser` to achieve that.

```js
const bodyParser = require("body-parser");

// Parses form data
app.use(bodyParser.urlencoded({ extended: false }));

// We can now work with 'req.body'.
```

> [!note]
> To work with specific request methods, we can use `get()`, `post()`, `patch()`, `put()` and `delete()` instead of `use()` (which processes all types of request).

- To serve static files and folders in our code, we need to define them in a middleware using Express' `static()` method.
    - For instance, to import external stylesheets from `~/public/styles/main.css` into our HTML, we can do this:

```js
// ~/app.js
app.use(express.static(path.join(__dirname, "public")));
```

```html
<!-- ~/views/Home.html -->
...
<link rel="stylesheet" href="/styles/main.css" />
...
```

## Routing

- It's considered good practice to make code modular.
- Using routers in Express, we can define routes in separate route files and import them into our main file; as a convention a `~/routes/` directory is used to organize route logic.

```js
// ~/routes/admin.js
const express = require("express");

const router = express.Router();

// matches '/admin/add'
router.get("/add", (req, res, next) => {
	res.send(`
      <form action="/added" method="POST">
        <input type="text" name="user" >
        <button type="submit">submit</button>
      </form>
    `);
});

// matches '/admin/added'
router.post("/added", (req, res, next) => {
	console.log(req.body);
	res.redirect("/");
});

module.exports = router;
```

```js
// ~/app.js
const adminRoutes = require("./routes/admin");

// matches '/add' (GET) & '/added' (POST)
// app.use("/admin", adminRoutes)

// matches '/admin/add' (GET) & '/admin/added' (POST)
app.use("/admin", adminRoutes);
```

> [!important]
> The first argument of `app.use()` is an optional path value. By default, it's value is `/` which acts as a catch-all route. If another route middleware is needed it needs to precede the default catch-all route (`/`), and it shouldn't invoke the `next()` function.
>
> We can also avoid this by matching exact paths using request-specific methods like `app.get()` & `app.post()`.

```javascript
// Should come first
app.use("/admin", adminRoutes);

// Exact match '/' route
app.get("/", (req, res, next) => {
	console.log("Middleware!");
});

// Catch-all route ('/*') for unhandled routes
// Comes after all route handlers
// Can be used to render 404 pages
app.use("/*", (req, res, next) => {
	res.status(404).send("<h1>Page Not Found!</h1>");
});
```

> [!note]
> `res` methods like `setHeader()` & `status()` can be chained together and must come before the `send()` method.

- `sendFile()` can be used to send [[HTML]] files as a response.

```js
app.use("/", (req, res) => {
	res.status(404).sendFile(path.join(__dirname, "pages", "404.html"));
});
```

## Static Files

- Express provides a built-in middleware called `express.static()` to serve static files, which are assets that don't change (such as HTML, CSS, JavaScript, and images).

- To serve files in the `public` directory directly:
    - A file at `public/images/logo.png` will be accessible at `http://localhost:3000/images/logo.png`.

```javascript
const app = express();

app.use(express.static("public"));
// OR Using an Absolute Path
app.use(express.static(path.join(__dirname, "public")));
```

- `express.static()` can be used multiple times to serve files from different directories:

```javascript
app.use(express.static("public"));
app.use(express.static("files"));
```

- To create a virtual path prefix for static files:

```javascript
// Files inside 'public/' will be prefixed with '/static'
app.use("/static", express.static("public"));
```

- Caching using cache headers:

```javascript
app.use(
	express.static("public", {
		maxAge: "1d",
		setHeaders: (res, path, stat) => {
			res.set("X-Custom-Header", "Static File");
		},
	}),
);
```

- By default, Express won't serve `index.html` for the root URL.
    - To serve `index.html` for root requests, add a corresponding route:

```javascript
app.use(express.static("public"));

// Serve index.html for the root route
app.get("/", (req, res) => {
	res.sendFile(path.join(__dirname, "public", "index.html"));
});

// For SPAs
app.get("*", (req, res) => {
	res.sendFile(path.join(__dirname, "public", "index.html"));
});
```

## MVC

![[MVC]]

### Models

- How data is represented in the code.

### Views

- What the user sees and interacts with.
- Templates and [[HTML]] files to be rendered can be stored separately; as a convention a `~/views/` directory is used to organize such files.

```js
// ~/routes/home.js
const path = require("path");

router.get("/", (req, res, next) => {
	res.sendFile(path.join(__dirname, "..", "views", "index.html"));
});
```

- Alternatively, we can create a utility function to return the root directory of an application:

```js
// ~/utils/path.js
const path = require("path");

module.exports = path.dirname(require.main.filename);
```

```js
// ~/routes/home.js
const rootDir = require("../utils/path");

router.get("/", (req, res, next) => {
	res.sendFile(path.join(rootDir, "views", "index.html"));
});
```

> [!important]
> `path.join()` constructs an absolute path to the specified route that works on all operating systems. However, the path arguments passed are relative to the _current file_ (`~/routes/home.js` above).

> [!note]
> Content that can be accessed publicly is stored in the `~/public` directory. It can be used to store static content like [[CSS]] files and static web files like [[robots.txt]].
>
> e.g. If our stylesheet path is `~/public/css/style.css`, there needs to be extra configuration in Express to expose that file to the public: `app.use(express.static(path.join(rootDir, "public")))`. From HTML files, our stylesheet file can be accessed with `/css/style.css`.

### Controllers

- The in-between logic connecting the models and views.
- Routes belong to this category.
- In the context of a Node/Express project, controllers might be split into different middleware functions.

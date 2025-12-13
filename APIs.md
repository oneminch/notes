## Architectures

### RESTful

- Representational State Transfer
	- Defines a set of constraints to be used for creating web services. 
	- Use HTTP methods like GET, POST, PUT, and DELETE to perform operations on resources identified by URIs.
- Allow accessing web services in a simple and flexible way without having any processing.
	- Resource-based and stateless
		- Resources are defined using nouns. e.g., `/users`
- Ideal for web servers
- Benefits
	- Standardization
	- Statelessness
	- Flexibility (of resource or methods)
	- Interoperability

```
GET /api/users/123
```

- A simple REST API in [[Express]]:

```js
let users = [
	{ id: 1, name: "John Doe", email: "john@example.com" },
	{ id: 2, name: "Jane Smith", email: "jane@example.com" },
];

// GET /users - Retrieve all users
app.get("/users", (req, res) => {
	res.json(users);
});

// GET /users/:id - Retrieve a specific user
app.get("/users/:id", (req, res) => {
	const user = users.find((u) => u.id === parseInt(req.params.id));
	
	if (!user) {
		return res.status(404).json({ error: "User not found" });
	}
	
	res.json(user);
});

// POST /users - Create a new user
app.post("/users", (req, res) => {
	const { name, email } = req.body;
	
	const newUser = { id: users.length + 1, name, email };
	users.push(newUser);
	
	res.status(201).json(newUser);
});

// PUT /users/:id - Update an existing user
app.put("/users/:id", (req, res) => {
	const { name, email } = req.body;
	
	const user = users.find((u) => u.id === parseInt(req.params.id));
	
	if (!user) {
		return res.status(404).json({ error: "User not found" });
	}
	
	user.name = name;
	user.email = email;
	
	res.json(user);
});

// DELETE /users/:id - Delete a user
app.delete("/users/:id", (req, res) => {
	const index = users.findIndex((u) => u.id === parseInt(req.params.id));
	
	if (index === -1) {
		return res.status(404).json({ error: "User not found" });
	}
	
	const deletedUser = users.splice(index, 1)[0];
	
	res.json(deletedUser);
});
```

### [[GraphQL]]

- A query language for APIs and a runtime for fulfilling those queries with existing data.
- Protocol-agnostic
- Reduce network load by giving clients the power to ask for exactly what they need.

```
query {
	user(id: "123") {
		name
		email
	}
}
```

### SOAP

- Simple Object Access Protocol
- A protocol specification for exchanging structured information in the implementation of web services in computer networks.
- Use XML-based messages that follow a specific format.
- Ideal for enterprise-level applications that require high security and transactional reliability. 
	- Suitable for private APIs, especially in industries like finance and healthcare, where data integrity and security are important.
- An example request:

```xml
<soap:Envelope 
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:xsd="http://www.w3.org/2001/XMLSchema"
	xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
	<soap:Body>
		<GetUserInfo xmlns="http://example.com/">
			<userId>123</userId>
		</GetUserInfo>
	</soap:Body>
</soap:Envelope>
```

### gRPC

- Google Remote Procedure Call
- High performance
- Ideal for microservices
- Use Protocol Buffers

### [[WebSockets]]

- Bi-directional communication
- Ideal for low-latency data exchange such as real-time / live collaboration applications

### Webhooks

- Asynchronous communication
- Ideal for event-driven applications

## REST API Design

### Principles

- **Uniform Interface**
	- Use standard HTTP methods (GET, POST, PUT, DELETE) and consistent naming conventions for resources. 
	- Each resource should have a unique URI.
- **Client-Server Separation**
	- The client and server should be independent of each other, allowing them to evolve separately.
- **Statelessness**
	- Each request from the client to the server must contain all information needed to understand and complete the request. 
	- The server should not store any client context between requests.
- **Cacheability**
	- Responses should be cacheable when possible to improve performance.
- **Layered System**
	- The API may be composed of multiple layers, but this should be transparent to the client.
- **Resource-Based**
	- Organize APIs around resources, using nouns instead of verbs in endpoint paths.
- **Representations**
	- Resources should have uniform representations in server responses. 
	- Use standard formats like JSON.
- **Self-Descriptive Messages**
	- Each message should include enough information to describe how to process it.
- **Versioning**
	- Implement versioning to manage changes and updates without breaking existing clients.
- **Security**
	- Use SSL/TLS for encryption and implement proper authentication and authorization.
- **Filtering, Sorting, and Pagination**
	- Allow clients to request specific data subsets for better performance and usability.
- **Clear Documentation**
	- Provide comprehensive documentation explaining how to use the API.
- **Consistent Error Handling**
	- Use standard HTTP status codes and provide clear error messages.

### Parameters

- Path parameters - `/products/{id}`
	- Part of a URL path.
	- Typically used to point to a specific resource within a collection, such as a product identified by its `id`.
- Query parameters - `/products?type=laptop`
	- More common.
	- Appear at the end of the request URL after a question mark (`?`).
		- Contain different `key=value` pairs separated by `&`.
	- Can be required and optional.
- Header parameters - `X-MyCustomHeader: Value`
- Cookie parameters - `Cookie: key1=value1; key2=value2`
	- Passed in the Cookie header
	- e.g. `Cookie: debug=0; csrftoken=ERT9dlhP3O1NWwPLC`

## Specifications

### OpenAPI Spec

- The OpenAPI Specification (OAS) is a community-driven open specification that provides a standard, language-agnostic interface to describe RESTful APIs. 
- It describes RESTful APIs in a machine-readable format, enabling a variety of tooling and automation around API development and consumption.
- It is maintained and developed by the OpenAPI Initiative under the Linux Foundation.
- Formerly known as the Swagger Specification.
- Key points:
	- It defines a standard way to describe HTTP APIs, allowing both humans and machines to understand the capabilities of a service without additional documentation or inspection of network traffic.
	- It supports use cases such as interactive documentation, code generation for clients and servers, and automation of test cases facilitating both design-first and code-first API development approaches.
	- It is represented in either YAML or JSON formats and can be produced statically or generated dynamically from an application.

```yaml
openapi: 3.0.0
info:
	title: Sample API
	version: 1.0.0
paths:
	/users:
		get:
			summary: List users
			responses:
				'200':
					description: Successful response
```

#### Swagger

- The tooling ecosystem that helps implement and work with the OpenAPI Specification.
- Includes various tools that help in designing, building, documenting, and consuming REST APIs.
	- Swagger UI - automatically generates interactive API docs
	- Swagger Editor - allows designing and modeling APIs according to the OpenAPI Specification.
	- Swagger Codegen - helps build stable, reusable code for APIs in various languages.

### JSON:API

- A specification for building APIs in JSON, focusing on how to structure API responses and requests.
- Defines a specific JSON structure for API responses and requests.
- Primarily concerned with the format of data exchanged between clients and servers, not the overall API description.

### AsyncAPI Spec

- A standard for describing event-driven APIs, similar to how OpenAPI is used for RESTful APIs.

## Best Practices

### Common Mistakes (API Design)

- Inconsistency or lack of a unified design across platforms.
- Incorrect Use of HTTP Methods.
- Lack of Documentation
	- Insufficient details about endpoints, request parameters, response formats, and error handling.
- Oversharing Data
	- Can expose too much data, and create security vulnerabilities & performance issues.
- Ignoring Scalability
	- Not planning for growth can lead to performance degradation as usage increases.
- Bloated Responses
	- Excessive data in responses increases latency and bandwidth usage.
- Poor Error Handling
	- Vague or confusing error messages complicate troubleshooting for developers.
- Neglecting Versioning
	- Lack of versioning can disrupt existing users with breaking changes.
- Complexity in Design

---
## Further

### Reads 📄

- [API Security Best Practices](https://roadmap.sh/best-practices/api-security)

### Videos 🎥

![API Architecture Patterns](https://www.youtube.com/watch?v=4vLxWqE94l4)

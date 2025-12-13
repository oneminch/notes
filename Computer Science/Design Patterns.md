## Creational Design Patterns

- Solve the fundamental problem of object creation

### Factory Pattern

- Creates objects without specifying their exact class.
- Add overhead but increase flexibility.

- Delegates object creation to a factory function/class rather than using direct constructors.
- Ideal when you need to create different objects based on runtime conditions, like switching between development and production configurations.
- React uses `React.createElement` as a factory. Axios uses factories for different request types. Database ORMs use factories for connection creation.

```javascript
// Simple Factory for API clients
function createApiClient(type) {
	switch (type) {
		case "rest":
			return new RestClient();
		case "graphql":
			return new GraphQLClient();
		case "websocket":
			return new WebSocketClient();
		default:
			throw new Error("Unknown client type");
	}
}
```

- **Web Development Applications**:
    - Component factories in React/Vue frameworks
    - HTTP client creation based on environment
    - Database connection factories
    - Route handler factories in Express

### Singleton Pattern

- Ensures only one instance of a class exists globally.
- Useful for managing shared resources.
- Reduce memory usage but can create bottlenecks.
- Often overused.
- Configuration managers, connection pools, and global state stores often implement Singleton pattern.

```javascript
class DatabaseConnection {
	constructor() {
		if (DatabaseConnection.instance) {
			return DatabaseConnection.instance;
		}
		this.connection = null;
		DatabaseConnection.instance = this;
	}

	connect() {
		if (!this.connection) {
			this.connection = createConnection();
		}
		return this.connection;
	}
}
```

- **Web Development Applications**:
    - Database connection pools
    - Application configuration managers
    - Event buses
    - Cache managers
    - Logger instances

> [!note]
> ES6 modules are natural singletons - a module's exports are cached after first import, providing singleton behavior without the complexity.

### Builder Pattern

- Constructs complex objects step by step.
- Perfect for objects with many optional parameters or complex initialization logic.
- Especially valuable when dealing with APIs that have many optional parameters or when constructing complex configurations.
- Query builders (Knex.js), configuration builders (Webpack config), and component builders (styled-components) all use Builder pattern extensively.

```javascript
class ApiRequestBuilder {
	constructor(url) {
		this.request = { url, headers: {}, params: {} };
	}

	withHeader(key, value) {
		this.request.headers[key] = value;
		return this;
	}

	withParam(key, value) {
		this.request.params[key] = value;
		return this;
	}

	build() {
		return this.request;
	}
}

// Usage
const request = new ApiRequestBuilder("/api/users")
	.withHeader("Authorization", "Bearer token")
	.withParam("page", 1)
	.build();
```

- **Web Development Applications**:
    - Query builders for databases (Knex.js, Prisma)
    - HTTP request builders (axios configurations)
    - Component configuration builders
    - Form validation rule builders
    - CSS-in-JS styled component builders

### Prototype Pattern

- Creates objects by cloning existing instances.
- JavaScript's prototype chain makes this pattern naturally accessible.

```javascript
const userPrototype = {
	init(name, email) {
		this.name = name;
		this.email = email;
		return this;
	},

	clone() {
		return Object.create(this);
	},

	getInfo() {
		return `${this.name} (${this.email})`;
	},
};

const user1 = Object.create(userPrototype).init("John", "john@example.com");
const user2 = user1.clone().init("Jane", "jane@example.com");
```

- **Web Development Applications**:
    - Component template cloning
    - Configuration object templates
    - Default state objects in Redux
    - Canvas drawing object templates
    - Email template systems

### Abstract Factory Pattern

- Provides an interface for creating families of related objects.
- Particularly useful in large applications with multiple environments or themes.

```javascript
class UIComponentFactory {
	createButton() {
		throw new Error("Must implement");
	}
	createInput() {
		throw new Error("Must implement");
	}
}

class MaterialUIFactory extends UIComponentFactory {
	createButton() {
		return new MaterialButton();
	}
	createInput() {
		return new MaterialInput();
	}
}

class BootstrapUIFactory extends UIComponentFactory {
	createButton() {
		return new BootstrapButton();
	}
	createInput() {
		return new BootstrapInput();
	}
}

function createUI(theme) {
	const factory =
		theme === "material"
			? new MaterialUIFactory()
			: new BootstrapUIFactory();

	return {
		button: factory.createButton(),
		input: factory.createInput(),
	};
}
```

- **Web Development Applications**:
    - Multi-theme component libraries
    - Cross-platform development (React Native vs React Web)
    - Environment-specific service creation
    - Database adapter factories
    - Payment processor factories

## Structural Design Patterns

- Deal with object composition - how classes and objects are combined to form larger structures.
- Can add layers of abstraction.
- Fundamental to building scalable, maintainable web applications by defining clean interfaces between components and managing complex relationships.

### Adapter Pattern

- Allows incompatible interfaces to work together.
- It's like a translator between two systems that speak different languages.

- Wrap an existing class with a new interface to make it compatible with client expectations.
- Invaluable when integrating external services or maintaining backward compatibility during refactoring.
- Libraries frequently provide adapters for different environments (React Native adapters, database adapters).
    - API client libraries often adapt third-party services to consistent interfaces.

```javascript
// Third-party payment library with inconsistent interface
class StripeAPI {
	makePayment(amount, currency, card) {
		return { success: true, transactionId: "stripe_123" };
	}
}

class PayPalAPI {
	processPayment(paymentData) {
		return { status: "completed", id: "paypal_456" };
	}
}

// Adapter to normalize interfaces
class PaymentAdapter {
	constructor(paymentService, type) {
		this.service = paymentService;
		this.type = type;
	}

	pay(amount, currency, paymentMethod) {
		if (this.type === "stripe") {
			const result = this.service.makePayment(
				amount,
				currency,
				paymentMethod,
			);

			return {
				success: result.success,
				id: result.transactionId,
			};
		}

		if (this.type === "paypal") {
			const result = this.service.processPayment({
				amount,
				currency,
				paymentMethod,
			});

			return {
				success: result.status === "completed",
				id: result.id,
			};
		}
	}
}
```

- **Web Development Applications**:
    - Third-party API integration (payment processors, analytics services)
    - Legacy system integration with modern frontends
    - Database ORM adapters for different databases
    - Browser API normalization (handling cross-browser differences)
    - GraphQL adapters for REST APIs

### Decorator Pattern

- Adds new functionality to objects dynamically without altering their structure.
- It's composition over inheritance in action.
- HOCs in React, middleware in Express, and plugin systems all use Decorator pattern. Libraries like Lodash provide decorators for function enhancement.

```javascript
// Base HTTP client
class HttpClient {
	async request(url, options) {
		return fetch(url, options);
	}
}

// Decorators
class LoggingDecorator {
	constructor(client) {
		this.client = client;
	}

	async request(url, options) {
		console.log(`Making request to ${url}`);
		const response = await this.client.request(url, options);
		console.log(`Response status: ${response.status}`);
		return response;
	}
}

class AuthDecorator {
	constructor(client, token) {
		this.client = client;
		this.token = token;
	}

	async request(url, options = {}) {
		options.headers = {
			...options.headers,
			Authorization: `Bearer ${this.token}`,
		};
		return this.client.request(url, options);
	}
}

// Usage - stack decorators
const client = new AuthDecorator(
	new LoggingDecorator(new HttpClient()),
	"user-token",
);
```

- **Web Development Applications**:
    - Express.js middleware (each middleware decorates the request/response)
    - React Higher-Order Components (HOCs)
    - Function decorators in TypeScript/ES7
    - API request/response interceptors
    - Component enhancement (adding loading states, error handling)
    - Authentication wrappers

### Facade Pattern

- Provides a simplified interface to a complex subsystem.
- It's like a remote control that hides the complexity of your entertainment system.
- jQuery is a classic facade over DOM manipulation. Modern libraries like Axios provide facades over fetch API complexity.

```javascript
// Complex animation subsystem
class DOMAnimator {
	fadeIn(element) {
		/* complex implementation */
	}
	slideDown(element) {
		/* complex implementation */
	}
}

class CSSAnimator {
	transition(element, properties) {
		/* complex implementation */
	}
}

class AnimationQueue {
	add(animation) {
		/* complex implementation */
	}
	play() {
		/* complex implementation */
	}
}

// Facade - simple interface
class AnimationFacade {
	constructor() {
		this.domAnimator = new DOMAnimator();
		this.cssAnimator = new CSSAnimator();
		this.queue = new AnimationQueue();
	}

	showElement(element, effect = "fade") {
		switch (effect) {
			case "fade":
				this.domAnimator.fadeIn(element);
				break;
			case "slide":
				this.domAnimator.slideDown(element);
				break;
		}
	}

	animateSequence(animations) {
		animations.forEach((anim) => this.queue.add(anim));
		this.queue.play();
	}
}

// Simple usage
const animator = new AnimationFacade();
animator.showElement(document.getElementById("modal"));
```

- **Web Development Applications**:
    - jQuery (facade over complex DOM manipulation)
    - API client libraries (Axios wraps fetch complexities)
    - Build tool interfaces (webpack config facades)
    - Component library interfaces
    - Database query builders
    - Complex form validation libraries

### Composite Pattern

- Treats individual objects and compositions uniformly.
- It's perfect for tree structures and hierarchical data.
- Component libraries (React, Vue) heavily use Composite pattern for nested components. Menu systems and form builders use this pattern.

```javascript
// Base component interface
class UIComponent {
	render() {
		throw new Error("Must implement render");
	}
}

// Leaf components
class Button extends UIComponent {
	constructor(text) {
		super();
		this.text = text;
	}

	render() {
		return `<button>${this.text}</button>`;
	}
}

class Input extends UIComponent {
	constructor(type, placeholder) {
		super();
		this.type = type;
		this.placeholder = placeholder;
	}

	render() {
		return `<input type="${this.type}" placeholder="${this.placeholder}">`;
	}
}

// Composite component
class Container extends UIComponent {
	constructor() {
		super();
		this.children = [];
	}

	add(component) {
		this.children.push(component);
		return this;
	}

	render() {
		const childrenHtml = this.children
			.map((child) => child.render())
			.join("");
		return `<div class="container">${childrenHtml}</div>`;
	}
}

// Usage - build complex UI trees
const form = new Container()
	.add(new Input("email", "Enter email"))
	.add(new Input("password", "Enter password"))
	.add(new Button("Login"));

const page = new Container().add(form).add(new Button("Cancel"));
```

- **Web Development Applications**:
    - React component trees (components can contain other components)
    - Menu systems (nested menus)
    - File system representations
    - Form builders with nested field groups
    - GraphQL schema composition
    - Validation rule composition

### Proxy Pattern

- Provides a placeholder or surrogate that controls access to another object.
- It's like a security guard or cache layer.
- React's Context API uses a form of Proxy pattern.

```javascript
class APIService {
	async getUserData(id) {
		// Expensive API call
		console.log(`Fetching user ${id} from API...`);
		return { id, name: `User ${id}`, email: `user${id}@example.com` };
	}
}

class CachingProxy {
	constructor(apiService) {
		this.apiService = apiService;
		this.cache = new Map();
	}

	async getUserData(id) {
		if (this.cache.has(id)) {
			console.log(`Cache hit for user ${id}`);
			return this.cache.get(id);
		}

		const userData = await this.apiService.getUserData(id);
		this.cache.set(id, userData);
		return userData;
	}
}

class AuthProxy {
	constructor(service, userRole) {
		this.service = service;
		this.userRole = userRole;
	}

	async getUserData(id) {
		if (this.userRole !== "admin" && id !== this.getCurrentUserId()) {
			throw new Error("Access denied");
		}
		return this.service.getUserData(id);
	}
}

// Chain proxies
const service = new AuthProxy(new CachingProxy(new APIService()), "admin");
```

- **Web Development Applications**:
    - API response caching
    - Lazy loading of components/resources
    - Access control and authentication
    - Request/response logging
    - Virtual scrolling implementations
    - Service worker proxies
    - ES6 Proxy for reactive data binding (Vue.js reactivity)

### Bridge Pattern

- Separates an abstraction from its implementation, allowing both to vary independently.
- It's particularly useful for cross-platform development.

```javascript
// Implementation side - different rendering engines
class WebGLRenderer {
	render(shape) {
		console.log(`Rendering ${shape} with WebGL`);
	}
}

class CanvasRenderer {
	render(shape) {
		console.log(`Rendering ${shape} with Canvas 2D`);
	}
}

class SVGRenderer {
	render(shape) {
		console.log(`Rendering ${shape} with SVG`);
	}
}

// Abstraction side - shapes
class Shape {
	constructor(renderer) {
		this.renderer = renderer;
	}

	draw() {
		this.renderer.render(this.constructor.name);
	}
}

class Circle extends Shape {
	constructor(renderer, radius) {
		super(renderer);
		this.radius = radius;
	}
}

class Rectangle extends Shape {
	constructor(renderer, width, height) {
		super(renderer);
		this.width = width;
		this.height = height;
	}
}

// Usage - mix and match
const webglCircle = new Circle(new WebGLRenderer(), 10);
const canvasRect = new Rectangle(new CanvasRenderer(), 20, 30);
```

- **Web Development Applications**:
    - Cross-browser compatibility layers
    - Multi-platform UI frameworks (React Native vs React Web)
    - Different storage implementations (localStorage, sessionStorage, IndexedDB)
    - Various authentication providers (OAuth, JWT, SAML)
    - Multiple deployment targets (serverless, containers, traditional servers)

## Behavioral Design Patterns

- Focus on communication between objects and the assignment of responsibilities.
- Define how objects interact and collaborate, making complex workflows manageable and systems more flexible.

### Observer Pattern

- Defines a one-to-many dependency between objects.
- When one object changes state, all dependents are notified automatically.
    - Publishers notify subscribers about events without tight coupling.
- Event systems (Node.js `EventEmitter`, DOM events), state management libraries (Redux, MobX), and reactive libraries (RxJS) all implement Observer pattern.

```javascript
class EventEmitter {
	constructor() {
		this.events = {};
	}

	on(event, callback) {
		if (!this.events[event]) {
			this.events[event] = [];
		}
		this.events[event].push(callback);
	}

	emit(event, data) {
		if (this.events[event]) {
			this.events[event].forEach((callback) => callback(data));
		}
	}

	off(event, callback) {
		if (this.events[event]) {
			this.events[event] = this.events[event].filter(
				(cb) => cb !== callback,
			);
		}
	}
}

// Usage in a shopping cart
class ShoppingCart extends EventEmitter {
	constructor() {
		super();
		this.items = [];
	}

	addItem(item) {
		this.items.push(item);
		this.emit("itemAdded", { item, total: this.items.length });
	}
}

const cart = new ShoppingCart();
cart.on("itemAdded", (data) => console.log(`Item added! Total: ${data.total}`));
cart.on("itemAdded", (data) => updateUI(data));
```

- **Web Development Applications**:
    - DOM event handling
    - State management (Redux, MobX)
    - Real-time updates (WebSocket connections)
    - Component lifecycle events
    - Model-View architectures
    - Custom event systems

### Strategy Pattern

- Defines a family of algorithms, encapsulates each one, and makes them interchangeable at runtime.
- Validation libraries, sorting libraries, and authentication libraries provide strategy patterns for different algorithms.

```javascript
// Payment strategies
class CreditCardStrategy {
	pay(amount) {
		return `Paid $${amount} using Credit Card`;
	}
}

class PayPalStrategy {
	pay(amount) {
		return `Paid $${amount} using PayPal`;
	}
}

class CryptoStrategy {
	pay(amount) {
		return `Paid $${amount} using Bitcoin`;
	}
}

class PaymentProcessor {
	constructor(strategy) {
		this.strategy = strategy;
	}

	setStrategy(strategy) {
		this.strategy = strategy;
	}

	processPayment(amount) {
		return this.strategy.pay(amount);
	}
}

// Dynamic strategy switching
const processor = new PaymentProcessor(new CreditCardStrategy());
console.log(processor.processPayment(100));

processor.setStrategy(new PayPalStrategy());
console.log(processor.processPayment(50));
```

- **Web Development Applications**:
    - Form validation strategies (email, phone, custom)
    - Sorting algorithms in data tables
    - Authentication strategies (OAuth, JWT, Session)
    - Caching strategies (memory, localStorage, Redis)
    - Image loading strategies (lazy, eager, progressive)
    - API request strategies (REST, GraphQL, gRPC)

### Command Pattern

- Encapsulates a request as an object, allowing you to parameterize clients, queue operations, and support undo functionality.
- Undo/redo systems, task queues, and action dispatchers in state management libraries use Command pattern.

```javascript
// Commands for text editor
class Command {
	execute() {
		throw new Error("Must implement execute");
	}
	undo() {
		throw new Error("Must implement undo");
	}
}

class InsertTextCommand extends Command {
	constructor(editor, text, position) {
		super();
		this.editor = editor;
		this.text = text;
		this.position = position;
	}

	execute() {
		this.editor.insertText(this.text, this.position);
	}

	undo() {
		this.editor.deleteText(this.position, this.text.length);
	}
}

class DeleteTextCommand extends Command {
	constructor(editor, position, length) {
		super();
		this.editor = editor;
		this.position = position;
		this.length = length;
		this.deletedText = "";
	}

	execute() {
		this.deletedText = this.editor.deleteText(this.position, this.length);
	}

	undo() {
		this.editor.insertText(this.deletedText, this.position);
	}
}

class EditorInvoker {
	constructor() {
		this.history = [];
		this.currentPosition = -1;
	}

	executeCommand(command) {
		// Remove any commands after current position
		this.history = this.history.slice(0, this.currentPosition + 1);
		this.history.push(command);
		this.currentPosition++;
		command.execute();
	}

	undo() {
		if (this.currentPosition >= 0) {
			const command = this.history[this.currentPosition];
			command.undo();
			this.currentPosition--;
		}
	}

	redo() {
		if (this.currentPosition < this.history.length - 1) {
			this.currentPosition++;
			const command = this.history[this.currentPosition];
			command.execute();
		}
	}
}
```

- **Web Development Applications**:
    - Undo/redo functionality in editors
    - Queue management for API requests
    - Macro recording in applications
    - Transaction management
    - Request/response middleware chains
    - UI action history for debugging

### State Pattern

- Allows an object to alter its behavior when its internal state changes.
    - The object appears to change its class.

```javascript
// Connection states
class ConnectionState {
	connect(connection) {
		throw new Error("Must implement");
	}
	disconnect(connection) {
		throw new Error("Must implement");
	}
	send(connection, data) {
		throw new Error("Must implement");
	}
}

class DisconnectedState extends ConnectionState {
	connect(connection) {
		console.log("Connecting...");
		connection.setState(new ConnectingState());
	}

	send(connection, data) {
		console.log("Cannot send - not connected");
	}
}

class ConnectingState extends ConnectionState {
	connect(connection) {
		console.log("Already connecting...");
	}

	disconnect(connection) {
		console.log("Canceling connection...");
		connection.setState(new DisconnectedState());
	}

	// Simulate successful connection
	onConnected(connection) {
		connection.setState(new ConnectedState());
	}
}

class ConnectedState extends ConnectionState {
	disconnect(connection) {
		console.log("Disconnecting...");
		connection.setState(new DisconnectedState());
	}

	send(connection, data) {
		console.log(`Sending: ${data}`);
	}
}

class WebSocketConnection {
	constructor() {
		this.state = new DisconnectedState();
	}

	setState(state) {
		this.state = state;
	}

	connect() {
		this.state.connect(this);
	}
	disconnect() {
		this.state.disconnect(this);
	}
	send(data) {
		this.state.send(this, data);
	}
}
```

- **Web Development Applications**:
    - WebSocket connection management
    - Form wizard steps
    - Game states (menu, playing, paused, game over)
    - Media player states (loading, playing, paused, stopped)
    - HTTP request states (pending, fulfilled, rejected)
    - Component lifecycle states

### Template Method Pattern

- Defines the skeleton of an algorithm in a base class, letting subclasses override specific steps without changing the algorithm's structure.
- Testing frameworks (Jest, Mocha), build tools (webpack loaders), and component lifecycle methods use Template Method pattern.

```javascript
class DataProcessor {
	// Template method
	process(data) {
		const cleaned = this.cleanData(data);
		const transformed = this.transformData(cleaned);
		const validated = this.validateData(transformed);
		return this.saveData(validated);
	}

	// Default implementations
	cleanData(data) {
		return data.filter((item) => item != null);
	}

	// Abstract methods - must be implemented by subclasses
	transformData(data) {
		throw new Error("Must implement transformData");
	}
	validateData(data) {
		throw new Error("Must implement validateData");
	}
	saveData(data) {
		throw new Error("Must implement saveData");
	}
}

class UserDataProcessor extends DataProcessor {
	transformData(data) {
		return data.map((user) => ({
			...user,
			email: user.email.toLowerCase(),
			name: user.name.trim(),
		}));
	}

	validateData(data) {
		return data.filter(
			(user) => user.email.includes("@") && user.name.length > 0,
		);
	}

	saveData(data) {
		console.log(`Saving ${data.length} users to database`);
		return data;
	}
}

class ProductDataProcessor extends DataProcessor {
	transformData(data) {
		return data.map((product) => ({
			...product,
			price: parseFloat(product.price),
			name: product.name.toUpperCase(),
		}));
	}

	validateData(data) {
		return data.filter(
			(product) => product.price > 0 && product.name.length > 0,
		);
	}

	saveData(data) {
		console.log(`Saving ${data.length} products to inventory`);
		return data;
	}
}
```

- **Web Development Applications**:
    - React component lifecycle methods
    - Express.js middleware chains
    - Build process pipelines
    - Data processing pipelines
    - Testing framework hooks (setup, test, teardown)
    - Authentication flows

### Chain of Responsibility Pattern

- Passes requests along a chain of handlers.
    - Each handler decides either to process the request or pass it to the next handler.

```javascript
class Handler {
	constructor() {
		this.nextHandler = null;
	}

	setNext(handler) {
		this.nextHandler = handler;
		return handler; // Allow chaining
	}

	handle(request) {
		if (this.canHandle(request)) {
			return this.processRequest(request);
		}

		if (this.nextHandler) {
			return this.nextHandler.handle(request);
		}

		throw new Error("No handler found for request");
	}

	canHandle(request) {
		throw new Error("Must implement canHandle");
	}

	processRequest(request) {
		throw new Error("Must implement processRequest");
	}
}

class AuthHandler extends Handler {
	canHandle(request) {
		return !request.isAuthenticated;
	}

	processRequest(request) {
		console.log("Authenticating user...");
		request.isAuthenticated = true;

		return this.nextHandler ? this.nextHandler.handle(request) : request;
	}
}

class ValidationHandler extends Handler {
	canHandle(request) {
		return request.isAuthenticated && !request.isValid;
	}

	processRequest(request) {
		console.log("Validating request...");
		request.isValid = request.data && request.data.length > 0;
		if (!request.isValid) {
			throw new Error("Invalid request data");
		}
		return this.nextHandler ? this.nextHandler.handle(request) : request;
	}
}

class ProcessingHandler extends Handler {
	canHandle(request) {
		return request.isAuthenticated && request.isValid;
	}

	processRequest(request) {
		console.log("Processing request...");
		request.result = `Processed: ${request.data}`;
		return request;
	}
}

// Setup chain
const authHandler = new AuthHandler();
const validationHandler = new ValidationHandler();
const processingHandler = new ProcessingHandler();

authHandler.setNext(validationHandler).setNext(processingHandler);
```

- **Web Development Applications**:
    - Express.js middleware
    - Request/response interceptors
    - Event bubbling in DOM
    - Error handling chains
    - Form validation chains
    - Authorization level checks

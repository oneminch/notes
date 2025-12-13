- A protocol that provides full-duplex communication channels over a single TCP connection.
- Allows for persistent, bidirectional, real-time data exchange between a client (typically a web browser) and a server without the need for constant [[polling]].
- Reduces overhead compared to traditional HTTP requests.
- Ideal for applications requiring instant data updates.
- Can be secured using the WSS (WebSocket Secure) protocol, similar to HTTPS.
- **Benefits**
    - Reduced latency due to fewer handshakes.
    - Lower overhead as headers are sent only once during the initial handshake.
    - Real-time data transfer without [[polling]].
- **How It Works**
    - WebSocket connections start with an HTTP handshake, where the client requests to upgrade the connection to WebSocket (using the `Upgrade` header).
        - If the server supports WebSockets, it responds with an HTTP 101 status code, indicating that the protocol is being switched.
        - Once the server agrees, the connection is upgraded, and both parties can send data simultaneously.
        - Data is exchanged in the form of "frames," which can carry text or binary data and can be sent continuously.
    - After the connection is established, both the client and server can send messages to each other at any time.
    - The connection remains open until either the client or server decides to close it.
        - Allows for long-lived connections, ideal for applications requiring constant updates.
        - Either party can initiate a connection closure. The connection is gracefully closed after both sides have exchanged close frames.

```javascript
// Create a new WebSocket connection
const socket = new WebSocket("ws://example.com/socketserver");

// Connection opened
socket.addEventListener("open", (event) => {
	socket.send("Hello Server!");
});

// Listen for messages
socket.addEventListener("message", (event) => {
	console.log("Message from server:", event.data);
});
```

> [!note]
> Connections are stateful, which can present challenges when scaling applications.

- **Use Cases**
    - Chat applications
    - Live sports updates
    - Real-time gaming
    - Collaborative editing tools
    - Financial trading platforms

## Example

```js
// server.js
const WebSocket = require("ws");
// Note: Node now comes with a native WebSocket client

const app = express();
const server = http.createServer(app);
const wss = new WebSocket.Server({ server });

const clients = new Set();

wss.on("connection", (ws) => {
	// Client Connected
	clients.add(ws);

	ws.on("message", (message) => {
		console.log("Received:", message);
		// Broadcast the message to all connected clients
		clients.forEach((client) => {
			if (client.readyState === WebSocket.OPEN) {
				client.send(message);
			}
		});
	});

	// Client Disconnected
	ws.on("close", () => {
		clients.delete(ws);
	});
});
```

```jsx
// App.jsx
function App() {
	const [messages, setMessages] = useState([]);
	const [inputMessage, setInputMessage] = useState("");
	const socketRef = useRef(null);

	useEffect(() => {
		socketRef.current = new WebSocket("ws://localhost:3001");

		socketRef.current.onopen = () => {
			console.log("WebSocket Connection Established");
		};

		socketRef.current.onmessage = (event) => {
			const message = JSON.parse(event.data);
			setMessages((prevMessages) => [...prevMessages, message]);
		};

		socketRef.current.onclose = () => {
			console.log("WebSocket Connection Closed");
		};

		return () => {
			socketRef.current.close();
		};
	}, []);

	const sendMessage = (e) => {
		e.preventDefault();
		if (inputMessage && socketRef.current.readyState === WebSocket.OPEN) {
			const message = {
				text: inputMessage,
				timestamp: new Date().toISOString(),
			};
			socketRef.current.send(JSON.stringify(message));
			setInputMessage("");
		}
	};

	return (
		<>
			<div>
				{messages.map((msg, index) => (
					<div key={index} className="message">
						<span className="timestamp">
							{new Date(msg.timestamp).toLocaleTimeString()}
						</span>
						<span className="text">{msg.text}</span>
					</div>
				))}
			</div>
			<form onSubmit={sendMessage}>
				<input
					type="text"
					value={inputMessage}
					onChange={(e) => setInputMessage(e.target.value)}
					placeholder="Type a message..."
				/>
				<button type="submit">Send</button>
			</form>
		</>
	);
}
```

## Best Practices

- Always use the encrypted `wss://` (WebSocket Secure) protocol instead of `ws://` to prevent man-in-middle attacks and ensure data confidentiality.
- Implement strict input validation on both client and server sides to prevent injection attacks, including XSS and SQL injection.
- Implement robust authentication mechanisms, such as token-based authentication, and ensure proper authorization checks for WebSocket connections.
- Validate the `Origin` header to prevent unauthorized cross-origin connections.
- Implement rate limiting to protect against DoS attacks and excessive resource consumption.

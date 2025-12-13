---
alias: SSE
---

- Allows servers to push data to clients in real-time over a single [[HTTP]] connection.
- Useful for providing real-time data updates.
    - Live news / social media feeds
    - Live sports scores
    - System monitoring and logging
    - Progress updates
    - Notification systems
- **Features**
    - _Unidirectional_ - Data flows only from server to client.
    - _Auto-reconnection_ - Browsers automatically try to reconnect if the connection is lost.
    - _Event IDs_ - Allows resuming the event stream from where it left off.
    - _Standard-based_ - Uses regular HTTP, making it firewall-friendly.
- **How It Works**
    - Client establishes a connection to Server using the `EventSource` API.
    - Server keeps the connection open and sends events as they occur.
    - Client receives these events in real-time without having to poll the server.

> [!note] Flushing Headers
> For streaming responses (large responses or server-sent events), flushing headers enables the client to start receiving data incrementally.
>
> In [[Express]], `flushHeaders` is used to immediately send the HTTP headers to the client before sending the response body.

```js
// Server
const app = express();

app.get("/events", (req, res) => {
	res.writeHead(200, {
		"Content-Type": "text/event-stream",
		"Cache-Control": "no-cache",
		Connection: "keep-alive",
		"Access-Control-Allow-Origin": "http://localhost:5173",
	});
	res.flushHeaders();

	const sendEvent = (data, eventType = "message", id = null) => {
		let eventString = `event: ${eventType}\n`;
		if (id) eventString += `id: ${id}\n`;
		eventString += `data: ${JSON.stringify(data)}\n\n`;

		res.write(eventString);
	};

	// Send an event every 5 seconds
	const intervalId = setInterval(() => {
		const timestamp = new Date().toISOString();
		sendEvent({ status: "update" }, "update", timestamp);
	}, 5000);

	// Clean up on client disconnect
	req.on("close", () => {
		clearInterval(intervalId);
		res.end();
	});
});
```

```js
// Client
const eventSource = new EventSource("/events");

eventSource.onmessage = (event) => {
	const data = JSON.parse(event.data);
	console.log("Received event:", data);
};

eventSource.onerror = (error) => {
	console.error("EventSource failed:", error);
	eventSource.close();
};
```

- **Limitations**
    - No built-in binary support.
    - 6 concurrent connections per browser/domain.
    - Unidirectional (Server to Client only)
- **Best Practices**
    - Use [[HTTP|HTTPS]].
    - Implement proper [[Auth]] & [[Auth#Authorization (AuthZ)|authorization]].

## Examples

### React Custom Hook

```ts
export default function useEventSource(url) {
	const eventSrc = useRef(null);
	const [eventData, setEventData] = useState(0);
	const [isStopped, setIsStopped] = useState(false);

	const createEventSource = () => {
		if (eventSrc.current) return;

		eventSrc.current = new EventSource(url);

		eventSrc.current.onmessage = (event) => {
			setEventData(JSON.parse(event.data));
		};

		eventSrc.current.onerror = (err) => {
			console.error("SSE Error: ", err);
			eventSrc.current.close();
		};

		setIsStopped(false);
	};

	const closeConnection = () => {
		if (eventSrc.current) {
			eventSrc.current.close();
			eventSrc.current = null;
		}
		setIsStopped(true);
	};

	const restartConnection = () => {
		closeConnection();
		createEventSource();
	};

	useEffect(() => {
		createEventSource();

		return () => {
			closeConnection();
		};
	}, [url]);

	return { eventData, restartConnection, closeConnection, isStopped };
}
```

### Vue Composable

```ts
export const useEventSource = (url: string) => {
	const eventSrc = ref<EventSource | null>(null);
	const eventData = ref(0);
	const isStopped = ref(false);

	const createEventSource = () => {
		if (eventSrc.value) return;

		eventSrc.value = new EventSource(url);

		eventSrc.value.onmessage = (event) => {
			eventData.value = JSON.parse(event.data);
		};

		eventSrc.value.onerror = (err) => {
			console.error("SSE Error: ", err);
			eventSrc.value?.close();
		};

		isStopped.value = false;
	};

	const closeConnection = () => {
		if (eventSrc.value) {
			eventSrc.value.close();
			eventSrc.value = null;
		}
		isStopped.value = true;
	};

	const restartConnection = () => {
		closeConnection();
		createEventSource();
	};

	onMounted(() => {
		createEventSource();
	});

	onUnmounted(() => {
		closeConnection();
	});

	return { eventData, restartConnection, closeConnection, isStopped };
};
```

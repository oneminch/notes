---
alias: Workers
---

- Serverless functions that run on Cloudflare's global edge network.
- Utilize the V8 JavaScript engine and operate similar to Node.js.
- Used to build and deploy code closer to users for better speed and reliability.
- Common use cases for Cloudflare Workers:
    - edge caching, 
    - image optimization, 
    - redirects, and
    - rate limiting.

## Bindings

- Bindings allow Workers to interact with resources on the Cloudflare Developer Platform, providing better performance and fewer restrictions than REST APIs. 
- They basically provide zero-trust resource access.
- They combine permission and API in one piece - the underlying secret is never exposed to your Worker's code.
- Available Bindings
	- **Storage**: KV, R2, D1, Durable Objects
	- **Compute**: Service bindings, Workers AI, Browser Rendering
	- **Data**: Vectorize, Analytics Engine, Queues, Workflows
	- **Infrastructure**: Hyperdrive, Assets, mTLS, Rate Limiting
- Configuration in `wrangler.toml` / `wrangler.jsonc`:

```toml
[[r2_buckets]]
binding = "MY_BUCKET"
bucket_name = "my-production-bucket"

[[kv_namespaces]]
binding = "MY_KV"
id = "abc123..."
```

```typescript
export default {
	async fetch(request, env, ctx) {
		// env.MY_BUCKET and env.MY_KV are now available
		await env.MY_BUCKET.put(key, value);
		const data = await env.MY_KV.get(key);
	}
}
```

- `env` can be imported from `cloudflare:workers` to access bindings within a worker either in top-level global scope or `fetch()` handler.

```typescript
import { env } from "cloudflare:workers";
```

> [!note]
> Workers do not allow I/O from outside a *request context*. While environment variables and secrets can be accessed in top-level scope, calling methods on Durable Object stubs, making KV calls, or calling other Workers will not work.

### Durable Objects

- Provides *shared state* in Workers' stateless architecture.
- Runs on exactly one machine in Cloudflare's network at a time.
- Can save data that survives restarts.
- Handles requests one at a time.
- Keeps real-time connections alive (via [[WebSockets]]).

```ts
// Example
export class ChatRoom extends DurableObject {
	constructor(ctx, env) {
		super(ctx, env);
		// Active WebSocket connections
		this.connections = new Set(); 
	}
	
	async fetch(request) {
		// Upgrade to WebSocket
		if (request.headers.get('Upgrade') === 'websocket') {
			const pair = new WebSocketPair();
			const [client, server] = Object.values(pair);
			
			// Store this connection
			this.connections.add(server);
			
			server.addEventListener('message', async (msg) => {
				// When someone sends a message, broadcast it
				for (let conn of this.connections) {
					conn.send(msg.data);
				}
			
				// And save it to history
				await this.ctx.storage.put('msg-' + Date.now(), msg.data);
			});
			
			server.addEventListener('close', () => {
				this.connections.delete(server);
			});
			
			server.accept();
			
			return new Response(null, { 
				status: 101, 
				webSocket: client 
			});
		}

		// Regular HTTP: return message history
		const messages = await this.ctx.storage.list({ prefix: 'msg-' });
		return new Response(JSON.stringify([...messages.values()]));
	}
}

/* --- Worker --- */
export default {
	async fetch(request, env) {
		const url = new URL(request.url);
		
		// e.g., "/room-123" -> "room-123"
		const roomName = url.pathname.slice(1); 
		
		// Everyone connecting to "/room-123" 
		// gets the SAME Durable Object
		const id = env.CHAT_ROOM.idFromName(roomName);
		const room = env.CHAT_ROOM.get(id);
		
		return room.fetch(request);
	}
}
```

- `idFromName(id)` always routes to the same physical Durable Object instance.

## Architecture

- The Workers runtime uses Chrome's V8 JavaScript engine and implements web platform APIs for code reuse across client and server.
- Rather than using containerized processes, Workers uses V8 isolates - lightweight execution contexts that run within an existing environment. 
	- A single runtime instance can run hundreds or thousands of isolates, seamlessly switching between them, with each isolate's memory completely isolated.
	- In traditional serverless (Lambda, etc.):
		- Each function runs in its own containerized process
		- Cold start = boot entire container + language runtime
		- ~100-500ms cold starts
		- Memory overhead of ~35MB+ per container
	- In Workers:
		- Isolates start around a hundred times faster than a Node process on a container, consuming an order of magnitude less memory on startup
		- Workers pays the overhead of a JavaScript runtime once on the start of a container, then runs essentially limitless scripts with almost no individual overhead
		- Cold starts < 5ms
		- Memory overhead of ~1-2MB per isolate

### How It Works

- When a request hits a `workers.dev` subdomain or Cloudflare-managed domain, it invokes the `fetch()` handler defined in your Worker code.

```typescript
export default {
  async fetch(request, env, ctx) {
    // request: incoming HTTP request
    // env: bindings to platform resources
    // ctx: execution context for waitUntil, passThroughOnException
    return new Response('Hello World!');
  }
}
```

- A single Workers instance may handle multiple requests including concurrent requests in a single-threaded event loop. 
	- Other requests may be processed during awaiting any async tasks if other requests come in while processing a request.

> [!important]
> - Mutable state shouldn't be stored in the global scope.
> - There are no guarantees that two requests hit the same isolate instance.

### Runtime APIs

- The Workers runtime is designed to be JavaScript standards compliant and web-interoperable, using web platform APIs wherever possible so code can be reused across client, server, and WinterCG JavaScript runtimes.
	- **Web Standards:** Fetch API (Request, Response, Headers), Streams API, etc.
	- **Workers-Specific:** Cache API (accessing Cloudflare's CDN cache), `WebSockets`, Scheduled events (cron triggers), etc.
	- **Node.js Compatibility:** Workers support a substantial subset of Node APIs.

### Security Model

- **V8 Isolates** - Primary isolation boundary
- **Linux namespaces + seccomp** - Process-level sandboxing with an empty filesystem and all filesystem-related system calls blocked via `seccomp`. 
	- Network access is totally prohibited - communication only happens over local UNIX domain sockets
- **Capability-based APIs** - Using Cap'n Proto RPC for controlled resource access
- **Spectre Mitigation** - Workers is designed to make it impossible for code to measure its own execution time locally - `Date.now()` is locked in place during execution, and no concurrency is provided

## Examples

### Simple Web Page

```ts
export default {
    fetch(request: Request) {
        const html = `<!DOCTYPE html>
            <html>
                <body>
                    <h1>Hello, World!</h1>
                </body>
            </html>`;

        return new Response(html, {
            headers: {
                "content-type": "text/html;charset=UTF-8",
            },
        });
    },
} satisfies ExportedHandler;
```

### Redirect Service

```ts
export default {
    fetch(request: Request) {
        const to = "https://minch.dev";
        
        return Response.redirect(to, 301);
    },
} satisfies ExportedHandler;
```

### AI Text Generation

```ts
import { Ai } from "@cloudflare/ai";

export interface Env {
  AI: Ai;
}

export default {
    async fetch(request: Request, env: Env) {
        const ai = new Ai(env.AI);
        const genAiModel = "@cf/meta/llama-2-7b-chat-fp16";
        
        const url = new URL(request.url);
        const input = url.searchParams.get("query");

        if (!input.trim()) {
            input = "What is Cloudflare Workers?";
        }
        
        const messages = [
            { role: "system", content: "You are a friendly assistant" },
            { role: "user", content: input },
        ];
        
        const stream = await ai.run(genAiModel, {
            messages,
            stream: true,
        });
        
        return new Response(stream, {
            headers: { "content-type": "text/event-stream" },
        });
    },
};
```

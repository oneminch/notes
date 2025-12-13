---
alias: Vue.js
---

> [!question]- Vue / Nuxt Questions
> **Nuxt**  
> - How does Nuxt make use of async components and suspense?
> - Are all server middleware global? Can they be applied to specific routes like route middleware?
> - What is the lifecycle of a Nuxt app?

## Setup

**Scaffolding a Starter Vue App**

```bash
npm create vue@latest
# OR
pnpm create vue@latest
```

**Single HTML File**

```html
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app"></div>

<script type="module">
    const { createApp } = Vue

    createApp({
	    // With Options API
        data() {
            return { count: 0 };
		},
		methods: {
			increment() {
				this.count += 1;
			},
		},
	    // Or With Composition API
	    setup() {
		    const count = ref(0);
		    
			const increment = () => {
				count.value += 1;
			}
		    
		    return { count }
	    },
        template: `
            <div>
                <button v-on:click="increment">+</button>
                <p>Count: {{ count }}</p>
            </div>
        `
    }).mount('#app')
</script>
```

**HTML + JS / SFC (`.vue`)**

```html
<!-- index.html -->
<script src="https://unpkg.com/vue@3/dist/vue.global.js"></script>

<div id="app"></div>

<script type="module">
    import MyCounter from "./my-counter.js"; // or ./my-counter-sfc.vue
    const { createApp } = Vue;
    
    createApp(MyCounter).mount("#app");
</script>
```

```js
// my-counter.js
export default {
    data() {
        return { count: 0 };
	},
	methods: {
		increment() {
			this.count += 1;
		},
	},
    template: `
        <div>
            <button v-on:click="increment">+</button>
            <p>Count: {{ count }}</p>
        </div>
    `
};
```

## Reactivity

### `ref()`

- Tracks a single value (primitive, object, or array).
- Requires `.value` to access/store the value.
- Deeply reactive if given objects/arrays.
- Use for simple value reactivity.
- Like a reactive `let`.
- **Use cases**: numbers, strings, booleans, or for reactive objects when you want to reassign/replace the whole object.

```js
const count = ref(0);
count.value++;

const user = ref({ name: 'Alice' })
user.value.name = 'Bob'             // Change property
user.value = { name: 'Charlie' }    // Reassign whole object ✅
```

### `shallowRef()`

- Only tracks changes to the reference of the value, not its nested properties.
- Use `.value`, like with `ref()`.
- Updating a property (e.g., `count.value.count = 2`) does NOT trigger reactivity; you must assign a new object reference.
- **Use cases**: optimization for large objects, external libraries, or when only reference changes matter.

```js
const state = shallowRef({ count: 1 });
state.value.count = 2;      // NO reactivity
state.value = { count: 2 }; // Triggers reactivity
```

### `reactive()`

- Makes all nested properties of an object/array deeply reactive.
- No `.value` needed; operates directly on the object.
- Cannot work with primitives.
- Loses reactivity if the whole object is reassigned (e.g. `user = { id: 2 }`).
- Not destructure-friendly.
- Like a reactive `const`.
- **Use cases**: complex objects/arrays with multiple properties when you don't need to reassign the whole object or when mutating objects.

```js
const state = reactive({ count: 0 });
state.count++; // direct property access

const user = reactive({ name: 'Alice' })
user.name = 'Bob'            // Change property ✅
user = { name: 'Charlie' }   // Loses reactivity! ⛔
```

### `shallowReactive()`

- Only the object’s first-level properties are reactive; nested objects are NOT reactive.
- No `.value` needed.
- **Use cases**: objects where only top-level property changes should trigger updates.

```js
const state = shallowReactive({ nested: { x: 5 } });
state.nested.x = 10;       // NO reactivity for nested
state.nested = { x: 20 };  // Triggers reactivity for root
```

### `readonly()`

- Creates a reactive object that can't be modified.
- **Use cases**: Pass data to child components that shouldn't modify it.

```js
const original = reactive({ count: 0 })
const copy = readonly(original)

original.count = 10  // ✅ Works
copy.count = 5       // ⛔ Warning: readonly!
```

### Destructuring State

- **Noteworthy** - [[Destructuring Reactive State in Vue]]

## Styling

### `class`

```vue
<script setup>
const item = ref({
    label: "Hello, Vue!",
    isHeading: true,
    isImportant: true
});
</script>

<template>
    <p 
        class="static-class"
        :class="{
            'font-bold': item.isImportant,
            'text-xl': item.isHeading
        }">
        {{ item.label }}
    </p>
    <!-- OR -->
    <p
        :class="[
            'static-class',
            { 'font-bold': item.isImportant },
            { 'text-xl': item.isHeading }
        ]">
        {{ item.label }}
    </p>
    <!-- OR -->
    <p
        :class="[
            'static-class',
            headingClass,
            item.isImportant ? imptClass : '',
            // OR
            { [imptClass]: item.isImportant }
        ]">
        {{ item.label }}
    </p>
</template>
```

- Values passed to `class` on a component with a single root element are added to the component's root element and merged with any existing classes.
- For components with multiple root elements, the `$attrs` component property can be used to define which element will receive this class.

```vue
<!-- <Text> SFC using $attrs (fallthrough attributes): -->
<p :class="$attrs.class">Hi!</p>
<span>This is a child component</span>

<!-- Pass class to component: -->
<Text class="baz" />

<!-- Renders: -->
<p class="baz">Hi!</p>
<span>This is a child component</span>
```

### `style`

```vue
<script setup>
const styleObject = reactive({
    color: 'red',
    fontSize: '30px',
    'font-family': 'Inter'
});
</script>

<template>
    <div :style="styleObject"></div>
</template>
```

- An array of multiple style objects can also be used. Vue will merge the objects and apply them to the same element.

```vue
<h1 :style="[baseStyles, overridingStyles]"></h1>
```

> [!note] Auto-prefixing
> Vue automatically adds vendor prefixes for CSS properties that require it. It checks for browser support at runtime to achieve this.

## Conditional Rendering

- When using `v-if`/`v-else-if`/`v-else` on a `<template>` element, it serves as an invisible wrapper. The `<template>` element will not be included in the final rendered result.

```vue
<template v-if="isVisible">
  <h1>Heading</h1>
  <p>Lorem Ipsum ...</p>
</template>
```

### `v-if` vs. `v-show`

- `v-if` ensures that event listeners and child components are properly destroyed and re-created based on the condition provided. 
    - The conditional block is only rendered when the condition is true for the first time.
    - It has higher toggle costs.
    - Prefer `v-if` for conditions that are unlikely to change at runtime.
- With `v-show`, an element will always be rendered and remain in the [[DOM]]. 
    - Using the directive only toggles the CSS `display` property.
    - It doesn't support the `<template>` element.
    - It has higher initial render costs.
    - Prefer `v-show` for frequent toggles.

## List Rendering

```vue
<div v-for="item in items" :key="item"></div>

<!-- Alias for 'in' -->
<div v-for="item of items" :key="item"></div>

<!-- Iterate on a range (from 1 to 5) -->
<span v-for="n in 5" :key="n">{{ n }}</span>

<!-- With a Component -->
<BlogPost v-for="item in items" :key="item.id" />
```

- `v-for` can be used to iterate through properties of an object.

```vue
<script setup>
const myObject = reactive({
    foo: 'bar',
    baz: 'boo'
})
</script>

<template>
    <ul>
        <li v-for="(value, key, index) in myObject" :key="key">
          {{ index }}. {{ key }}: {{ value }}
        </li>
    </ul>
</template>
```

- Similar to `v-if`, `<template>` can be used with `v-for` to render a block of multiple elements.

> [!important]
> When `v-if` and `v-for` exist on the same node, `v-if` will have a higher priority. That means `v-if` will not have access to variables from `v-for`'s scope. The solution is to wrap the `v-if` node in a `<template>` and using `v-for` on the template.

```vue
<template v-for="post in posts" :key="post.id">
    <li v-if="post.published">
        {{ post.title }}
    </li>
</template>
```

> [!note] Why Not to Use Array Index as Key?
> - If the list order changes (e.g., sorting), the index-based keys will no longer correspond to the same data items, which causes Vue to unnecessarily re-render elements.
> - Adding or removing items from the list can cause unexpected behavior in component state and DOM updates.
> - It's always recommended to use a unique identifier from data as the key, such as an ID from a database or any unique property of the item.

## Composables

- In the context of Vue apps, a composable is a function that leverages the Composition API to encapsulate and reuse stateful logic.
- Similar to hooks in [[React]], composables can be used to manage state that changes over time or reactive state.
- A function is not considered a composable, if 
    - it does not manage any reactive state 
    - it doesn't register any lifecycle hooks
    - it doesn't provide/inject anything.

```ts
import { ref, onMounted, onUnmounted } from 'vue';

export const useMousePosition = () => {
    const x = ref(0);
    const y = ref(0);
    
    const updatePosition = (e: MouseEvent) => {
        x.value = e.pageX;
        y.value = e.pageY;
    }
    
    onMounted(() => window.addEventListener('mousemove', updatePosition));
    onUnmounted(() => window.removeEventListener('mousemove', updatePosition));
    
    return { x, y };
}
```

## Error Handling

```vue
<script setup>
import { onErrorCaptured, ref } from "vue";
import Child from "./Child.vue";

const hadError = ref(false);

onErrorCaptured((err, instance, info) => {
    console.log(err, instance, info);
    hadError.value = true;	
    return false;
});
</script>

<template>
    <p v-if="hadError">An error occurred</p>
    <Child v-if="!hadError" />
</template>
```

## Lifecycle Hooks

- In the Composition API, the `setup()` function runs before the `beforeCreate` and `created` lifecycle hooks, making them redundant. 
	- It combines the functionality of both `beforeCreate` and `created`, so any initialization logic can be placed directly inside `setup()`. 
	- Since code in `setup()` executes at the same time these hooks would have fired, there's no need for separate hook functions.

## Advanced

### Rendering Mechanism

- **Compile** 
    - Vue templates get compiled into render functions (functions that return [[Virtual DOM]] trees)
    - Can be done ahead-of-time (build step) or on-the-fly (runtime compiler).
- **Mount**
    - The runtime renderer calls the render functions, walks the returned VDOM tree, and constructs a real DOM tree from it.
    - It's performed as a reactive effect, therefore it *tracks* of all reactive dependencies that were used.
- **Patch** / **Diffing** / **Reconciliation**
    - When a dependency used during mount changes, the effect re-runs, creating a new, updated [[Virtual DOM|VDOM]] tree. 
    - The runtime renderer walks the new tree, compares it with the old one, and applies necessary updates to the actual [[DOM]].

> [!quote] Render Pipeline
> ![[vue.rendering-mechanism.png]]
> 
> **Source**: [Vue.js](https://vuejs.org/guide/extras/rendering-mechanism.html)

### Async Components

- `defineAsyncComponent`
- Used for [[code splitting]] and [[lazy loading]].
- Can be used with `<Suspense>`.
- Divide large apps into smaller chunks, loading components from the server only when needed, with built-in support for loading/error states.
- Bundlers like Vite and webpack use it as bundle split points.

```ts
app.component('MyButton', defineAsyncComponent(() =>
	import('./components/Button.vue')
))
```

```vue
<script setup>
import { defineAsyncComponent } from 'vue'

// Simple lazy loading
const AdminPage = defineAsyncComponent(() => 
	import('./components/AdminPage.vue')
)

// With loading/error states
const AsyncModal = defineAsyncComponent({
	loader: () => import('./components/Modal.vue'),
	loadingComponent: LoadingSpinner,
	errorComponent: ErrorComponent,
	delay: 200,  // delay before showing loading component
	timeout: 3000  // show error if loading takes too long
})
</script>

<template>
	<AdminPage />
	<AsyncModal />
</template>
```

- If a loading component is provided, it displays first while the inner component loads. 
- If an error component is provided, it displays when the Promise returned by the loader function is rejected.
- Since Vue 3.5, async components can control when they are hydrated with a hydration strategy when using SSR (e.g. `hydrateOnVisible`)

> [!note]
> If an async component is wrapped with `<Suspense>`, it will be treated as an async dependency of that `<Suspense>`, and its loading state will be controlled by the `<Suspense>`. The component's own loading, error, delay and timeout options will be ignored.

### Async `setup()` & `<Suspense>`

- Used for data-fetching coordination.
- Components with an async `setup()` hook or `<script setup>` with top-level `await` expressions become async dependencies that `<Suspense>` can wait on. 
	- This allows parent components to coordinate loading states for multiple async operations instead of each component managing its own spinner.

```vue
<!-- ArticleInfo.vue -->
<script setup>
// Top-level await makes this an async dependency
const res = await fetch('https://api.example.com/article')
const article = await res.json()
</script>

<template>
	<h2>{{ article.title }}</h2>
	<p>{{ article.content }}</p>
</template>
```

```vue
<!-- Parent component -->
<template>
	<Suspense>
		<template #default>
			<ArticleInfo />
		</template>
		<template #fallback>
			<div>Loading article...</div>
		</template>
	</Suspense>
</template>
```

### [[Server-Side Rendering|SSR]]

- **Server-side rendering lifecycle:**
	1. **Server-side app creation**: Use `createSSRApp()` to create an SSR-compatible app instance that will be shared between server and client. 
		- The application should be modularized so that core functionalities like creating the Vue instance, defining routes, and managing state are abstracted in shared files
	2. **HTML generation**: `renderToString()` takes the app instance and returns a Promise that resolves to the rendered HTML string.
		- Only `beforeCreate` and `created` hooks execute during SSR. Lifecycle hooks like `mounted` or `updated` are not called on the server and only execute on the client. 
		- Reactivity is disabled during SSR for better performance since there are no dynamic updates.
		- Data state must be serialized and embedded in the final HTML payload (typically as `window.__INITIAL_STATE__`) for the client to pick up where the server left off.
	3. **HTML Delivery**: The server-rendered output includes a `data-server-rendered` attribute on the root element to signal the client that this markup should be hydrated. 
		- The HTML is sent to the browser along with the serialized state.
	4. **[[Hydration]]**: Vue takes over the static HTML sent by the server and turns it into dynamic DOM that can react to client-side data changes. 
		- When `app.mount('#app')` is called, Vue detects the `data-server-rendered` attribute and performs hydration instead of mounting new DOM nodes. 
		- On the client, `createSSRApp()` (not `createApp()`) is used with the same app implementation as on the server, since `createApp()` deletes the entire server-rendered HTML by doing `container.innerHTML = ''`, performing a full render instead of hydration.
		- Instead of re-creating all DOM elements, Vue "hydrates" the static markup by:
			- Matching existing DOM nodes inside the container with the client-side virtual DOM tree.
			- Attaching event listeners and making the app interactive.
			- If mismatches are encountered, Vue attempts to automatically recover and adjust the pre-rendered DOM to match the expected output by morphing existing nodes.
- It's important to avoid code with side effects needing cleanup in `beforeCreate`/`created`/`setup()`. 
	- For example, timers set with `setInterval` will stay around forever since unmount hooks never fire during SSR. Such code should be moved to `mounted()` instead.
- Universal code cannot assume access to platform-specific APIs like `window` or `document`, as they throw errors when executed in Node.js.

```js
/* --- Simplified Implementation --- */

// --- app.js (Shared) ---
import { createSSRApp } from 'vue';

export function createApp() {
	return createSSRApp({
		data: () => ({ count: 1 }),
		template: `<div @click="count++">{{ count }}</div>`,
	});
}

// --- server.js --- import express from 'express';
import { renderToString } from 'vue/server-renderer';
import { createApp } from './app.js';

const server = express();

server.get('/', (req, res) => {
	const app = createApp();
	
	renderToString(app).then((html) => {
		res.send(`
		<!DOCTYPE html>
		<html>
		  <head>
			<title>Vue SSR Example</title>
			<script type="importmap">
			  {
				"imports": {
				  "vue": "..."
				}
			  }
			</script>
			<script type="module" src="/client.js"></script>
		  </head>
		  <body>
			<div id="app">${html}</div>
		  </body>
		</html>
		`);
	});
});

server.use(express.static('.'));

server.listen(3000, () => {
	console.log('ready');
});


// --- src/entry-client.js --- 
import { createApp } from './app.js';

createApp().mount('#app');
```

---
## Further

### Books 📚

- [The chibivue Book](https://book.chibivue.land/)

### Reads 📄

- [12 Design Patterns in Vue (Michael Thiessen)](https://michaelnthiessen.com/12-design-patterns-vue)

### Videos 🎥

- [The Power of Render Functions (YouTube)](https://www.youtube.com/watch?v=vzR00-I3YSE&list=PLC2LZCNWKL9bt5t7n6rAPy1Yni-VIGJMc)

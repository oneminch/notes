- In [[Next.js]], components that use browser-only APIs (like `window`) cause issues when statically rendering an app.
- This issue can be resolved by disabling SSR for such components.

```jsx
import dynamic from "next/dynamic";

const BrowserOnlyComponent = dynamic(
	() => import("@/components/browser-only-component"),
	{ ssr: false },
);

export default function Page() {
	return (
		<div>
			      <h1>Home</h1>
			      <BrowserOnlyComponent />   {" "}
		</div>
	);
}
```

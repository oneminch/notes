## Using Route Rules

```ts
export default defineNuxtConfig({
	routeRules: {
		// Static Path Redirects
		// "/old": { redirect: "/new" } (307 - Temporary Redirect)
		"/old": {
			redirect: {
				to: "/new",
				statusCode: 301,
			},
		},
		// Wildcard Redirects
		"/old/**": {
			redirect: "/new/**",
		},
	},
});
```

## Fundamentals

- A framework to structure React applications, and optimizations that improve the developer and user experience.
- Core features of Next.js:
    - File-based routing
    - Hybrid rendering
        - Using [[middleware]], we can run server-side code at the Edge.
    - Automatic code splitting & prefetching
        - Application is code split by route segments. This results in isolated pages, where an error on a certain page doesn't affect the rest of the application.
        - In production, whenever `<Link>` components appear in the viewport, the code for the linked route is automatically prefetched in the background.
    - Support for different styling methods: CSS Modules, CSS-in-JS
    - API routes for server-side processing
    - Fast Refresh on development
    - SEO features
        - Customize metadata using the built-in `<Head>` component (from `next/head`)
    - Script optimizations
        - Using the built-in `<Script>` component (from `next/script`)
    - Asset optimization
        - Automatic image optimization using the built-in `<Image>` component (from `next/image`)
        - Font Optimization
            - When using the `next/font` module, Next.js downloads font files at build time and hosts them with other static assets. This helps avoid additional network requests for fonts which would impact performance.

```jsx
import { Inter } from 'next/font/google';

export const inter = Inter({ subsets: ['latin'] });

export default function Page() {
  return (
    { /* ... */ }
    <p className={`${inter.className} text-xl`}>Hello, Next.js</p>
    { /* ... */ }
  );
}
```

- Optimization features of Next.js:
    - Compiling
    - Minifying
    - Bundling
    - Code Splitting

## Rendering

### Pre-rendering

- Pre-rendering is the process of generating HTML for each page in advance.
- HTML is displayed on initial load even if [[JavaScript|JS]] is disabled.
- It is followed by [[hydration]] to run JS code and make the page fully interactive.
- This improves [[Web Performance|performance]] and [[Search Engine Optimization]].
- Next.js pre-renders every page by default.
- There are 2 forms of pre-rendering: Static Generation & Server-side Rendering.
- Like with Nuxt, Next.js lets you define any form of pre-rendering on per-page basis.

#### Static Generation

- [[Static Site Generators|Static Generation]] - generating HTML at ==build time==.
    - Can be done with or without data.
- `getStaticProps` is an `async` function exported alongside a page component that runs at build time in production. It can fetch external data, and send it as props to a page.
    - It can only be exported from a page.
    - In dev mode, it runs on every request.

> [!important]
> Because `getStaticProps` is meant to be executed at build time, data that’s only available during request time, such as query parameters or HTTP headers, are not accessible.

```js
export default function HomePage({ data }) { ... }

export async function getStaticProps() {
    const res = await fetch("https://api.example.com");
    const data = await res.json();

    return { props: { data } };
}
```

#### Server-side Rendering

- [[Server-side Rendering]] - generating HTML ==on demand== or ==on request time==.
    - Used in development mode.
- In similar fashion to `getStaticProps`, `getServerSideProps` is exported from a page and runs at request time.
    - It has a parameter (`context`) which contains request specific information.

```js
export async function getServerSideProps(context) {
	return {
		props: {
			// props for the page component
		},
	};
}
```

### Client-side Rendering

- It involves statically generating parts of a page that don't require data, and, after page load, fetch data on the client-side to populate the page with relevant data.
    - This approach can be useful for private or user-specific pages where SEO is irrelevant, such as dashboards.
- The [SWR](https://swr.vercel.app/) React hook library is commonly used for client-side data fetching in Next.js.

## Styling

- Next.js supports different styling methods out of the box:
    - Sass,
    - CSS Modules,
    - CSS-in-JS.
- Sass (`.scss` & `.sass`) imports are supported out of the box, including component-level Sass (`.module.scss` & `.module.sass`).
- Next.js compiles CSS using PostCSS, which can be configured using a top-level `postcss.config.js` file.

## Project Structure

- `app/`- app router
- `pages/`- pages router
- `public/` - storing static assets
    - Served from `/`
- `src/` - optional source directory

- **Layouts** are persistent UI shells that wrap around the application's pages.
    - They maintain their state and do not re-render when navigating between pages that share the same layout.
    - They are defined by creating a React component in a file named `layout.js` or `layout.tsx` (for App Router) with a `children` prop to integrate child layouts or pages.
    - They can be nested to create complex UI structures.
- **Templates** are similar to layouts, but a template is fully remounted every time a user navigates to a new page.
    - This process resets the component state and effects, providing a fresh start with each page transition.
    - They are defined by exporting a default React component from a `template.js` or `template.tsx` file (for App Router).

## Routing

- Next.js uses file-system routing.
- The built-in `<Link>` component (from `next/link`) enables client-side navigation between pages in the same Next.js app.
    - Next.js provides the `usePathname()` hook for working with the currently active link.
- A page is a React component exported from a file in the `pages/` directory.

## Pages Router

- It uses the `pages/` directory.
    - Global layout: `pages/_app.jsx`
    - Entry point: `pages/index.jsx`
    - Custom Document: `pages/_document.jsx`

```jsx
export default function App({ Component, pageProps }) {
	return <Component {...pageProps} />;
}
```

- Similar to `app.vue` in Nuxt applications, a default-exported `/pages/_app.js` is a top-level component that wraps all pages in a Next.js application.
    - It can be used to keep state when navigating between pages, or to add global styles.

> [!important]
> Global CSS can only be imported inside `/pages/_app.js`.

### Dynamic Routes

- In a statically generated Next.js app, when an `async` `getStaticPaths` is exported from a page that uses dynamic routes, Next.js will statically pre-render all the paths specified by the function.
- **Catch-all Routes**: `/pages/subroute/[...param].js`
    - Matches `/pages/posts/post-1`, `/pages/posts/post-1/subpost-1`, etc.
- **Custom 404 Pages**: `/pages/404.js`

```js
{/* /pages/posts/[slug].js */}
export default function Post() { ... }

// (1) SSR
export async function getServerSideProps(context) {
	const { query: { slug } } = context;
	// ...
}

// (2) SSG
// Return a list of possible values for 'slug'
export async function getStaticPaths() {
    return [
        {
            params: {
                slug: "...",
            }
        }, ...
    ];
}

// Fetch necessary data for a post using params.slug
export async function getStaticProps({ params }) { ... }
```

### API Routes

- Server endpoints created in the `pages/api` directory and defined as [[Node|Node.js]] serverless functions.
    - Any file inside `pages/api` is mapped to `/api/*`.
    - They can be dynamic just like page routes.

```js
export default function handler(req, res) {
	res.status(200).json({ message: "Hello, World!" });
}
```

- A common use case for API routes is for handling forms.

> [!important]
> API Routes should _not_ be fetched from `getStaticProps` or `getStaticPaths`.

## App Router

- Newer evolution of the Pages Router.
- Uses `app/` directory.
    - Contains all routes, components and logic.
    - Global layout: `app/layout.jsx` (Required)
    - Entry point: `app/page.jsx`
- Unlike Pages Router, global CSS can be imported into any component, but it's usually a good practice to do so into the top-level component (`app/layout.tsx`).

```jsx
import "@/app/ui/global.css";

export default function RootLayout({ children }) {
	return (
		<html lang="en">
			<body>{children}</body>
		</html>
	);
}
```

- `page.tsx` is a special Next.js file that exports a React component, and it's required for a route to be accessible.
    - e.g. `app/dashboard/page.jsx` is accessible from the `/dashboard` path
- `layout.tsx` is a special Next.js file used to create UI that is shared between multiple pages.
    - e.g. `app/dashboard/layout.tsx` is shared between all nested pages (`/dashboard/settings/page.tsx`, `/dashboard/profile/page.tsx`).

```jsx
/* app/dashboard/layout.tsx */
import SideBar from "@/app/components/dashboard/sidebar";

export default function Layout({ children }) {
	return (
		<div className="...">
			<div className="...">
				<SideBar />
			</div>
			<div className="...">{children}</div>
		</div>
	);
}
```

- **Partial Rendering**
    - When using layouts, only the page components (`children`) update while the layout won't re-render on navigation.
    - Shared segments are preserved.

### Route Handlers

- Used to create custom request handlers for a given route using the Web Request and Response APIs.
- The equivalent of API Routes in Pages Router.
- Defined in a `route.js|ts` file inside `app/`.
- Can be nested inside `app/`, similar to `page.jsx` and `layout.jsx`.

> [!important]
> There **cannot** be a route handler at the same route segment level as `page.js`.

```ts
/* app/api/route.ts */
import { cookies } from "next/headers";

// For dynamic rendering
export const dynamic = "force-dynamic";

export async function GET(request: Request) {
	const cookieStore = cookies();
	const token = cookieStore.get("token");

	return new Response("Hello, Next.js!", {
		status: 200,
		headers: { "Set-Cookie": `token=${token.value}` },
	});
}
```

### Data Fetching

- `loading.tsx` is a special Next.js file built on top of `<Suspense>`.
    - It can be used to create fallback UI (e.g. skeletons) to show as a replacement while loading page content.
    - It is used to stream an entire page.
    - By default, it is applied to nested pages in the directory it's created in.
        - To prevent it from being applied to nested pages, we can move the file inside a route group along with the adjacent `page.tsx` file.
        - e.g. `app/dashboard/(home)/loading.tsx` will only be applied to `/dashboard` (but not to any nested paths `/dashboard/*`).
- `<Suspense>` is used to stream individual components.
    - Data fetching logic must be moved inside the components that need it.
    - Page sections can be streamed by wrapping them in a single component and performing data fetching inside the wrapper component.

```tsx
export default async function ProductList() {
    const products = await fetchProducts();

    return (
        /* ... */
    )
}

export default function Page() {
    return (
        /* ... */
        <Suspense fallback={<p>Loading...</p>}>
            <ProductList />
        </Suspense>
        /* ... */
    )
}
```

### Route Groups

- Nested folders are normally mapped to URL paths.
- To prevent a folder from being included in the route's URL path, we can mark a folder as a **Route Group** by wrapping it in parenthesis.
    - e.g. `app/dashboard/(home)/page.tsx` or `app/dashboard/(home)/loading.tsx`

### React Server Components

- Passing server components as children or props to client components helps maintain their server-side rendering benefits while still allowing for client-side interactivity.

```jsx
// Layout.js (Client Component)
'use client';

import { useState } from 'react';

export default function Layout({ children }) {
    const [theme, setTheme] = useState('light');

    const handleThemeChange = () => {
        setTheme(theme => (
            theme === 'light' ? 'dark' : 'light')
        )
    }

    return (
        <main data-theme={theme}>
            <button onClick={handleThemeChange}>
                Toggle Theme
            </button>
            {children}
        </main>
    );
}

// Page.js
import Layout from './Layout';
import ServerContent from './ServerContent';

export default function Page() {
    return (
        <Layout>
            <Suspense fallback={<div>Loading...</div>}>
                <ServerContent />
            </Suspense>
        </Layout>
    );
}
```

> [!important]
> It's important to avoid importing server components directly into client components, as this can convert them into client components unexpectedly.

```jsx
"use client";

// This will make ServerComponent a client component
import ServerComponent from "./ServerComponent";

export default function ClientComponent() {
	return (
		<div>
			{/* This is now client-side rendered */}
			<ServerComponent />
		</div>
	);
}
```

---

## Further

### Learn 🧠

- [Learn Next.js (Next.js) ⭐](https://nextjs.org/learn)

### Videos 🎥

![How To Avoid Big Serverless Bills (YouTube)](https://www.youtube.com/watch?v=jsuNjCAngnQ)

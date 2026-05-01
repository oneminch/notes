- A shared vocabulary added to web pages so that search engines can understand exactly what its content is about.
- It makes content machine-readable in a standard way, which can improve how pages are interpreted and sometimes how they appear in search results.
- Instead of a search engine having to guess that a page contains a product, event, recipe, or article, it explicitly tells it what each piece is.
    - This can help search engines and other systems display richer search results, often called rich results or rich snippets, with extras like ratings, prices, dates, or availability when supported.
- Mostly an [[Search Engine Optimization|SEO]] and content-discovery tool, not a visual design feature.
    - It doesn't change how the page appears to users; it changes how machines interpret the page.

- **Examples**
    - For a product page, schema can tell search engines the name, price, availability, and review rating. 
    - For an event page, it can specify the date, location, and organizer.

```html
<script type="application/ld+json">
{
    "@context": "https://schema.org",
    "@type": "Article",
    "headline": "My First Post",
    "author": {
        "@type": "Person",
        "name": "Jane Doe"
    },
    "datePublished": "2026-04-23"
}
</script>
```

> [!note] 
> JSON-LD is the standard recommended format for structured data because it keeps markup separate from the visible page content.
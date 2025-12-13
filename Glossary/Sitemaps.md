- Provide a structured outline of a website's content.
- Facilitate both user navigation and search engine indexing, ensuring that all pages are easily accessible.

- **Benefits**
    - **Organization of Content**
        - A sitemap acts as an outline or blueprint for a website, listing all the pages and their hierarchical relationships.
        - This helps developers understand the website's structure, and makes it easier to create and manage content.
    - **Enhanced UX**
        - Visitors can quickly find the information they need, improving the user experience.
    - **SEO**
        - Sitemaps help search engines discover and index pages more efficiently.
    - **Identification of Issues**
        - A well-structured sitemap allows website owners to spot potential issues such as broken links or orphan pages (pages without links).
        - This proactive approach helps maintain the health of the website.

- **Types**
    - **HTML Sitemaps**
        - Designed for human visitors and typically appear as a webpage containing links to all major sections and pages of the site.
        - Help users navigate the site more easily.
    - **XML Sitemaps**
        - Intended for search engines.
        - Contain URLs of all important pages along with metadata such as last modified dates and priority levels.
            - Helps search engines index the site more effectively.

## sitemap.xml

- An XML file that lists the URLs for a site which are available for crawling.
- Can include extra information about each URL: last updated date and time, frequency of updates, and rate of importance/priority.
- A _URL inclusion protocol_ and complements `robots.txt`.
- Example `sitemap.xml` file:

```xml
<?xml version="1.0" encoding="UTF-8"?>

<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9">
	<url>
		<loc>http://www.example.com/</loc>
		<lastmod>2022-01-01</lastmod>
		<changefreq>monthly</changefreq>
		<priority>0.8</priority>
	</url>
</urlset>
```

- aka _robots exclusion standard_ or _robots exclusion protocol_.
- A standard used by sites to communicate with crawlers and bots like search engines to inform which portions of the site shouldn't be accessed or scanned. It is mainly used to reduce the request load on the site.
- It is a ==URL exclusion protocol==.
- Example `robots.txt`

```txt
# MDN Web Docs

User-agent: *
Sitemap: https://developer.mozilla.org/sitemap.xml

Disallow: /api/
Disallow: /*/files/
Disallow: /media
Disallow: /en-US/search
```

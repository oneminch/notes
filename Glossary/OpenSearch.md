- The OpenSearch spec allows a site to define a search engine for itself. It is supported by the major browsers.
- A search engine can be described using an XML file that is similar to the following basic example:

```xml
<!-- opensearch.xml -->
<OpenSearchDescription xmlns="http://a9.com/-/spec/opensearch/1.1/">
  <ShortName>MDN Web Docs</ShortName>
  <Description>Search the MDN Web Docs</Description>
  <InputEncoding>UTF-8</InputEncoding>
  <Image width="16" height="16" type="image/x-icon">https://developer.mozilla.org/favicon.ico</Image>
  <Url type="text/html" method="get" template="https://developer.mozilla.org/search?q={searchTerms}"/>
</OpenSearchDescription>
```

> Firefox (and probably other browsers) might support additional features that are not in the standard like search suggestions.

- To be auto-discovered by browsers, search plugins can be advertised using `<link>`.

```html
<!-- index.html -->
<link
	rel="search"
	type="application/opensearchdescription+xml"
	title="searchTitle"
	href="pluginURL"
/>
```

> [!NOTE]
>
> - `searchTitle` - must match the content of `<ShortName>` in the XML file.
> - `pluginURL` - URL to the XML search plugin / file.
> - The `xmlns` attribute is important. The plugin can't be downloaded without it.
> - Favicons that are remotely fetched mustn't exceed 10KB.
> - A URL of type `text/html` must be included.

- Multiple search plugins can be offered for a single site. An XML file in the OpenSearch
  format needs to be created for each plugin and `<link>` can be used to make each plugin discoverable.
- OpenSearch plugins can automatically update - [OpenSearch - MDN](https://developer.mozilla.org/en-US/docs/Web/OpenSearch#supporting_automatic_updates_for_opensearch_plugins)

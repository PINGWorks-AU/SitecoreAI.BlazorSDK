# Search
[&lt; Docs index](index.md)

> [!NOTE]
> **Placeholder - not yet implemented.**
> This page is a stub so we remember to document search once SDK support lands. The content below sketches the intended scope; treat it as a to-do rather than a guide.

Sitecore offers two distinct search products, and a site may use either (or both). This page will eventually cover how to integrate each from a Blazor head.

## Sitecore Search (formerly Discover / Search & Recommendations)

The standalone, crawler-and-API search product. Planned coverage:

- Connecting to the Sitecore Search API (publish/serve keys, domain/source configuration)
- Building a search results component (query, facets, pagination, sorting)
- Type-ahead / autosuggest
- Recommendations / "more like this" widgets
- Analytics and click tracking for search results
- Where the SDK should provide helpers vs. what the consumer wires up

## Experience Search

Sitecore's newer search offering. Planned coverage:

- How Experience Search differs from Sitecore Search and when to choose it
- Indexing model and content sources
- Querying from a Blazor component
- Faceting, ranking and personalised results
- Integration points with the rest of the SDK (content sync, caching, analytics)

## To do

- [ ] Decide what, if anything, the SDK abstracts for each product
- [ ] Add a worked search-results component to the Starter Kit
- [ ] Document configuration and keys
- [ ] Cross-link from the analytics doc for search event tracking

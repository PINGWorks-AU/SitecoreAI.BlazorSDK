# Page-level security
[&lt; Docs index](index.md)

> [!NOTE]
> **Placeholder - not yet implemented.**
> This page is a stub so we remember to document page-level security once SDK support lands. The content below sketches the intended scope; treat it as a to-do rather than a guide.

Some sites need pages that aren't public: members-only content, gated downloads, sections restricted to authenticated users or particular roles. This page will eventually cover how to protect Sitecore-routed pages in a Blazor head.

## Planned coverage

- How Sitecore item security maps (or doesn't) to a headless delivery model
- Protecting Sitecore-routed pages with ASP.NET authentication and authorization
- Integrating an identity provider (e.g. Entra ID, Auth0) with the server-rendered Blazor app
- Role- and policy-based access at the route / template / component level
- Handling unauthorised access cleanly (redirect to sign-in, 403 page - see [Error handling](error-handling.md))
- Interaction with caching: per-user content must not be served from the shared cache (see [Caching and content sync](caching-sync.md))
- Editing experience for secured pages in Sitecore Pages

## To do

- [ ] Decide what the SDK provides vs. what the consumer wires up with standard ASP.NET auth
- [ ] Document the cache-bypass requirement for authenticated/personalised pages
- [ ] Add a worked secured-page example to the Starter Kit
- [ ] Cross-link with error-handling (403) and caching docs

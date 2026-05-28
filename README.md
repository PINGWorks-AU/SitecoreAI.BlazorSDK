# Introduction

This Blazor SDK is used to provide the runtime features needed to build a Blazor-based rendering host. The engine supports authoring through the Sitecore AI Pages application and can be used to create SSR and interactive Blazor-based sites.

[Additional documentation is available](docs/index.md) in this repo to help you understand and access all the features that the SDK has to offer.

See the [Sitecore XMC Blazor Starter Kit (coming soon)]() for a worked example that shows how to create a Blazor-based site.

# Dependencies

This library has the following dependencies:

1. PINGWorks.SitecoreExperienceEdge.AdminSDK - provides strongly-typed models and services for accessing Sitecore AI's Admin SDK, used to create and manage webhooks dynamically to ensure content updates automatically evict the local item cache.
1. PINGWorks.SitecoreExperienceEdge.ContentSDK - provides strongly-typed models and services for accessing Sitecore AI's Delivery GraphQL APIs.
1. PINGWorks.SitecoreExperienceEdge.EventsSDK - provides strongly-typed models and services for accessing Sitecore AI's Events APIs.
1. PolyglotDataStudio.GraphQL - provides strongly-typed models and services for writing and parsing GraphQL queries.


# Getting Started

## Installation

Install the library via NuGet:

```
dotnet add package PINGWorks.SitecoreAI.BlazorSDK
```

## Configuration

A complete list of settings is included below, showing default or sample values where configured. These settings sections
have fixed names, so must appear using the names and structure shown precisely. Values are only required where
you do not wish to use the system default.

```json
{
  "SitecoreSettings": {
	/* Omit this key entirely to use all the recommended defaults */
	"Analytics": {
		"Enabled": true,
		/*
		The cookie to check for user consent to Events tracking. Default 'pw#eventConsent'.
		Events will not be recorded unless this cookie has a value of '1'.
		*/
		"EnableEventsCookie": "pw#eventConsent",
		/*
		The cookie to check for user consent to Profile tracking. Default 'pw#profileConsent'.
		Events will not be recorded unless this cookie has a value of '1'.
		*/
		"EnableProfileCookie": "pw#profileConsent",
		"EventsCookie": {
			"Name": "sc_cid",
			/* Set automatically to match site domain, unless specified here. Default 'null' */
			"Domain": null,
			"Path": "/",
			"ExpiryDays": 730
		},
		"ProfileCookie": {
			"Name": "sc_cid_personalize",
			/* Set automatically to match site domain, unless specified here. Default 'null' */
			"Domain": null,
			"Path": "/",
			"ExpiryDays": 730
		},
		/* Base url for events. Omit to use the default ingestion endpoint. Default 'null'. */
		"ServiceEndpoint": null
	},

	/* REQUIRED by CORS for authoring. List of authorised host names (excl. localhost) */
	"AuthoringHosts": [
	  "https://authoring.sample-host.com"
	],

	/* API key required in the 'x-apikey' header for access to remote cache reset. Default 'null'. */
	"CacheResetApiKey": null,

	/* API path for remote cache reset. Default '/api/cache-reset'. */
	"CacheResetPath": "/api/cache-reset",

	/* Default Sitecore content language. Default 'en'. */
	"DefaultLanguage": "en",

	/* List of additional languages or culture codes to accept at request time. DefaultLanguage is implicit, entries are case-sensitive. Default empty. */
	"AllowedLanguages": [ "fr-CA", "de-DE" ],

	/* Default Sitecore navigable item's template name, usually Page. Default 'Page'. */
	"DefaultPageTemplate": "Page",

	/* Origin restriction when using Design Studio. Default 'https://designlibrary.sitecorecloud.io/'. */
	"DesignOrigin": "https://designlibrary.sitecorecloud.io/",

	/* REQUIRED. Sitecore Deploy -> Projects -> [Environment] -> Developer settings -> SITECORE_EDGE_CONTEXT_ID.
	   Sensitive — set via user secrets, not in this file. See "User Secrets" below. */
	"EdgeContextId": "set-via-user-secrets",

	/* List of npm packages to include in the editing handshake response payload. Default is empty. */
	"EditingConfigPackages": {},

	/* Policy name used by app.MapGet().RequireCors() when registering editing route handlers */
	"EditingCorsPolicyName": "AllowSitecoreEditing",

	/* Origin restriction when editing. Default [https://pages.sitecorecloud.io, https://designlibrary.sitecorecloud.io] */
	"EditingOrigins": [
		"https://pages.sitecorecloud.io",
		"https://designlibrary.sitecorecloud.io"
	],

	/* REQUIRED. Sitecore Deploy -> Projects -> [Environment] -> Developer settings -> SITECORE_EDITING_SECRET.
	   Sensitive — set via user secrets, not in this file. See "User Secrets" below. */
	"EditingSecret": "set-via-user-secrets",

	/* 'true' to render editing markup. Use 'false' on public-facing systems. Default 'false'. */
	"EnableEditingMode": false,

	/* API path for authoring handshake. See ServerSideRenderingEngineConfigUrl field in the RenderingHost Sitecore item. Default '/api/editing/config'. */
	"EditingPath": "/api/editing/config",

	/* REQUIRED if editing is enabled. The authoring environment hostname from Sitecore Deploy -> Projects -> Environments -> Details -> Environment host name */
	"EnvironmentHostName": "",

	/* API path for retrieving an analytics tracking token. Default '/api/token'. */
	"GetTrackingTokenPath": "/api/token",

	/* API path for authoring render callback. This path is required by the Pages editor. Default '/api/editing/render'. */
	"RenderingPath": "/api/editing/render",

	/* REQUIRED. Sitecore Deploy -> Projects -> Environments -> Sites -> Name */
	"SiteName": "",

	/* Adds the 'jssmedia' url segment in place of 'media' segment. Default 'false'. */
	"UseJssMediaUrls": false,

	/* API path for publishing webhook callback. Default '/api/webhooks/publish'. */
	"WebhookPath": "/api/webhooks/publish"
  },

  "SyncSettings": {
	/* REQUIRED.
	   MemoryAndDisk | MemoryOnly | Disabled. Caching mode for Sitecore page rendering.
	   When 'Disabled' the webhooks are also disabled; use this during development.
	   Default 'MemoryAndDisk'. */
	"CachingMode": "MemoryAndDisk",

	/* Configure the webhook content change notification system */
	"ChangeFeed": {

	  /* 'true' to create the webhook automatically on application startup. Requires caching to be enabled. Default 'false'. */
	  "AutoCreateEdgeWebhook": false,

	  /* REQUIRED. Sitecore Edge administration ClientId. Sensitive — set via user secrets. See "User Secrets" below. */
	  "ClientId": "set-via-user-secrets",

	  /* REQUIRED. Sitecore Edge administration ClientSecret. Sensitive — set via user secrets. See "User Secrets" below. */
	  "ClientSecret": "set-via-user-secrets",

	  /* Unique identifier for the Webhook subscription to properly support cleanup. Set to NULL to use the host machine name. Default 'null'. */
	  "UniqueID": "",

	  /* RECOMMENDED. Configure a required API Key header for automatically registered publish webhooks. Value can be anything, or 'null' to disable. Default 'null'. */
	  "WebhookApiKey": null,

	  /* REQUIRED. Host that Sitecore webhook should call. During development use a Dev Tunnel. Default 'null'. */
	  "WebhookReceiverBase": "https://abc234def-9999.aue.devtunnels.ms/",

	  /* Publish webhooks can raise many messages. Cache clearing is global, so we de-bounce the events. Default 30s. */
	  "CacheClearDebounceSecs": 30
	},

	/* REQUIRED. Relative disk location to write page cache items. Requires CachingMode = MemoryAndDisk. */
	"DataPath": "./App_Data",

	/* REQUIRED. Maximum ttl for in-memory cache items. Requires CachingMode != Disabled. */
	"MemoryCacheTimeout": "01:00:00",

	/* Sitecore paths that should be cached at application startup. Default [ '/' ]. */
	"PreloadPaths": [
		"/"
	],

	/* REQUIRED. Maximum ttl for disk-based cache items. Requires CachingMode = MemoryAndDisk. */
	"StorageCacheTimeout": "1.00:00:00",

	/* Reduce storage requirements by employing Brotli compression for disk cache. Default 'false'. */
	"StoreCompressed": false
  },

  /* Resolver will search your Blazor application to find razor components with the same name as the component being referred to in Sitecore. */
  "ComponentResolver": {
	/* Limit the search for matching components to specific namespaces. Use '*' to search all namespaces in the application. Default ['*']. */
	"SearchNamespaces": [ "*" ]
  }
}
```

### Cache-reset endpoint

The cache-reset endpoint requires a `lang` querystring parameter matching one of the configured `AllowedLanguages` (or `DefaultLanguage`). The match is case-insensitive but must correspond to a known entry. Mismatched or missing values are silently ignored with `204 NoContent` (consistent with the response when the API key or license is invalid).

```
POST /api/cache-reset?lang=fr-CA
x-apikey: <your key>
```

### User Secrets

The settings marked above as `"set-via-user-secrets"` are sensitive and should never be committed to source control. Add them to your project's **User Secrets** (or any secrets provider such as Azure Key Vault):

```json
{
  "SitecoreSettings": {
	"EdgeContextId": "[SITECORE_EDGE_CONTEXT_ID]",
	"EditingSecret": "[SITECORE_EDITING_SECRET]"
  },
  "SyncSettings": {
	"ChangeFeed": {
	  "ClientId": "[CLIENT ID]",
	  "ClientSecret": "[CLIENT SECRET]"
	}
  }
}
```

You can get these values from the following locations *(Sitecore Deploy portal labels as of mid-2026 — refer to current Sitecore documentation if the UI has shifted)*:

- `SitecoreSettings.EdgeContextId` and `SitecoreSettings.EditingSecret` — from the Sitecore Deploy portal (https://deploy.sitecorecloud.io). Navigate to **Projects → [Environment] → Developer settings**. Values are listed as `SITECORE_EDGE_CONTEXT_ID` and `SITECORE_EDITING_SECRET`.
- `SyncSettings.ChangeFeed.ClientId` / `ClientSecret` — these allow the SDK to manage a webhook subscription for content-change notifications. In the Sitecore Deploy portal, navigate to **Credentials** and create a new set of `Edge administration` credentials at either the Environment or Organization scope (Environment is recommended).

### Licensing

The SDK requires a license to run. One license is required per unique Sitecore `EdgeContextId` — i.e. one per Sitecore environment (dev / staging / production are bought separately).

To obtain a license, email <a href="mailto:licensing@ping-works.com.au">licensing@ping-works.com.au</a>. **DO NOT** send your `EdgeContextId` - this is a sensitive value.
You will receive a `BlazorSDK.lic` file (or similar) for each environment; place it in the application content root of the corresponding host.

## Setup and registration

The SDK requires several services to be registered in the ASP.NET Core DI container. You can do this by calling the `AddSitecoreBlazorSDK` extension method on the `IServiceCollection` instance in your `Program.cs` file:

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services
	.AddAntiforgery()
	.AddSitecoreBlazorSDK( builder.Configuration )     // Register Sitecore Blazor SDK services
	//.AddYourWebsiteServices()                        // Here's a good place to register your services
	.AddRazorComponents()
	.AddInteractiveServerComponents();
```

Routes and middleware are also required, particularly for error handling, apis and editing scenarios. Be sure to add these in the correct order, **after** `MapRazorComponents()` in your `Program.cs` file:

```csharp
if ( !app.Environment.IsDevelopment() )
{
	app.UseExceptionHandler( "/status-500", createScopeForErrors: true );    // Route unhandled exceptions through the Sitecore error pipeline (see "Error Handling")
	app.UseHsts();
}

app.UseStatusCodePagesWithReExecute( "/status-{0}", createScopeForStatusCodePages: true );  // Use Sitecore-defined error pages with a /status-{code} fallback route

app.UseHttpsRedirection();
app.UseRequestLocalization();
app.UseAntiforgery();
app.MapStaticAssets();

/* BlazorSDK sets its own CSP, remove the default */
app.MapRazorComponents<App>()
	.AddInteractiveServerRenderMode( opts => opts.ContentSecurityFrameAncestorsPolicy = null )  // Sitecore BlazorSDK will write its own CSP policies
		.AddAdditionalAssemblies( typeof( SitecorePage ).Assembly );                            // Sitecore BlazorSDK adds default route handling

app.UseSitecoreBlazorSDK();       // Add Sitecore Blazor SDK routes last

// Webhook for receiving form data, implement IFormProcessor to act on submitted data.
// Add this path to the Sitecore Form in the portal - it can be any path you like.
app.MapPost( "/api/webhooks/form-data", FormReceiver.ReceiveData );

await app.RunAsync();
```

The `UseExceptionHandler` wrap on `!IsDevelopment()` preserves ASP.NET Core's built-in developer exception page in Development.


>[!IMPORTANT]
> Ensure you set the default Blazor CSP Frame Policy to `null` as shown. Sitecore Pages requires a custom Content Security Policy to operate correctly.

### Request-pipeline ordering

The SDK registers a custom `IRequestCultureProvider` that resolves the request language from URL segment, `Accept-Language`, or `sc_lang` (in editing mode). For this to take effect, the host application must call `app.UseRequestLocalization()` early in the pipeline, before the Sitecore middleware:

```csharp
app.UseStatusCodePagesWithReExecute( "/status-{0}", createScopeForStatusCodePages: true );
app.UseHttpsRedirection();
app.UseRequestLocalization();    // Must be called early, before app.UseRouting()
app.UseAntiforgery();
app.MapStaticAssets();
```

The resolver does **not** rewrite `HttpContext.Request.Path`. Path-segment stripping for the Sitecore page lookup happens internally inside `SitecoreContextFactory`. Any explicit Razor page that matches the original path will still match.

# Main Components

## Routing

There is no special routing process used by the Blazor SDK. Instead, the SDK ships `<SitecorePage>` — a Razor page registered at the catch-all route `/{*RequestPath}` — that captures all requests not otherwise handled by your app and passes them to Sitecore for resolution. The component reads the path from its `RequestPath` parameter, fetches the matching layout from Sitecore, resolves the page template to a Blazor component (see [Page Templates](#page-templates)), and renders it.

`<SitecorePage>` also accepts a `SuppressNotFound` parameter (default `false`). When `true`, the component will *not* call `NavigationManager.NotFound()` if Sitecore returns no layout — useful when embedding the page directly (as `<ScErrorContent>` does internally to render Sitecore-configured error pages without risk of re-triggering the status pipeline).

Because the SDK's route is a catch-all, you can define additional routes in your Blazor application that aren't handled by Sitecore — for example a Customer Portal section. Minimal APIs and other routes can be defined as you would expect.

Automatic analytics tracking requires the use of the `<SitecoreRouter />` in place of the normal `<Router />` component. In your **Routes.razor** page update the code as follows:

```html
<SitecoreRouter AppAssembly="typeof( Program ).Assembly" NotFoundPage="typeof( Pages.Status )">
	<Found Context="routeData">
		<RouteView RouteData="routeData" DefaultLayout="typeof( Layouts.MainLayout )" />
		<FocusOnNavigate RouteData="routeData" Selector="h1" />
	</Found>
</SitecoreRouter>
```

The `SitecoreRouter` component supports the same parameters as the regular Blazor Router component, so you can use it exactly as you normally would. It adds the Blazor SDK assembly into the list of `AdditionalAssemblies` so that pages provided by the SDK can be reached by your app, and it adds an `OnNavigateAsync` handler to inject analytics data into the scoped DI container.

## Page Templates

Web pages in Sitecore are represented by Items, and like all items these have an Item Template. In the Blazor SDK we map the template to a razor component that allows us to vary the structure of the page based on the template used.

The Starter Kit includes a default `Page` template component that is used for most pages. You can modify this or create additional template components as required.

To work as a Template component, the razor component must:
- have the same name (case insensitive) as the template defined in Sitecore
- inherit from `SitecoreTemplate` or `SitecoreTemplate<TModel>`

You don't have to have a 1:1 map of Page Templates to Blazor component.
If your Page Templates do not require layout variations at the template level (and usually they don't) it's sufficient to just use the one `Page` template component.

## Razor Layouts

As the Blazor project can have a mixture of locally defined pages and Sitecore-managed pages, Razor Layouts are supported to allow necessary variations.

Pages defined in Sitecore always use the layout component that is specified in the `DefaultLayout` attribute of the `RouteView` element in your `Routes.razor`. If your project requires the use of different layouts for non-Sitecore pages these should be specified through the use of the `@layout` directive in the relevant razor page.

## Components

The Blazor SDK uses a Component Resolver to map Sitecore components to Blazor razor components. By default, the resolver searches all namespaces in the application for a matching component name (case insensitive).

To work, Components must also:
- inherit from `SitecoreComponent`, or
- inherit from `SitecoreComponent<TModel>`, or
- be annotated with `@attribute [SitecoreComponent("componentName")]` in the razor file

Use the attribute approach where the component name in Sitecore does not match the razor component name, or where the Sitecore component name contains characters that are not valid in a C# class name (for example, hyphens and spaces).

## Components reference

Brief catalogue of the consumer-facing components shipped by the SDK. Detailed documentation is published separately.

**Routing & page composition**
- `<SitecorePage>` — auto-routed page resolver at `/{*RequestPath}`. Looks up the path in Sitecore, resolves a template component, renders it. Accepts `RequestPath` and `SuppressNotFound` parameters.
- `<SitecoreRouter>` — drop-in replacement for `<Router>` that adds the SDK assembly and wires up analytics navigation tracking.
- `<ScPlaceholder>` — renders every component placed by Sitecore into a named placeholder. Takes a `Name` parameter and an optional `Editable` flag.

**Field bindings** (render a single Sitecore field with editing-chrome support)
- `<ScText>` — plain-text field.
- `<ScRichText>` — rich-text field with HTML rendering.
- `<ScImage>` — image field with `srcset` handling.
- `<ScLink>` — general-link field, renders an anchor.
- `<ScDate>` — date/datetime field with formatting.
- `<ScNumber>` — numeric field with formatting.
- `<ScFile>` — file/media link field.

**Error handling** (see [Error Handling](#error-handling) below)
- `<ScErrorContent>` — dispatcher for HTTP 404 / 500 status pages. Renders the Sitecore-configured error page if available, falls back to the SDK's defaults, and shows a developer-facing exception detail in editing/dev contexts.
- `<Error404Content>` / `<Error500Content>` — the SDK's default fallback components. Override per-instance via `<ScErrorContent>` RenderFragment slots.

# Error Handling

The SDK integrates with Sitecore's per-site error-page settings (`Error404` / `Error500`) and ASP.NET Core's standard status-code re-execution to render content-managed error pages — with automatic exception logging and built-in last-resort fallbacks so a misconfigured (or broken) error template can never take a request down silently.

## Pipeline

Middleware lines work together to route every kind of failure through the same Sitecore error page. Add these in `Program.cs`:

```csharp
app.UseExceptionHandler( "/status-500", createScopeForStatusCodePages: true );           // unhandled exceptions outside Blazor components → /status-500
app.UseStatusCodePagesWithReExecute( "/status-{0}", createScopeForStatusCodePages: true ); // bare status responses (e.g. 404 from NavMgr.NotFound) → /status-{code}
```

Each handles a distinct failure class:

| Failure source | Handled by | How it reaches `ScErrorContent` |
|---|---|---|
| Non-existent Sitecore path | `UseStatusCodePagesWithReExecute` | `SitecorePage` returns a bare 404 via `NavMgr.NotFound()`; middleware re-executes through `/status-404`. |
| Unhandled exception in a controller / minimal API / custom middleware | `UseExceptionHandler( "/status-500" )` | Middleware re-executes through `/status-500`; `ScErrorContent` reads the original exception from `IExceptionHandlerFeature`. |
| Exception thrown while rendering a Sitecore page template | `SitecorePage`'s built-in `ErrorBoundary` | Caught in-process, rendered inline as `<ScErrorContent StatusCode="500" Exception="…">`; HTTP status is set to `500` during SSR. |

Without the two middleware lines, only the third category works — 404s and non-component exceptions would render the framework's default empty response.

Define one Razor page that owns the `/status-{StatusCode:int}` route. The Starter Kit's example:

```razor
@page "/status-{StatusCode:int}"
@layout MainLayout

@switch ( StatusCode )
{
	case 404:
	case 500:
		<ScErrorContent StatusCode="@StatusCode" />
		break;
	case 403:
		<h1>Forbidden</h1>
		<p>You don't have permission to view that page.</p>
		break;
	default:
		<h1>Status @StatusCode</h1>
		<p>Something went wrong.</p>
		break;
}

@code {
	[Parameter] public int StatusCode { get; set; } = 404;
}
```

`<ScErrorContent>` handles **only** HTTP 404 and 500. Any other status code passed to it renders nothing — handle them yourself in the `@switch` as shown above. For 404 / 500 it:

1. Looks up the current host's `SiteInfo` and reads `Error404` or `Error500` (matching the `StatusCode` parameter).
2. If a Sitecore path is configured, embeds a `<SitecorePage RequestPath="…" SuppressNotFound />` that renders the configured page using its real template — placeholders, Layout, fields and all. The same page-render cache used by every other request applies, and the SDK pre-warms it at startup so the first hit is instant.
3. If no path is configured (or the configured page itself throws while rendering), renders the appropriate built-in fallback: `Error404Content` for 404, `Error500Content` for 500. Override either per-instance via RenderFragment slots — see [Overriding the fallback content](#overriding-the-fallback-content) below.

## Exception logging — wired for you

`SitecorePage` wraps every render in an `ErrorBoundary`. When a Sitecore page template throws:

1. The exception is captured by the boundary, the HTTP response status is set to `500` (when running as static SSR — see "Status codes" below), and `<ScErrorContent>` is rendered inline with the captured exception and the request path passed in as parameters.
2. `ScErrorContent` logs the exception via `ILogger<ScErrorContent>` (category: `PINGWorks.SitecoreAI.BlazorSDK.Components.ScErrorContent`) at `Error` level, including the original request path and the exception message.
3. If the request reaches `/status-500` through `UseExceptionHandler` (e.g. a non-Blazor middleware threw), `ScErrorContent` pulls the original exception from `HttpContext.Features.Get<IExceptionHandlerFeature>()` and logs it the same way.

Either path logs the original exception — you do not need to add `try/catch` or `OnError` wiring in your page templates.

## Developer view (Development environment / editing mode)

When the host is running in the `Development` ASP.NET environment **or** `SitecoreSettings.EnableEditingMode` is `true`, `ScErrorContent` short-circuits the Sitecore-error-page render and instead shows the SDK's `ExceptionDetail` component — exception type, message, source, full stack trace, and recursively-rendered inner exceptions. This trades the polished error page for a useful debugging surface, on the principle that the first audience for an error in editing mode is the developer who built the template, not an end user. Production deployments (with `EnableEditingMode = false` and a non-Development environment) always render the Sitecore-content error page or the configured fallback.

## Status codes

When `SitecorePage`'s `ErrorBoundary` catches a render exception:

- **Static SSR (initial request):** the response status is set to `500` if `HttpContext.Response.HasStarted` is false. Browsers, search crawlers, and uptime monitors all see the failure.
- **Interactive Server (after circuit established):** the HTTP response is long gone; the inline `ScErrorContent` render is the only signal. The user sees the error UI; HTTP status is whatever it was on the original response (typically `200`).
- **Streaming SSR with headers already flushed:** status cannot be changed; same outcome as interactive.

## Overriding the fallback content

The SDK ships with two built-in fallback components, used when no Sitecore error page is configured (or the configured one fails to render):

- `Error404Content` — minimal "page not found" page with a link home.
- `Error500Content` — "something went wrong" page with the request ID and an environment hint.

Both are isolated by Blazor scoped CSS so they don't inherit your site's typography, and they handle bare HTTP responses cleanly on their own.

Override either one per-instance by passing a `RenderFragment` to `<ScErrorContent>`:

```razor
<ScErrorContent StatusCode="@StatusCode">
	<Error404Content>
		<h1>Lost?</h1>
		<p>That page isn't here. <a href="/">Head home</a> and try again.</p>
	</Error404Content>
	<Error500Content>
		<h1>Sorry</h1>
		<p>Something broke. We've been notified.</p>
	</Error500Content>
</ScErrorContent>
```

Either slot can be omitted — unsupplied slots use the built-in component. Use one or both as needed.

If you want a single branded fallback shared across every status page, define it once as a Razor component in your project and pass it to both slots:

```razor
<ScErrorContent StatusCode="@StatusCode"
	Error404Content="@(@<MyBrandedError />)"
	Error500Content="@(@<MyBrandedError />)" />
```

## Sitecore content authoring

In Sitecore Pages, set the site's **Error Handling** values (`Error404PagePath`, `Error500PagePath`) to the content path of the items that should render for each status. Both paths are language-agnostic — the page itself can have per-language variants, which the SDK selects using the same rules as any other rendered page.

## What gets cached

The error pages are loaded into the page cache during startup alongside the regular preload paths. They observe the same `SyncSettings.CachingMode`, `MemoryCacheTimeout`, and `StorageCacheTimeout` as every other page — no error-page-specific cache tuning is needed.

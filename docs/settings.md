# Configuration settings
[&lt; Docs index](index.md)

Three configuration sections drive the Blazor SDK: `SitecoreSettings`, `SyncSettings` and `ComponentResolverSettings`. They are bound to strongly-typed options classes during `AddSitecoreBlazorSDK( builder.Configuration )` and consumed throughout the SDK via `IOptions<T>`. Everything described below can be supplied through `appsettings.json`, environment variables, User Secrets, or any other configuration provider you have wired into the host.

The Starter Kit ships with a working `appsettings.json` and `appsettings.Development.json`. Use them as your starting point; the tables below explain each setting in detail.

## SitecoreSettings

This section configures how your application talks to Sitecore Experience Edge, how it handshakes with the Pages editor, and how request languages are resolved.

| Property | Type | Default | Description |
| - | - | - | - |
| `SiteName` | string | _required_ | The site name from `Sitecore Deploy → Projects → Environments → Sites → Name`. |
| `EdgeContextId` | string | _required_ | The Edge context identifier from `Sitecore Deploy → Projects → [Environment] → Developer Settings`. Treat as a secret. |
| `EditingSecret` | string | _required_ | Shared secret used to validate authoring callbacks from `Sitecore Deploy → Projects → [Environment] → Developer Settings`. Treat as a secret. |
| `EnableEditingMode` | bool | `false` | When `true`, the SDK exposes the editing endpoints and CORS policy required by the Pages editor. Turn this on for environments that Pages will call into (typically dev and authoring). |
| `EnvironmentHostName` | string? | `null` | The authoring environment hostname (e.g. `xmc-example-dev.sitecorecloud.io`). Required when editing is enabled so the SDK can build absolute callback URLs. |
| `DefaultLanguage` | string | `"en"` | Two-letter or culture code used when no other language can be resolved for a request. Always implicitly allowed. |
| `AllowedLanguages` | List&lt;string&gt; | `[]` | Additional language or culture codes that the site should accept. Pre-loaded at startup. See [Multi-language support](multi-language.md). Values are case-sensitive and passed verbatim to Sitecore Edge. |
| `DefaultPageTemplate` | string | `"Page"` | Name of the Sitecore template used by default when no matching template-mapped Blazor component is found. |
| `AuthoringHosts` | IReadOnlyList&lt;string&gt; | `[]` | Additional hosts that should be permitted by CORS, typically the public hostnames you use to author the site. |
| `EditingOrigins` | List&lt;string&gt; | Sitecore Pages, Design Library | Origins allowed by the editing CORS policy. The defaults cover Sitecore Pages and Design Library; add to this list only if you have custom authoring tooling. |
| `EditingCorsPolicyName` | string | `"AllowSitecoreEditing"` | Name of the CORS policy registered when editing is enabled. Rarely changed. |
| `EditingConfigPackages` | IReadOnlyDictionary&lt;string,string&gt; | empty | Optional list of npm packages and versions returned in the editing handshake payload. Most projects can leave this empty. |
| `DesignOrigin` | string | `"https://designlibrary.sitecorecloud.io/"` | Origin restriction when using Design Studio. |
| `UseJssMediaUrls` | bool | `false` | When `true`, media URLs use the `jssmedia` segment in place of `media`. |
| `WebhookPath` | string | `"/api/webhooks/publish"` | Path for the publish webhook endpoint the SDK registers. |
| `EditingPath` | string | `"/api/editing/config"` | Path for the editing handshake endpoint. Mirrors the `ServerSideRenderingEngineConfigUrl` value on the Default Rendering Host item. |
| `RenderingPath` | string | `"/api/editing/render"` | Path for the editing render callback. |
| `GetTrackingTokenPath` | string | `"/api/token"` | Path for the analytics tracking token endpoint. |
| `CacheResetPath` | string | `"/api/cache-reset"` | Path for the remote cache-reset endpoint. |
| `CacheResetApiKey` | string? | `null` | API key required in the `x-apikey` header for calls to the cache-reset endpoint. Leave `null` to disable the endpoint. |
| `Analytics` | SitecoreAnalyticsSettings | _see below_ | Nested settings for tracking and consent cookies. |

### SitecoreSettings.Analytics

| Property | Type | Default | Description |
| - | - | - | - |
| `Enabled` | bool | `true` | Master switch for analytics. When `false`, no events are recorded. |
| `EnableEventsCookie` | string | `"pw#eventConsent"` | Cookie checked for event-tracking consent. Events are only recorded when this cookie has the value `1`. |
| `EnableProfileCookie` | string | `"pw#profileConsent"` | Cookie checked for guest-profile consent. |
| `EventsCookie` | CookieSettings | `Name = "sc_cid"` | Settings for the event-tracking cookie itself (`Name`, `Domain`, `Path`, `ExpiryDays`). |
| `ProfileCookie` | CookieSettings | `Name = "sc_cid_personalize"` | Settings for the guest-profile cookie. |
| `ServiceEndpoint` | string? | `null` | Override URL for the analytics ingestion endpoint. Leave `null` to use the default. |

## SyncSettings

The SDK keeps an in-process and on-disk cache of layout responses fetched from Sitecore Experience Edge so that the site stays fast under load and continues to serve content if Edge is briefly unavailable. `SyncSettings` controls this cache and the publishing webhooks that keep it fresh.

| Property | Type | Default | Description |
| - | - | - | - |
| `CachingMode` | enum | `MemoryAndDisk` | One of `MemoryAndDisk`, `MemoryOnly`, or `Disabled`. Set to `Disabled` to bypass caching entirely during development. Caching is always disabled during editing. |
| `DataPath` | string | _required_ | Folder used for on-disk cache files. Relative paths resolve against the content root. The Starter Kit uses `./App_Data`. |
| `StoreCompressed` | bool | `false` | When `true`, cached layout responses are compressed on disk. Recommended for production. |
| `MemoryCacheTimeout` | TimeSpan | `01:00:00` | Lifetime for in-memory cache entries. |
| `StorageCacheTimeout` | TimeSpan | `1.00:00:00` | Lifetime for on-disk cache entries. _Note:_ the JSON property `DiskCacheTimeout` is also accepted for backwards compatibility. |
| `PreloadPaths` | IReadOnlyList&lt;string&gt; | `[ "/" ]` | URL paths to fetch and cache at startup so the first user request is already warm. |
| `ChangeFeed` | ChangeFeedSettings? | `null` | Nested settings that wire the SDK into Sitecore Edge's publishing webhook for automatic cache invalidation. Omit to disable. |

### SyncSettings.ChangeFeed

| Property | Type | Default | Description |
| - | - | - | - |
| `ClientId` | string | _required_ | Edge API client identifier. Treat as a secret. |
| `ClientSecret` | string | _required_ | Edge API client secret. Treat as a secret. |
| `WebhookReceiverBase` | Uri | _required_ | The publicly-reachable base URL of this application. The SDK appends `WebhookPath` to this when registering itself with Edge. |
| `AutoCreateEdgeWebhook` | bool | `false` | When `true`, the SDK uses the supplied credentials to register the publish webhook with Sitecore Edge on startup. Set to `false` if you manage webhook registration outside the application. |
| `WebhookApiKey` | string? | `null` | Optional shared secret. When set, incoming publish webhook calls must present this key in the `x-apikey` header. |
| `UniqueId` | string? | `null` | Optional identifier used to disambiguate multiple environments sharing the same Edge tenant. |
| `CacheClearDebounceSecs` | int | `30` | Window during which repeated publish notifications are coalesced into a single cache clear. |

## ComponentResolverSettings

Controls how the SDK discovers Blazor components mapped to Sitecore templates and renderings.

| Property | Type | Default | Description |
| - | - | - | - |
| `SearchNamespaces` | IReadOnlyList&lt;string&gt; | `[ "*" ]` | List of namespaces searched when resolving a Sitecore component or template by name. `"*"` searches every namespace in every loaded assembly. Restrict this list if you want to limit where the resolver looks. |

## Where to put each value

A simple convention works well:

- `appsettings.json` - non-secret configuration that is the same across all environments, plus environment-specific overrides such as `EnvironmentHostName` and `SiteName`.
- `appsettings.Development.json` - developer-only overrides such as detailed errors or pointing `CachingMode` at `Disabled`.
- User Secrets (in development) and environment variables (in deployed environments) - everything sensitive: `EditingSecret`, `EdgeContextId`, `ChangeFeed.ClientId`, `ChangeFeed.ClientSecret`, `CacheResetApiKey`, `WebhookApiKey`.

## Next steps

Continue with [App.razor requirements](app-razor.md).

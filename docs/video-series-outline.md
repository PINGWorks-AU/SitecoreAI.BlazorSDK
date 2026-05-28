# Delivering Headless Sitecore AI with ASP.NET Blazor — Video Series Outline (Draft v0.3)

**Channel name:** *Delivering Headless Sitecore AI with ASP.NET Blazor* — keyword-rich, descriptive, no surprises.

A YouTube channel teaching the PING Works Sitecore AI Blazor SDK through short, focused episodes. Drafted from the existing `/docs` content, the `PINGWebsite` worked example, and the SDK source surface.

## Status — decisions locked

| Decision | Choice |
|-|-|
| Channel name | *Delivering Headless Sitecore AI with ASP.NET Blazor* |
| Pilot vs full first pass | **Hold** — more `/docs` updates coming. Outline is the deliverable for now; scripts wait until docs and the starter kit are stable. |
| Recurring artifact | **Strong-likely: the Starter Kit, expanded episode-by-episode into a mini-site.** Public, ships to customers, doubles the series as a guided tour. Precondition: the starter kit must be brought current first; episode arc then progressively adds components, several of which can be curated from `PINGWebsite` (Banner, LinkList, Title, the variant dispatchers). Public GitHub with one branch per episode lets viewers clone the state at the start of any episode. |
| Presenter style | **AI avatar** — locked. Drives the tool-chain choices in the companion doc. |
| Episode order | Approved — proceed with the 29-episode arc below. |

**v0.3 — what changed since v0.2:** channel name set; presenter style set; recurring-artifact direction marked as starter-kit-as-spine; episode descriptions de-coupled from `PINGWebsite`-specific files so they work whichever artifact is chosen; "Decisions" section retired (now in the table above).

**v0.2 — what changed since v0.1:** re-grounded against the major `/docs` refresh. Two new episodes for the new `multi-language.md` and `error-handling.md` docs; binding episode split into basics + sources; new episode on `SitecoreTemplate<TModel>` route-field binding; early episodes updated to reflect the expanded `anatomy.md` and the new wiring in `installation.md` / `app-razor.md`; gaps list refreshed.

---

## 1. Channel positioning

**Audience (per earlier choice):** existing Sitecore MVC developers + architects/tech leads. Every episode tags one or both.

**Tone:** practical, no waffle. Code on screen by the 30-second mark. Architect-respectful (don't dumb down), MVC-empathetic ("here's how this used to feel in MVC").

**Cadence:** one 3–5 minute episode per week. Bank 4–5 episodes before launch so you're never under pressure to ship a follow-up.

**Recurring artifact (strong-likely):** the Starter Kit, expanded episode-by-episode into a working mini-site. Viewers clone the public GitHub repo and follow along. Each episode corresponds to one branch (`ep-10-richtext`, `ep-11-binding`, etc.) so anyone can pick up at any episode. Components published in `PINGWebsite` (Banner, LinkList, Title, the variant dispatchers) can be curated into the kit as the series progresses — less invention, more curation.

**Episode template (each video):**

1. Cold open (5–10s) — the "from MVC, this used to be…" or "by the end of this video you'll…" hook
2. Concept (30–60s) — the idea, no code yet
3. Live code (2–3 min) — real changes in Visual Studio against the starter kit
4. Recap + next episode tease (15–20s)

---

## 2. Series arc — five phases, 29 episodes

Audience legend: **A** = Architects/tech leads · **M** = MVC developers · **B** = Both

> **Note on file references:** Several episodes name files that today live in `PINGWebsite` (`Banner.razor`, `LinkList.razor`, `Title.razor`, `SitecorePageInspector.razor`, `Form.razor`). Under the starter-kit-as-spine pattern, these files are curated *into* the starter kit on the episode they're taught — file names hold, locations may shift, and a few may be simplified for the kit before they ship.

### Phase 1 — Foundations (the *why*)

| # | Title | Min | Aud | Teaches | Hook |
|-|-|-|-|-|-|
| 1 | Welcome — what this channel is | 2 | B | What the series will cover, who it's for, what won't be covered (Sitecore CMS setup, XM Cloud basics) | "If you've shipped a Sitecore MVC solution and now you're staring at SitecoreAI wondering where everything went — this channel is for you." |
| 2 | From MVC to Blazor: why the change? | 4 | A | Headless rationale, why Blazor specifically, what *doesn't* change (templates, content, datasources), what *does* (controllers gone, Glass gone, server-rendered MVC gone) | The Glass Mapper / Synthesis / @Html.Sitecore() world vs. the new component model |
| 3 | What the SDK gives you | 4 | A | High-level tour of capabilities — routing, components, binding, editing chrome, analytics, caching, sync, multi-language, error handling — the value-prop slide deck in video form | "Eight things the SDK does so you don't have to build them" |
| 4 | Project anatomy: the Starter Kit layout | 4 | B | The full top-level layout from `anatomy.md`: `/apps/PINGWebsite`, the `PINGWorks.Web.GraphQL` codegen companion, `Generate-GraphQL.ps1`, `/authoring`, `xmcloud.build.json`. Then the inside of `PINGWebsite`: `Ioc/AddPingWebsite()`, the dual `UI/` + `PingUI/` library pattern, `Processors/`, `Repositories/`, the `BlazorSDK_*.lic` license files. ProjectReference vs PackageReference for the SDK | Walk the folder tree in Solution Explorer, point at every file the developer is likely to touch |

### Phase 2 — Getting Started (the *first build*)

| # | Title | Min | Aud | Teaches | Hook |
|-|-|-|-|-|-|
| 5 | Install: clone, rename, run | 5 | B | Clone, rename csproj/solution/RootNamespace, restore, `dotnet run`, hit `/`. Updated dependency callouts (MudBlazor, BitzArt.Blazor.Cookies.Server, Azure.Communication.Email) | First success: a Sitecore page rendered by Blazor |
| 6 | Configure: the three settings sections | 5 | B | `SitecoreSettings` (SiteName, EdgeContextId, EditingSecret, **EnvironmentHostName**, **AllowedLanguages**, DefaultPageTemplate, the Analytics sub-section), `SyncSettings` (**CachingMode**: MemoryAndDisk / MemoryOnly / Disabled, **StoreCompressed**, ChangeFeed wiring), `ComponentResolverSettings`. User secrets vs env vars vs appsettings | "Three sections, every knob explained" — leans on the now-substantial `settings.md` |
| 7 | Wiring it up: Program.cs, Routes.razor, App.razor | 6 | B | `AddSitecoreBlazorSDK`, `UseSitecoreBlazorSDK`, `ContentSecurityFrameAncestorsPolicy = null`, `AddAdditionalAssemblies(typeof(SitecorePage).Assembly)`, **`<SitecoreRouter>` in `Routes.razor` with `NotFoundPage="typeof(Pages.Status)"`**, **`UseRequestLocalization()`**, **`UseStatusCodePagesWithReExecute("/status-{0}")`**, the four `<SectionOutlet>` outlets (HeadLayout, HeadPage, ScriptLayout, ScriptPage) and the new **`SitecoreEditingScripts`** outlet | "Three files, everything the SDK needs to know about your app" |
| 8 | From scratch: adding the SDK to an existing Blazor app | 5 | A,M | The updated 10-step "Installing from scratch" recipe from `installation.md`: .NET 10, server-side rendering, package add, the new middleware ordering (`UseRequestLocalization` before `UseAntiforgery`, `UseStatusCodePagesWithReExecute` for error pages), the `<SitecoreRouter>` swap in `Routes.razor` | For teams who don't want the starter kit |

### Phase 3 — Building Components (the *daily skill*)

| # | Title | Min | Aud | Teaches | Hook |
|-|-|-|-|-|-|
| 9 | Pages, Templates, and Components: the three concepts | 4 | M | What each maps to in Sitecore, `SitecoreComponent` vs `SitecoreTemplate`, name resolution, `[SitecoreComponent("…")]` override (e.g. `Page.razor` registering itself as `Default` too) | "Three Sitecore words, three Blazor base classes, one-to-one mapping" |
| 10 | Your first component: a RichText | 5 | M | Inherit `SitecoreComponent<TModel>`, the nested `ViewModel` convention, `<ScRichText Field="…" />`, run it, edit it in Pages | Live-port the `RichText.razor` from the worked example |
| 11 | Model binding basics: properties become fields | 5 | M | Automatic name matching, `[SitecoreComponentField]`, `[SitecoreComponentParam]`, `[SitecoreIgnore]`, the `Glass Mapper` replacement story, `ViewModelBase` (Styles, RenderingVariant, GridParameters) | "Everything Glass did, with no mapping layer" |
| 12 | **NEW** — Beyond fields: routes, linked items, well-known properties | 5 | M | The full 8-priority binding source table from the rewritten `models.md`: `[SitecoreRouteField]`, `[SitecoreRouteProperty]` (ItemId, TemplateName, …), `[SitecoreLinkedItemField]`, `[SitecoreLinkedItemProperty]`, `[SitecoreComponentProperty]`, `[SitecoreRestoreHierarchy]` for nested item collections, multi-attribute fallback | "Eight binding sources — when each one wins, and why you almost never need to think about it" |
| 13 | **NEW** — Page-level fields with SitecoreTemplate | 4 | M | `SitecoreTemplate<TModel>` for the page item itself, `[SitecoreRouteField] PageTitle / MetaDescription / MetaKeywords`, pushing into `HeadPage` SectionOutlet, the SEO pattern | "How `Page.razor` becomes your page's SEO surface" |
| 14 | Datasources and complex content | 4 | M | The `Datasource` JSON property, `LinkListDatasource` pattern (nested POCOs), `[SitecoreComponentField("data")]` for differently-named datasource fields, custom converters | Live-walk `LinkList.razor` and `Title.razor` from the worked example |
| 15 | Field components: the Sc-prefix toolbox | 4 | M | `ScText`, `ScRichText`, `ScImage`, `ScLink`, `ScDate`, `ScNumber`, `ScFile`, `ScPlaceholder` — what each one renders, the universal `Editable` flag, when to override field defaults | "Eight components replace `@Html.Sitecore().Field()`" |
| 16 | Placeholders: composing layouts | 4 | M | `<ScPlaceholder Name="…" ParentId="…" />`, the placeholder hierarchy, the new `ParentId` parameter and when interactive children need it, how `DynamicComponent` resolves children at runtime | Drop a placeholder, drop components into it from Pages editor |
| 17 | Variants: one component, many faces | 5 | M | The dispatcher pattern, `ComponentNameVariants/` sibling folder, `VARIANT_*` constants, branching on `Model.RenderingVariant` from `ViewModelBase` | Live-walk `Banner.razor` + its `Banners/` folder from the worked example |
| 18 | Images, srcset, and the media pipeline | 4 | M | `ScImage` advanced — `SrcSet`, `Sizes`, `ImageParams` as an anonymous object, responsive image patterns | "How to ship responsive images without writing `<picture>` tags by hand" |

### Phase 4 — Content, Editing & Data (the *system view*)

| # | Title | Min | Aud | Teaches | Hook |
|-|-|-|-|-|-|
| 19 | The Pages editor: editing chrome explained | 5 | B | How `Editable` flips inline editing on, `EnableEditingMode`, the editing CORS policy, `EditingOrigins`, the `SitecoreEditingScripts` outlet (automatically populated by the SDK), the editing-chrome render path | "Pages just *works* — here's why" |
| 20 | **NEW** — Multi-language: AllowedLanguages and the request culture resolver | 5 | B | `DefaultLanguage` + `AllowedLanguages`, the four-step resolver (`sc_lang` query in editing → `/fr/about` URL segment → `Accept-Language` header → default), `UseRequestLocalization` placement, URL-prefix vs per-domain strategies, reading `CultureInfo.CurrentUICulture` inside components — sourced from the new `multi-language.md` | "How `/fr/about` becomes a French page without you writing any routing" |
| 21 | **NEW** — Error handling: 404, 500, and the Status page | 5 | B | `UseStatusCodePagesWithReExecute("/status-{0}")`, `UseExceptionHandler("/status-500")`, the `Status.razor` parameterised page, `<ScErrorContent />` three-step render order (developer detail → Sitecore-authored `Error404`/`Error500` site fields → built-in fallback), `Fallback404Content` / `Fallback500Content` overrides — sourced from the new `error-handling.md` | "Authored 404 pages that look like the rest of your site" |
| 22 | GraphQL under the hood: how content arrives | 5 | A | Experience Edge contract, `IPageProvider.GetPage` vs `GetPageRaw`, what queries get issued, why GraphQL beats Item Web API, **`Generate-GraphQL.ps1` and the `PINGWorks.Web.GraphQL` codegen project** for custom queries | For architects who want to see the wire format |
| 23 | Caching, sync, and webhooks | 5 | A | The full `SyncSettings` model — `CachingMode`, disk + memory cache, `StoreCompressed`, `PreloadPaths` (per-language warm-up), publish webhooks, `CacheClearDebounceSecs`, `IPublishUpdateNotifier`, `ICacheEvictor`, the manual `CacheResetPath` API with `CacheResetApiKey` | "How a site stays fast and fresh at the same time" |
| 24 | Tracking and analytics | 4 | A | `ISitecoreTracking`, `RecordPageView`, `RecordCustomEvent`, `RecordFormEvent`, `IdentifyUser`, the `EnableEventsCookie` / `EnableProfileCookie` consent gate, the xConnect-successor story | "What replaced xConnect, and how to use it" |

### Phase 5 — Migration, Production & Operations (the *real world*)

| # | Title | Min | Aud | Teaches | Hook |
|-|-|-|-|-|-|
| 25 | Migrating an MVC rendering to Blazor: a worked example | 5 | M | Take a real MVC view (Two Columns or PageTitle), port it to a Blazor component, register the rendering as JSON Rendering, fix datasource locations — sourced from `process.md` rough notes (and an opportunity to formalise that doc) | "Live-port one rendering, end to end" |
| 26 | Mixing Blazor pages with Sitecore routes | 4 | M | The `/{*RequestPath}` fallback story, when to use real Blazor `@page` routes (e.g. the diagnostic `SitecorePageInspector`), order of route resolution, `NotFoundPage` on `<SitecoreRouter>` | Show `UI/Pages/SitecorePageInspector.razor` alongside Sitecore content |
| 27 | A real form: Contact + processors + Teams notification | 5 | M | The `Form.razor` + `AddPingWebsite()` + `IFormProcessor` pipeline + `SendToTeamsProcessor`, the `/api/webhooks/form-data` endpoint — sourced from `send-to-teams-processor.md` | Submit a contact form → see it land in a Teams channel live |
| 28 | Production hardening: SSR, license files, webhook security | 4 | A | What changes between `dotnet run` and `dotnet publish`: deploying `BlazorSDK_*.lic`, `WebhookApiKey` on publish callbacks, `CacheResetApiKey` on the reset endpoint, environment-variable secrets, the obfuscated release build, `EnableEditingMode = false` for production hosts | "What changes between `dotnet run` and `dotnet publish`" |
| 29 | Where to next: extending the SDK | 3 | B | Custom `IFieldValueConverter`, custom `IFormProcessor`, custom `IPageProvider`, the `IInitialiseOnStartup` hook, the `Repositories/` pattern for domain-specific data access — channel sign-off | "You've hit the limits of the SDK — here's how to push past them" |

---

## 3. Pilot proposal — make these five first

If you want to validate the format and audience before committing to all 29, shoot these in order — they cover the full arc in microcosm:

1. **Ep 1** — Welcome (sets channel identity)
2. **Ep 2** — From MVC to Blazor: why the change? (the architect hook)
3. **Ep 7** — Wiring it up: Program.cs, Routes.razor, App.razor (the practical wire-up — now covering the `<SitecoreRouter>` swap and the new middleware ordering)
4. **Ep 10** — Your first component: a RichText (the first "I built something" moment)
5. **Ep 11** — Model binding basics (the killer feature)

If episode 10 lands well, the rest of phase 3 writes itself. If episode 2 lands well, you know the architect audience is there. Episode 12 (the advanced binding sources) makes a natural episode 6 if the binding episode resonates and you want to keep pushing into the practical material before the pilot wraps.

---

## 4. Gaps in the existing `/docs` worth flagging

The v0.1 outline flagged `settings.md` as a stub — it's now fully fleshed out, along with new docs for multi-language and error handling. Three gaps remain, and a fourth is worth adding:

- **`process.md`** — still rough migration notes. Episode 25 will need to formalise the migration steps; this doc should grow into a proper migration guide as the script is written.
- **No dedicated doc on analytics/tracking** — episode 24 will write this. `ISitecoreTracking`, the consent-cookie model from `Analytics` settings, and the xConnect-successor story have no end-user-facing doc yet.
- **No dedicated doc on caching/sync as a concept** — `settings.md` documents every knob, but there's no narrative doc explaining the model (memory + disk layers, debounced publish webhooks, preload, `IPublishUpdateNotifier` / `ICacheEvictor`). Episode 23 will write this.
- **No dedicated doc on the editing-chrome story** — episode 19 will write this. The setting and outlet behaviour is documented but the conceptual "what does editing mode actually do" story is missing.
- **No dedicated doc on the GraphQL codegen workflow** — `anatomy.md` now mentions `PINGWorks.Web.GraphQL` and `Generate-GraphQL.ps1`, but the "how to add a custom query" workflow has no page. Episode 22 will write this.

The video scripts will naturally generate this content — each episode's script can be promoted into the corresponding doc page.

---

## 5. What this outline deliberately does *not* cover

- Sitecore CMS / XM Cloud setup (templates, content trees, environments) — assumed knowledge, signposted in Ep 1
- Blazor fundamentals (components, parameters, lifecycle) — assumed; this is not a "learn Blazor" channel
- General .NET deployment (Azure, containers) — only the SDK-specific bits in Ep 24
- The `PINGWorks.SitecoreExperienceEdge.*` lower-level SDKs — these are dependencies, not the teaching surface

These could become "Season 2" topics if there's appetite.

---

## 6. Next steps

Decisions are locked (see Status table at top). Open work:

- **Wait on docs.** Doc refresh is in progress; scripts won't start until the docs and the starter kit are both current.
- **Bring the starter kit current.** Precondition for the starter-kit-as-spine pattern. Once it's at parity with the v1.9 SDK and `PINGWebsite`'s patterns, we can plan the episode-to-branch progression.
- **Plan the branch progression** for the public GitHub. Each episode → one branch. Ep 5 (clone, rename, run) seeds the repo. Eps 7–18 each add one folder/file/feature. The diff between branch `ep-N` and `ep-N+1` is the on-screen change.
- **AI production stack.** See the companion doc `video-series-production-stack.md` — driven by the AI-avatar decision.

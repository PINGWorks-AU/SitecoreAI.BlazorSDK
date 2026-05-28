# Models &amp; Binding
[&lt; Docs index](index.md)

The Blazor SDK includes an advanced and convenient model binding system that makes creating components a breeze.

When creating a new component, inherit from `SitecoreComponent<TModel>` and pass the type you want to use as a model into the generic parameter at `TModel`. By convention we create a `ViewModel` class that is a child of the component type, minimising the chances of breaking changes through component isolation and cohesion principles.

A simple example is shown:

```html
@inherits SitecoreComponent<RichText.ViewModel>

<div class="component rich-text @Model.GridParameters @Model.Styles" id="@Id">
	<div class="component-content">
		<ScRichText Field="@Model.Text"></ScRichText>
	</div>
</div>

@code {
	public sealed class ViewModel : ViewModelBase
	{
		// This field is mapped automatically because the property name matches the Sitecore field name
		public RichTextField? Text { get; set; }
	}
}
```

In the Starter Kit we use a small `ViewModelBase` to give every component the rendering-variant and grid parameters that almost every Sitecore component needs:

```cs
public abstract class ViewModelBase
{
	public MarkupString? Styles { get; set; }
	[SitecoreComponentParam( "FieldNames" )] public string? RenderingVariant { get; set; }
	[SitecoreComponentParam] public string? GridParameters { get; set; }
}
```

The base class, `SitecoreComponent`, provides the following members to your component automatically:

| Member | Type | Description |
| - | - | - |
| `Model` | `TModel` | The bound model instance. Created and populated by the SDK during initialisation. |
| `Context` | `SitecoreContext?` | The full rendering context for the current component - layout, route, current component and the placeholder it sits in. Available as a cascading parameter. |
| `Id` | `string?` | Convenience accessor for the current component's id (`Context.Component.Id`). |
| `IsEditing` | `bool` | `true` when the component is being rendered inside the Sitecore Pages editor. |

## Binding sources and priority

The SDK can bind a property from any of the following sources, in this priority order (lowest number wins when more than one source resolves a value):

| Priority | Attribute | Source |
| - | - | - |
| 0 | `[SitecoreIgnore]` | Skip - the property is not bound at all. |
| 1 | `[SitecoreComponentField]` | A field on the current component's datasource item. |
| 2 | `[SitecoreComponentParam]` | A value from the component's rendering parameters. |
| 3 | `[SitecoreComponentProperty( name )]` | A well-known property of the current component (e.g. `UId`, `ComponentName`, `DataSource`, see `SitecoreComponentPropertyNames`). |
| 4 | `[SitecoreLinkedItemField]` | A field on a linked item, used during recursive binding of `ItemLinkField` collections. |
| 5 | `[SitecoreLinkedItemProperty( name )]` | A well-known property of a linked item (e.g. `Id`, `Url`, `Name`, `DisplayName`, see `SitecoreLinkedItemPropertyNames`). |
| 6 | `[SitecoreRouteField]` | A field on the page (route) item. Use this for page-level fields such as the page title or meta description. |
| 7 | `[SitecoreRouteProperty( name )]` | A well-known property of the route (e.g. `Name`, `DisplayName`, `ItemId`, `TemplateName`, see `SitecoreRoutePropertyNames`). |

When you don't apply any attribute, the binder tries every source in priority order and takes the first that resolves a non-null value. This means that property names that match a Sitecore field will bind automatically; you only need an attribute when you want to force a particular source or override the name.

If you apply more than one attribute to the same property, every listed source is tried in priority order. A single Sitecore data element can also bind to more than one model property by giving each property its own attribute.

## Binding - name resolution

The binder **ONLY** supports <u>public writable properties</u>, though model classes themselves do not need to be `public`.

If you want the binder to ignore a property that is writable, decorate the property with `[SitecoreIgnore]`.

By default the binder matches a property by name against the source. Each attribute also accepts an optional `name` argument that overrides the property name:

```cs
public sealed class ViewModel : ViewModelBase
{
	// Bound automatically because 'Text' matches the field name
	public RichTextField? Text { get; set; }

	// Same source, exposed as a MarkupString under a different name
	[SitecoreComponentField( "Text" )]
	public MarkupString? TextMarkup { get; set; }

	// Force a rendering parameter rather than a field
	[SitecoreComponentParam( "FieldNames" )]
	public string? RenderingVariant { get; set; }

	// Pull a value from the page (route) rather than the component
	[SitecoreRouteField]
	public string? PageTitle { get; set; }
}
```

## Binding - data types

Properties bound to component parameters and other string sources are passed through ASP.NET's built-in `BindConverter` to convert the supplied string to your chosen type. Mileage varies, so keep the property type nullable and experiment with anything more exotic than a primitive.

Properties bound to **field** sources support the following types:

- `IField` derivatives - types from the Sitecore Experience Edge Content SDK (`RichTextField`, `StringField`, `HyperlinkField`, `ImageField`, and so on). These are required to support the full Pages inline editing experience.
- `MarkupString` and `MarkupString?` - the field's HTML content, ready to be rendered verbatim by Blazor.
- `string?` - the field's raw underlying value as a string. Use this when you don't need editing support and want to read the JSON value yourself.
- Primitive types (`int`, `decimal`, `DateTime`, `Guid`, and so on) - converted using the Content SDK's typed `Field<T>` accessor. Type conversion must be supported by that underlying type.

Conversion is handled by classes that implement `IFieldValueConverter`. You can register your own converter against the DI container to override or extend the built-in set; the binder picks the first converter (by `Priority`) whose `CanConvert` returns `true`.

## Binding - datasource as JSON

When a Sitecore UI component has a datasource, the SDK exposes the raw JSON for that datasource through a property called `Datasource`. To take advantage of it, add a property of your own that deserialises the JSON into a POCO. The simplest approach is to name your property to match the field you want to bind and let name matching do the work:

```cs
public sealed class ViewModel : ViewModelBase
{
	public LinkListDatasource? Datasource { get; set; }

	public sealed class LinkListDatasource
	{
		public LinkListField? Field { get; set; }
		public LinkListChildren? Children { get; set; }

		// ...nested types...
	}
}
```

See the Starter Kit's `LinkList.razor` for a complete worked example, or `Title.razor` for the equivalent pattern using `[SitecoreComponentField( "data" )]` to bind a different field name to a `data` property.

## Collections and `SitecoreRestoreHierarchy`

So far every model has bound a single datasource item. Many real components instead need a *list* of items: a blog landing page listing its posts, an accordion built from a set of child items, a navigation menu built from a tree of pages. This is where datasource resolvers and `[SitecoreRestoreHierarchy]` come in.

### Where the list comes from: datasource resolvers

By default a rendering's layout data contains the fields of its single datasource item. To make Sitecore include a *collection* of items in the layout, you assign a **Rendering Contents Resolver** (often called a datasource resolver) to the rendering. The resolver decides which items are serialised into the layout for that rendering. The available resolvers are managed in the Sitecore Content Editor at:

```
/sitecore/system/Modules/Layout Service/Rendering Contents Resolvers
```

The ones you'll reach for most often return more than the datasource item alone:

- **Datasource Item Children Resolver** - the children of the datasource item.
- **Context Item Children Resolver** - the children of the current page item.
- **Datasource Item and Children Resolver For BlazorSDK** - the datasource item *and* its children, added by the BlazorSDK starter kit.

You can also write a resolver that runs a Sitecore query such as `./descendant-or-self::*`, which returns the datasource item and every item beneath it, at any depth.

Whichever resolver you choose, the items it returns arrive in the layout as a **flat array** under a field named `items` - even when, in the content tree, those items form a parent/child hierarchy several levels deep. The resolver flattens the tree on the way out.

### The problem: a flat list that used to be a tree

A `./descendant-or-self::*` query against a three-level menu returns every node in one flat array, with no nesting. Each item does, however, carry its own `Url` (its content-tree path), which records where it sat in the tree. `[SitecoreRestoreHierarchy]` uses those paths to put the tree back together before binding.

### What `[SitecoreRestoreHierarchy]` does

Apply the attribute to a `List<T>` property that should receive the `items` collection. Before binding, the SDK:

1. **Removes the "self" item.** If the returned array contains the rendering's own datasource (or context) item - as it does with a `descendant-or-self` query or the *Item and Children* resolver - that item is taken out of the list and its fields are lifted onto your model directly. This keeps the anchor item out of your child collection, where it doesn't belong.
2. **Rebuilds the hierarchy by path.** Items are de-duplicated by `Url`, then each item's parent is located by trimming the last segment off its path. A child found to have a parent in the array is nested under that parent (in a nested `items` field) and removed from the top level. What remains in your list is the set of *top-level* items, each with its descendants nested beneath it.

The binder then binds the reshaped tree recursively, so a nested `List<T>` on your item model is populated with that item's children.

Without the attribute the `items` array binds as-is: you get a single flat list containing every returned item (including the anchor and all descendants), with no nesting. Use `[SitecoreRestoreHierarchy]` whenever the resolver returns a hierarchy you want to walk as a tree, or simply to drop the self item from a children list.

> [!NOTE]
> The collection is matched to the `items` field by name, so name your property `Items` (case-insensitive) or apply `[SitecoreComponentField( "items" )]` to bind a differently-named property.

### Worked example - a flat list

`BlogContentList.razor` lists blog posts returned by a resolver. The posts are siblings, so there's no nesting to rebuild; the attribute's job here is to bind the `items` collection and drop the landing-page item itself from the list:

```razor
@inherits SitecoreComponent<BlogContentList.ViewModel>

@foreach ( var item in Model?.Items ?? Enumerable.Empty<BlogItemModel>() )
{
	<a href="@item?.Url">@item?.PageTitle?.Value</a>
	<p>@item?.Date?.ToString( "d MMMM yyyy" )</p>
}

@code {
	public sealed class ViewModel : ViewModelBase
	{
		[SitecoreRestoreHierarchy]
		public List<BlogItemModel>? Items { get; set; }
	}

	public class BlogItemModel
	{
		public StringField? Category { get; set; }
		public string? Url { get; set; }            // bound from the item's path
		public DateTime? Date { get; set; }
		public StringField? PageTitle { get; set; }
		public ImageField? Thumbnail { get; set; }
	}
}
```

Each `BlogItemModel` binds exactly like a normal model: field-typed properties (`StringField`, `ImageField`) for editable fields, primitives (`DateTime?`) for converted values, and `Url` for the item's path.

### Worked example - a nested hierarchy

`Accordion.razor` is configured with a resolver that returns a hierarchy, so its item model carries its *own* `Items` list. The binder fills that nested list with each item's children:

```razor
@inherits SitecoreComponent<Accordion.ViewModel>

@code {
	public class ViewModel : ViewModelBase
	{
		public StringField? AccordionTitle { get; set; }

		[SitecoreRestoreHierarchy]
		public List<AccordionItemModel> Items { get; set; } = [];

		public class AccordionItemModel
		{
			public string? Id { get; set; }
			public string? Url { get; set; }
			public string? DisplayName { get; set; }

			public ImageField? AccordionImageDesktop { get; set; }
			public StringField? AccordionHeading { get; set; }
			public StringField? AccordionText { get; set; }

			// children of this item, rebuilt from the flat array by path
			public List<AccordionItemModel> Items { get; set; } = [];
		}
	}
}
```

Because `AccordionItemModel` declares a nested `Items` of the same type, the restored tree binds all the way down: top-level entries land in `ViewModel.Items`, their children in each item's `Items`, and so on. Note that the *nested* `Items` does not need its own `[SitecoreRestoreHierarchy]` attribute - the restore happens once, on the root collection, and the reshaped data binds recursively from there.

> [!NOTE]
> Parent items, children and grand-children do not all need to be the same item Template and the ViewModel shapes can also be different at each level, but each 'tier' of the hierarchy can only have items of the same type. That is, covariance is not supported for properties of type List&lt;T&gt; because Sitecore does not supply any fields that can be used as type discriminators, which is required for de-serialisation of covariant types in C#.
>
> To gain full control over the deserialisation process, return the "Items" property as a string and it will contain the source Json. You can then implement manual deserialisation to handle edge cases not supported by the default binder.

## Templates

`SitecoreTemplate<TModel>` provides the same binding behaviour for page-template components as `SitecoreComponent<TModel>` does for renderings. Use it when your component is mapped to a Sitecore page template (the file lives in `UI/Templates`). The default `Page.razor` in the Starter Kit shows the pattern:

```cs
public class ViewModel
{
	[SitecoreRouteField] public string? PageTitle { get; set; }
	[SitecoreRouteField] public string? MetaDescription { get; set; }
	[SitecoreRouteField] public string? MetaKeywords { get; set; }
}
```

## Supporting types

### SitecoreContext

**Namespace:** `PINGWorks.SitecoreAI.BlazorSDK.Data`<br/>
**Assembly:** `PINGWorks.SitecoreAI.BlazorSDK`

The cascading context object available on every Sitecore-rendered component.

| Property | Type | Description |
| - | - | - |
| `IsEditing` | `bool` | `true` if the page is being rendered in the Pages editor. |
| `Route` | `Route?` | The current page (route) item, including its fields and placeholders. |
| `Page` | `RouteContext?` | The top-level context object, including site name, language and visitor identity. |
| `Component` | `SitecoreComponent?` | The current rendering, when one is in scope. |
| `PlaceholderKey` | `string?` | The placeholder key the current rendering sits in. |
| `Placeholders` | `Dictionary<string, Placeholder>` | Placeholders available at the current scope. |
| `Components` | `IEnumerable<SitecoreComponent>` | Renderings in the current placeholder. |
| `Devices` | `IReadOnlyList<Device>` | The set of devices defined for the page. |
| `Path` | `string?` | An internal path representation used by the SDK to disambiguate nested components. |

### `SitecoreComponentAttribute`

**Namespace:** `PINGWorks.SitecoreAI.BlazorSDK.Components`<br/>
**Assembly:** `PINGWorks.SitecoreAI.BlazorSDK`

Applied to a Blazor component to override the Sitecore name used for resolution. See [Pages, Templates and Components](components.md).

### `IFieldValueConverter`

**Namespace:** `PINGWorks.SitecoreAI.BlazorSDK.Binding.FieldConverters`<br/>
**Assembly:** `PINGWorks.SitecoreAI.BlazorSDK`

Implement this interface and register your converter in DI to add or override how Sitecore field values are mapped to model property types. System converters have `Priority = 0`; use a larger positive value to win over them.

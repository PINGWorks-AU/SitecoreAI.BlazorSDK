1. Inspect the presentation of the page to determine what structure is being currently used.
2. Currently, many Content Half Width, Two Columns, Page Title, Breadcrumb and Image
3. Take Content Half Width, find the renderings and put them in a package (include any templates)
4. import into Cloud -> SitecoreAI -> Settings -> Desktop -> Installation Manager
5. Change layout to headless layout in presentation

Now we have to migrate all the renderings from View Renderings to JSON Renderings to link them to the Blazor setup


Migration notes:

- For migrated pages to show up in Pages hierarchy, they need an existing layout
- Renderings require: 
	- Component Name
	- Parameters Template (use a standard empty one if not specific
	- Datasource Location
	- Datasource Template
	- Rendering Contents Resolver
	- Ensure the Layout Service Placeholders for any page structure include the placeholders that are defined in the Layout-> Placeholder Settings 
	
- Use :deep in any embedded css
- Use ```<ComponentScript Src="./PingUI/Components/<ComponentName>.razor.js" /> ```for embedded js

```
query{
  site{ siteInfo(site:"website"){ sitemap}}
}

- publishing PingWorks.SitecoreExperienceEdge.[stuff] to VSTS, make sure the packagereferences are to nuget and not local
- ensure that the versioning is correct and that releasenotes are edited
- For variants to show, ensure that the page layout inheritance includes sitecore/templates/Foundation/JSS Experience Accelerator/Multisite/Base Page
```
- check that any imported basic page matches the new system in regards to inheritance, so that stuff like Variants work with the imported page templates
- inherit (/sitecore/templates/Foundation/JSS Experience Accelerator/Multisite/Base Page)

- check that datasource location filters are correct or are adjusted to the new folder structure
- we used 
```
cd /sitecore/layout/renderings

$before = "query:./ancestor-or-self::*[@@templateid='{98A3D034-92E4-4ECB-9632-119CABD08E15}']/*[@@templateid='{B2076F9F-E735-4F31-91CF-597524B76A1E}']"
$after  = "query:./ancestor-or-self::*[@@templateid='{D88745CE-9BAC-4DD6-9455-76843A221D6B}']/Data"

gci -Recurse |
        ? { $_.TemplateName -eq "Json Rendering" -and ($_."Datasource Location").StartsWith( $before ) } |
        % {
            <#
            $_."Datasource Location" = $_."Datasource Location".Replace($before, $after)
            Write-Host "$($_.Name) updated"
            #>
            
            # check
            write-host "Name: $($_.Name) NewDsLocation: $(($_.`"Datasource Location`").Replace($before, $after)) "
        }
        ```
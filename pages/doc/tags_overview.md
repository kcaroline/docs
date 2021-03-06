---
title: Organizing with Tags
tags: [getting started, alerts, dashboards, events, videos]
sidebar: doc_sidebar
permalink: tags_overview.html
summary: Learn use tags to speed up searching and query display and how to manage entity tags.
---
A tag is custom metadata that adds application-specific meaning to Wavefront _entities_: alerts, dashboards, events, and sources and _metrics_. Tags group together entities and metrics according to categories you define.

You use tags to limit the number entities and metrics you are working with or querying at once. Limiting the number of entities reduces information overload. Limiting the number of metrics reduces the time to display results.

In the Wavefront UI and API you can use entity tags to filter alert, dashboard, event, and source entities. In the Wavefront UI, entity tags display as gray labeled icons ![tag](images/tag.png#inline) in the filter bar and below each entity in the entity browser.

In ts() and events() queries, you can filter:

-   Metrics with _source_ and _point_ tags
-   Events with:
    - _alert_ and _event_ entity tags
    - _system_ severity, subtype, and type tags added by alerts

The table summarizes where the two types of tags are used and where they are added and updated.

<table>
<colgroup>
<col width="16%"/>
<col width="28%"/>
<col width="28%"/>
<col width="28%"/>
</colgroup>
<thead>
<tr>
<th>Tag Type</th>
<th>Used in UI and API</th>
<th>Used in Queries</th>
<th>Where Added and Updated</th>
</tr>
</thead>
<tbody>
<tr>
<td>alert</td>
<td markdown="span">[Managing Alerts](alerts_managing.html) and [Managing Maintenance Windows](maintenance_windows_managing.html)</td>
<td markdown="span">[Basic events() Queries](events_queries.html)</td>
<td markdown="span">Wavefront UI and API</td>
</tr>
<tr>
<td>dashboard</td>
<td markdown="span">[Managing Dashboards](dashboards_managing.html)</td>
<td></td>
<td markdown="span">Wavefront UI and API</td>
</tr>
<tr>
<td>event</td>
<td markdown="span">[Managing Events](events_managing.html)</td>
<td markdown="span">[Basic events() Queries](events_queries.html)</td>
<td markdown="span">system tags added by alerts<br /><br />entity tags added in the Wavefront UI and API</td>
</tr>
<tr>
<td>point</td>
<td></td>
<td markdown="span">[Wavefront Data Format](wavefront_data_format.html) and [Point Tags in Queries](query_language_point_tags.html)</td>
<td markdown="span">Wavefront proxy<br />[Configuring Wavefront Proxy Preprocessor Rules](proxies_preprocessor_rules.html)<br /><br />
Telegraf agent<br />
[Wavefront CLI](wavefront_cli.html)</td>
</tr>
<tr>
<td>source</td>
<td markdown="span">[Managing Sources](sources_managing.html) and [Managing Maintenance Windows](maintenance_windows_managing.html)</td>
<td markdown="span">[Getting Started with Wavefront Query Language](query_language_getting_started.html)</td>
<td markdown="span">Wavefront UI and API</td>
</tr>
</tbody>
</table>

## Tag Paths

All tag types support the ability to organize tags in a hierarchy. The hierarchy is defined by separating tag components with a dot ".". For example: **MyService.MyApp**. Dashboards provided by Wavefront start with the tag path component **wavefront.**. To improve readability, tags retain case for display but are treated case-insensitive for searching, sorting, etc.

### Selecting and Searching Tag Paths

In the UI you operate on tag paths by selecting a component at a specific node in the hierarchy.  For example, you can select all Wavefront dashboards by clicking **wavefront**, or only tutorial dashboards by expanding the **wavefront** node and selecting **wavefront.tutorial**.

Here's a video overview: 

{% include video.html file="u1vzsuttf6" %}

In queries you achieve the same effect by using trailing wildcards "**.\***" when specifying tag paths. For example, to match all tags starting with **alertTagPath.**, enter **alertTagPath.\***. This string matches alerts named **alertTagPath.tpc1**, **alertTagPath.tpc1.tpc11**, etc. When creating maintenance windows you can use tag paths and wildcards to put a group of of alerts in maintenance.

<a name="entity_tags"></a>

## Managing Entity Tags

Entity tags are tags that apply to Wavefront entities: alerts, dashboards, events, and sources.

{% include shared/permissions.html entity="entity tags" entitymgmt="Alert, Dashboard, Event, or Source Tag" %}

### Adding Entity Tags

To add tags to one or more entities:

1.  Open an entity browser by selecting **Browse &gt; &lt;entity&gt;**, where **&lt;entity&gt;** is **Alerts**, **Dashboards**, **Events**, or **Sources**.
2.  Choose which entities to tag:
    -   Check the checkboxes next to the entities and click the **+ Tag** button.
    -   Click **+tag** below an entity.

        ![source tags](images/source_tags.png)

3.  In the **Add Tag** dialog:

    ![add tag](images/add_tag.png)

    -   Click the **Create Tag** button at the bottom:
        1.  Type a tag name. Tag names can contain alphanumeric (a-z, A-Z, 0-9), dash (-), underscore (\_), and colon (:) characters. Tag names are *case sensitive*. For example, the tags **MyApp** and **myapp** are stored as distinct tags. However, mixed case tag paths are collapsed into one path; **MyService.myapp** and **myservice.myapp** are collapsed into **Myservice.myapp**.
        2.  Click **Add**.

### Searching for Entity Tags

When there are many tags you can search for tags by typing tag names in the Search box below the Tags heading in the filter bar:

![search tags](images/search_tags.png)

As you type in the Search box, the list of tags below is filtered by the search string. When you search for tags, the search process is *case insensitive*. For example, searching for the tag **myapp** returns **MyApp** and **myapp.** Similarly, searching for the tag **MyApp** returns **MyApp** and **myapp**.

### Filtering by Entity Tags

To filter by a tag, click a tag icon, for example ![mytag icon](images/mytag_icon.png#inline):

-   In the filter bar:

    ![mytag2](images/mytag2.png)

-   Below an entity in an entity browser:

    ![mytag](images/mytag.png)
    

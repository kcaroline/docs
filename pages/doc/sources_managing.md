---
title: Managing Sources
keywords: sources
tags: [sources]
datatable: true
sidebar: doc_sidebar
permalink: sources_managing.html
summary: This describes how to manage sources.
---

## What is a Source?

A source is a unique application, host, container, or instance that emits metrics. The source is explicitly set
in the **source** field of a [Wavefront data format](wavefront_data_format) metric. For
cloud integrations, the source is extracted from [AWS service properties or dimensions](integrations_aws_metrics#aws_sources).

Sources are automatically hidden after 4 weeks of inactivity, but you can also manually hide sources. While hidden sources are removed from autocomplete, they can still be used in a query when data values are present.

[Wavefront Query Language](query_language_reference) supports filtering by sources and source tags.

To view and manage sources, select **Browse > Sources**.
 
You must have [Source Tag Management permission](permissions) to manage sources. If you do not have permission, UI menu selections and buttons required to perform the tasks are not visible.

## Adding and Editing Source Descriptions

Source descriptions are a great way to assign additional details to a specific source, such as the role, availability zone, or running applications. A source description can be entered by clicking the Add a Description link in the Description column of the list of sources. To edit a description, click the description and a text field displays where you can modify the description. Once the desired source description is applied, press enter to save it. The entered source description can be viewed by all users from the Sources page. You can remove a source description by following the same process.

## Managing Source Tags

See [Tags Overview](tags_overview).

## Moving Sources Between Active and Hidden States

With more and more companies using dynamic services such as AWS, it's typical to have sources constantly being spun up and shut down. When applying source filters to the Metrics Browser or a ts() expression, this can lead to several sources being included in the autocomplete dropdown even when they are no longer reporting data. Those sources will be automatically removed from the autocomplete dropdown after 4 weeks of inactivity, but you can also manually remove them in the UI or API by moving them from an active to hidden state. While hidden sources are removed from the autocomplete dropdown, those sources can still be used in a ts() query when data values are present.
 
To view hidden sources, click the ACTIVE-HIDDEN toggle at the top right of the Sources page: ![Active source](images/active.png#inline), ![Hidden source](images/hidden.png#inline)
 
When a source is hidden, a hidden tag is added and the source is filtered out of the active sources list.
 
To hide sources, select the source(s) to be hidden. Click the **Hide** button located on the tag bar above the Source list.
 
To unhide sources, view the hidden sources list and select the source(s) to be unhidden. Click the Unhide button to return the source to the active list.
 
To hide and unhide a single source, use the ![action_menu.png](images/action_menu.png#inline) **> \[Hide\|Unhide\]** menu at the far right.

{% include links.html %}
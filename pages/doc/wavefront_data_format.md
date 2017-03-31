---
title: Wavefront Data Format
keywords: proxies
tags: [proxies]
sidebar: doc_sidebar
permalink: wavefront_data_format.html
summary: This topic describes the Wavefront data format.
---

The Wavefront proxy supports several data formats. This article documents the native Wavefront data format.

## Wavefront Data Format Syntax

`<metricName> <metricValue> [<timestamp>] source=<source> [pointTags]`

The lines are terminated with the newline character (\\n or ASCII hex 0A), and fields are space separated.

## Wavefront Data Format Fields

<table>
<colgroup>
<col width="15%" />
<col width="10%" />
<col width="15%" />
<col width="55%" />
</colgroup>
<thead>
<tr>
<th>Field</th>
<th>Required</th>
<th>Description</th>
<th>Format</th>
</tr>
</thead>
<tbody>
<tr>
<td>metricName</td>
<td>Yes</td>
<td>The name of the metric.</td>
<td>Valid characters are: a-z, A-Z, 0-9, hyphen (&quot;-&quot;), underscore (&quot;_&quot;), dot (&quot;.&quot;). Forward slash (&quot;/&quot;) and comma (&quot;,&quot;) are allowed if metricName is enclosed in double quotes.
<ul>
<li markdown="span">Data points with invalid characters in the metricName are rejected and logged by the Wavefront proxy. For information on how to configure the proxy to rewrite invalid metric names, see [​Configuring Wavefront Proxy Preprocessor Rules](proxies_preprocessor_rules).</li>
<li>Metric searches are case-sensitive; i.e., ts(&quot;my.metric&quot;) will not find a metric &quot;my.Metric&quot;.</li>
</ul>
Metric naming hierarchy recommendations:
<ul>
<li>Partition the top level of the metric hierarchy by including at least one dot.</li>
<li>Organize metric names in a meaningful hierarchy from <em>most general to most specific</em> (i.e. system.cpu0.loadavg.1m <em>instead of</em> 1m.loadavg.cpu0.system).</li>
</ul></td>
</tr>
<tr>
<td>metricValue</td>
<td>Yes</td>
<td>The value of the metric.</td>
<td><ul>
<li>A number that can be parsed into a double-precision floating point number or a long integer. Can be positive, negative, 0, Infinity, NaN and -Infinity.</li>
<li>In charts, the Wavefront UI represents values using SI and IEC/Binary units. See [Units in Chart Axes and Legends]().</li>
</ul></td>
</tr>
<tr>
<td>timestamp</td>
<td>No</td>
<td>The timestamp of the metric.</td>
<td><ul>
<li>A number reflecting the epoch seconds of the metric (e.g. 1382754475).</li>
<li>When this field is omitted, the timestamp is set to the current time at the Wavefront proxy when the metric arrives.</li>
</ul></td>
</tr>
<tr>
<td>source</td>
<td>Yes</td>
<td>The name of an application, host, container, instance, or any other unique entity sending the metric to Wavefront.</td>
<td><ul>
<li>Valid characters are: a-z, A-Z, 0-9, hyphen (&quot;-&quot;), underscore (&quot;_&quot;), dot (&quot;.&quot;)</li>
<li>The length of the source field should be less than 1024 characters.</li>
<li>Prior to Wavefront proxy 2.2, this field was named <strong>host</strong>. <strong>host</strong> is still supported.</li>
</ul></td>
</tr>
<tr>
<td>pointTags</td>
<td>No</td>
<td>Custom metadata associated with the metric.</td>
<td>An arbitrary number of key-value pairs separated by spaces: &lt;k1&gt;=&quot;&lt;v1&gt;&quot; ... &lt;kn&gt;=&quot;&lt;vn&gt;&quot;.
Point tags must satisfy the following constraints:
<dl>
<dt>Key</dt><dd>Valid characters are: a-z,A-Z, 0-9, hyphen (&quot;-&quot;), underscore (&quot;_&quot;), dot (&quot;.&quot;)</dd>
<dt>Value</dt><dd>We recommend enclosing tag values with double quotes (&quot; &quot;). If you surround the value with quotes any character is allowed, including spaces. To include a double quote, escape it with a backslash. The backslash cannot be the last character in the tag value.</dd>
</dl>
The maximum allowed length for a combination of a point tag key and value is 254 characters (255 including the &quot;=&quot; separating key and value). If the length is longer the point is rejected and logged.
The expected number of possible values (cardinality) for a given key should be <strong>&lt; 1000</strong> over the lifetime of that key. While Wavefront does not enforce a hard limit on the number of distinct values, using point tags to store high-cardinality data such as timestamps, login emails, or web session IDs will eventually cause performance issues when querying your data.</td>
</tr>
</tbody>
</table>

### Valid Metrics

-   `request.count 1001 source=test.wavefront.com`
-   `system.cpu.loadavg.1m 0.03 1382754475 source=test1.wavefront.com`
-   `marketing.adsense.impressions 24056 source=campaign1`
-   `new-york.power.usage 42422 source=localhost datacenter="dc1"`

### Invalid Metrics

- `system.cpu.load\# 0.03 source=test.wavefront.com`

  -   **Reason:** Metric name has an invalid character ('\#')

- `system.cpu.loadavg source=test.wavefront.com`

  -   **Reason:** No metric value

- `cpu0.loadavg.1m 0.03`

  -   **Reason:** No **source** field

{% include links.html %}
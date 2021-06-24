# Prometheus data source

> References:
>
> https://grafana.com/docs/grafana/latest/datasources/prometheus/

### Query editor in dashboards

| Name                | Description                                                  |
| ------------------- | ------------------------------------------------------------ |
| `Legend format`     | Controls the name of the time series, using name or pattern. For example `{{hostname}}` is replaced with the label value for the label `hostname`. |
| `Min step`          | An additional lower limit for the [`step` parameter of Prometheus range queries](https://prometheus.io/docs/prometheus/latest/querying/api/#range-queries) and for the `$__interval` and `$__rate_interval` variables. The limit is absolute, it cannot modified by the *Resolution* setting. |
| `Resolution`        | `1/1` sets both the `$__interval` variable and the [`step` parameter of Prometheus range queries](https://prometheus.io/docs/prometheus/latest/querying/api/#range-queries) such that each pixel corresponds to one data point. For better performance, lower resolutions can be picked. `1/2` only retrieves a data point for every other pixel, and `1/10` retrieves one data point per 10 pixels. Note that both *Min time interval* and *Min step* limit the final value of `$__interval` and `step`. |
| `Instant`           | Perform an “instant” query, to return only the latest value that  Prometheus has scraped for the requested time series. Instant queries  return results much faster than normal range queries. Use them to look  up label sets. |
| `Min time interval` | This value multiplied by the denominator from the *Resolution* setting sets a lower limit to both the `$__interval` variable and the [`step` parameter of Prometheus range queries](https://prometheus.io/docs/prometheus/latest/querying/api/#range-queries). Defaults to *Scrape interval* as set in the data source options. |
| `Exemplars`         | Run and show exemplars in the graph.                         |

#### Instant queries in dashboards

The Prometheus data source allows you to run “instant” queries, which query only the latest value. You can visualize the results in a table panel to see all available labels of a timeseries.

Instant query results are made up only of one data point per series but can be shown in the graph panel with the help of [series overrides](https://grafana.com/docs/grafana/latest/panels/visualizations/graph-panel/#series-overrides). To show them in the graph as a latest value point, add a series override and select `Points > true`. To show a horizontal line across the whole graph, add a series override and select `Transform > constant`.

### Query editor in Explore

| Name         | Description                                                  |
| ------------ | ------------------------------------------------------------ |
| `Query type` | `Range`, `Instant`, or `Both`. When running **Range query**, the result of the query is displayed in graph and table. Instant query  returns only the latest value that Prometheus has scraped for the  requested time series and it is displayed in the table. When **Both** is selected, both instant query and range query is run. Result of range query is displayed in graph and the result of instant query is  displayed in the table. |

#### Limitations

The metrics browser has a hard limit of 10,000 labels (keys) and  50,000 label values (including metric names). If your Prometheus  instance returns more results, the browser will continue functioning.  However, the result sets will be cut off above those maximum limits.

### Query variable

Variable of the type *Query* allows you to query Prometheus for a list of metrics, labels or label values. The Prometheus data source plugin provides the following functions you can use in the `Query` input field.

| Name                          | Description                                                  |
| ----------------------------- | ------------------------------------------------------------ |
| `label_names()`               | Returns a list of label names.                               |
| `label_values(label)`         | Returns a list of label values for the `label` in every metric. |
| `label_values(metric, label)` | Returns a list of label values for the `label` in the specified metric. |
| `metrics(metric)`             | Returns a list of metrics matching the specified `metric` regex. |
| `query_result(query)`         | Returns a list of Prometheus query result for the `query`.   |

### Using interval and range variables

You can use some global built-in variables in query variables; `$__interval`, `$__interval_ms`, `$__range`, `$__range_s` and `$__range_ms`. These can be convenient to use in conjunction with the `query_result` function when you need to filter variable queries since `label_values` function doesn’t support queries.

The `$__rate_interval` variable is meant to be used in the rate function.

There are two syntaxes:
- `$<varname> Example: rate(http_requests_total{job=~"$job"}[5m])`
- `[[varname]] Example: rate(http_requests_total{job=~"[[job]]"}[5m])`

When the *Multi-value* or *Include all value* options are enabled, Grafana converts the labels from plain text to a regex compatible string. Which means you have to use `=~` instead of `=`.

### Ad hoc filters variable

Prometheus supports the special [ad hoc filters](https://grafana.com/docs/grafana/latest/variables/variable-types/add-ad-hoc-filters/) variable type. It allows you to specify any number of label/value filters on the fly. These filters are automatically applied to all your Prometheus queries.

### Configuring exemplars

Grafana 7.4 and later versions have the capability to show exemplars data alongside a metric both in Explore and Dashboards. Exemplars are a way to associate higher cardinality metadata from a specific event with traditional timeseries data.

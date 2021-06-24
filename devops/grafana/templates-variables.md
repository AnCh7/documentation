# Templates and variables

> References:
>
> https://grafana.com/docs/grafana/latest/variables/



A variable is a placeholder for a value. You can use variables in metric queries and in panel titles. So when you change the value, using the dropdown at the top of the dashboard, your panel’s metric queries will change to reflect the new value.

A *template* is any query that contains a variable.

```
wmi_system_threads{instance=~"$server"}
```

To see variable settings, navigate to **Dashboard Settings > Variables**. Click a variable in the list to see its settings.

Variables can be used in titles, descriptions, text panels, and queries. Queries with text that starts with `$` are templates.

Panel titles and metric queries can refer to variables using different syntaxes:

- `$varname` This syntax is easy to read, but it does not allow you to use a variable in the middle of a word. `apps.frontend.$server.requests.count`
- `${var_name}` Use this syntax when you want to interpolate a variable in the middle of an expression.
- `${var_name:<format>}` This format gives you more control over how Grafana interpolates values. Refer to [Advanced variable format options](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/) for more detail on all the formatting types.
- `[[varname]]` Do not use. Deprecated old syntax, will be removed in a future release.

# Variables types

Grafana uses several types of variables.

| Variable type     | Description                                                  |
| ----------------- | ------------------------------------------------------------ |
| Query             | Query-generated list of values such as metric names, server names, sensor IDs, data centers, and so on. [Add a query variable](https://grafana.com/docs/grafana/latest/variables/variable-types/add-query-variable/). |
| Custom            | Define the variable options manually using a comma-separated list. [Add a custom variable](https://grafana.com/docs/grafana/latest/variables/variable-types/add-custom-variable/). |
| Text box          | Display a free text input field with an optional default value. [Add a text box variable](https://grafana.com/docs/grafana/latest/variables/variable-types/add-text-box-variable/). |
| Constant          | Define a hidden constant. [Add a constant variable](https://grafana.com/docs/grafana/latest/variables/variable-types/add-constant-variable/). |
| Data source       | Quickly change the data source for an entire dashboard. [Add a data source variable](https://grafana.com/docs/grafana/latest/variables/variable-types/add-data-source-variable/). |
| Interval          | Interval variables represent time spans. [Add an interval variable](https://grafana.com/docs/grafana/latest/variables/variable-types/add-interval-variable/). |
| Ad hoc filters    | Key/value filters that are automatically added to all  metric queries for a data source (InfluxDB, Prometheus, and  Elasticsearch only). [Add ad hoc filters](https://grafana.com/docs/grafana/latest/variables/variable-types/add-ad-hoc-filters/). |
| Global variables  | Built-in variables that can be used in expressions in the query editor. Refer to [Global variables](https://grafana.com/docs/grafana/latest/variables/variable-types/global-variables/). |
| Chained variables | Variable queries can contain other variables. Refer to [Chained variables](https://grafana.com/docs/grafana/latest/variables/variable-types/chained-variables/). |

# Global variables

```
$__dashboard
$__from and $__to
$__interval
$__interval_ms
$__name
$__org
$__user
$__range
$timeFilter or $__timeFilter
```

# Custom all value

Enter regex, globs, or lucene syntax in the **Custom all value** field to define the value of the `All` option.

If leave it blank, then the Grafana concatenates (adds together) all the values in the query. Something like `value1,value2,value3`.

Graphite uses glob expressions: . A variable with multiple values would, in this case, be interpolated as `{host1,host2,host3}` if the current variable value was *host1*, *host2*, and *host3*.

InfluxDB and Prometheus use regex expressions, so the same variable would be interpolated as `(host1|host2|host3)`. Every value would also be regex escaped. If not, a value with a regex control character would break the regex expression.

Elasticsearch uses lucene query syntax, so the same variable would be formatted as `("host1" OR "host2" OR "host3")`. In this case, every value must be escaped so that the value only contains lucene control words and quotation marks.

# Advanced variable format options

General syntax: `${var_name:option}`

[CSV](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#csv)
[Distributed - OpenTSDB](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#distributed---opentsdb)
[Doublequote](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#doublequote)
[Glob - Graphite](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#glob---graphite)
[JSON](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#json)
[Lucene - Elasticsearch](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#lucene---elasticsearch)
[Percentencode](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#percentencode)
[Pipe](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#pipe)
[Raw](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#raw)
[Regex](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#regex)
[Singlequote](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#singlequote)
[Sqlstring](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#sqlstring)
[Text](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#text)
[Query parameters](https://grafana.com/docs/grafana/latest/variables/advanced-variable-format-options/#query-parameters)

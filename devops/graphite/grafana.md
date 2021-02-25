### Using Graphite in Grafana

> References:
>
> https://grafana.com/docs/grafana/latest/datasources/graphite/
>
> https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/



alias() function to specify a custom series name.

---

aliasByNode(2) to alias by a specific part of your metric path.

---

groupByNode(2, 'sum') is useful if you have 2 wildcards in your metric path and want to sumSeries and group by.

---

#### Max data points

- Every graphite request is issued with a maxDataPoints parameter.
- Graphite uses this parameter to consolidate the real number of values down to this number.
- If there are more real values, then by default they will be consolidated using averages.
- This could hide real peaks and max values in your series.
- You can change how point consolidation is made using the consolidateBy graphite function.
- Point consolidation will effect series legend values (min,max,total,current).
- if you override maxDataPoint and set a high value performance can be severely effected.

---

If you want consistent ordering, use sortByName. 

---

You can reference queries by the row “letter” that they’re on. If you add a second query to a graph, you can reference the first query simply by typing in #A.

---

Use `alias` functions to change metric names on Grafana tables or graphs For example `aliasByNode()` or `aliasSub()`.

---

All Graphite metrics are consolidated so that Graphite doesn’t return  more data points than there are pixels in the graph. By default, this consolidation is done using `avg` function. You can control how Graphite consolidates metrics by adding the Graphite consolidateBy function.

---

To select data, you use the `seriesByTag` function, which takes tag expressions (`=`, `!=`, `=~`, `!=~`) to filter timeseries.

---

Variables are shown as drop-down select boxes at the top of the dashboard. These dropdowns make it easy to change the data being displayed in your dashboard.

---

A query like `prod.servers.*` will fill the variable with all possible values that exist in the wildcard position.

A query `apps.$app.servers.*` uses the variable `$app` in its query definition.

Using `__searchFilter` in the query field will filter the query result based on what the user types in the dropdown select box. When nothing has been entered by the user the default value for `__searchFilter` is `*` and `` when used as part of a regular expression.

---

You can use a variable in a metric node path or as a parameter to a function: `my.server${serverNumber}.count`. For tags use: `server=~${servers:regex}`.




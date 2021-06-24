# Querying Prometheus

> References:
>
> https://prometheus.io/docs/prometheus/latest/querying/basics/

## Expression language data types

In Prometheus's expression language, an expression or sub-expression can evaluate to one of four types:

- **Instant vector** - a set of time series containing a single sample for each time series, all sharing the same timestamp
- **Range vector** - a set of time series containing a range of data points over time for each time series
- **Scalar** - a simple numeric floating point value
- **String** - a simple string value; currently unused

## Literals

PromQL follows the same [escaping rules as Go](https://golang.org/ref/spec#String_literals). In single or double quotes a backslash begins an escape sequence, which may be followed by `a`, `b`, `f`, `n`, `r`, `t`, `v` or `\`. Specific characters can be provided using octal (`\nnn`) or hexadecimal (`\xnn`, `\unnnn` and `\Unnnnnnnn`).

### Instant vector selectors

Instant vector selectors allow the selection of a set of time series and a single sample value for each at a given timestamp (instant): in the simplest form, only a metric name is specified. This results in an instant vector containing elements for all time series that have this metric name.

It is possible to filter these time series further by appending a comma separated list of label matchers in curly braces (`{}`).

```
http_requests_total{job="prometheus",group="canary"}
```

It is also possible to negatively match a label value, or to match label values against regular expressions. The following label matching operators exist:

- `=`: Select labels that are exactly equal to the provided string.
- `!=`: Select labels that are not equal to the provided string.
- `=~`: Select labels that regex-match the provided string.
- `!~`: Select labels that do not regex-match the provided string.

Vector selectors must either specify a name or at least one label matcher that does not match the empty string. 

```
{job=~".+"}
{job=~".*",method="get"}
```

Label matchers can also be applied to metric names by matching against the internal `__name__` label. For example, the expression `http_requests_total` is equivalent to `{__name__="http_requests_total"}`

### Range Vector Selectors

Range vector literals work like instant vector literals, except that they select a range of samples back from the current instant. Syntactically, a [time duration](https://prometheus.io/docs/prometheus/latest/querying/basics/#time-durations) is appended in square brackets (`[]`) at the end of a vector selector to specify how far back in time values should be fetched for each resulting range vector element.

```
http_requests_total{job="prometheus"}[5m]
```

Time durations are specified as a number, followed immediately by one of the following units:

- `ms` - milliseconds
- `s` - seconds
- `m` - minutes
- `h` - hours
- `d` - days - assuming a day has always 24h
- `w` - weeks - assuming a week has always 7d
- `y` - years - assuming a year has always 365d

The `offset` modifier allows changing the time offset for individual instant and range vectors in a query.

The `@` modifier allows changing the evaluation time for individual instant and range vectors in a query. The time supplied to the `@` modifier is a unix timestamp and described with a float literal. 

## Subquery

Subquery allows you to run an instant query for a given range and resolution. The result of a subquery is a range vector.

Syntax: `<instant_query> '[' <range> ':' [<resolution>] ']' [ @ <float_literal> ] [ offset <duration> ]`

- `<resolution>` is optional. Default is the global evaluation interval.

### Staleness

If a target scrape or rule evaluation no longer returns a sample for a time series that was previously present, that time series will be marked as stale. If a target is removed, its previously returned time series will be marked as stale soon afterwards.

# Operators

The following binary arithmetic operators exist in Prometheus:

- `+` (addition)
- `-` (subtraction)
- `*` (multiplication)
- `/` (division)
- `%` (modulo)
- `^` (power/exponentiation)

Binary arithmetic operators are defined between scalar/scalar, vector/scalar, and vector/vector value pairs.

The following binary comparison operators exist in Prometheus:

- `==` (equal)
- `!=` (not-equal)
- `>` (greater-than)
- `<` (less-than)
- `>=` (greater-or-equal)
- `<=` (less-or-equal)

Comparison operators are defined between scalar/scalar, vector/scalar, and vector/vector value pairs. By default they filter. Their behavior can be modified by providing `bool` after the operator, which will return `0` or `1` for the value rather than filtering.

These logical/set binary operators are only defined between instant vectors:

- `and` (intersection)
- `or` (union)
- `unless` (complement)

Prometheus supports the following built-in aggregation operators that can be used to aggregate the elements of a single instant vector, resulting in a new vector of fewer elements with aggregated values:

- `sum` (calculate sum over dimensions)
- `min` (select minimum over dimensions)
- `max` (select maximum over dimensions)
- `avg` (calculate the average over dimensions)
- `group` (all values in the resulting vector are 1)
- `stddev` (calculate population standard deviation over dimensions)
- `stdvar` (calculate population standard variance over dimensions)
- `count` (count number of elements in the vector)
- `count_values` (count number of elements with the same value)
- `bottomk` (smallest k elements by sample value)
- `topk` (largest k elements by sample value)
- `quantile` (calculate φ-quantile (0 ≤ φ ≤ 1) over dimensions)

These operators can either be used to aggregate over **all** label dimensions or preserve distinct dimensions by including a `without` or `by` clause.

## Vector matching

**One-to-one** finds a unique pair of entries from each side of the operation. In the default case, that is an operation following the format `vector1 <operator> vector2`. Two entries match if they have the exact same set of labels and corresponding values. The `ignoring` keyword allows ignoring certain labels when matching, while the `on` keyword allows reducing the set of considered labels to a provided list.

**Many-to-one** and **one-to-many** matchings refer to the case where each vector element on the "one"-side can match with multiple elements on the "many"-side. This has to be explicitly requested using the `group_left` or `group_right` modifier, where left/right determines which vector has the higher cardinality.

# Functions

- [ `abs()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#abs)
- [ `absent()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#absent)
- [ `absent_over_time()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#absent_over_time)
- [ `ceil()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#ceil)
- [ `changes()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#changes)
- [ `clamp()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#clamp)
- [ `clamp_max()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#clamp_max)
- [ `clamp_min()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#clamp_min)
- [ `day_of_month()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#day_of_month)
- [ `day_of_week()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#day_of_week)
- [ `days_in_month()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#days_in_month)
- [ `delta()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#delta)
- [ `deriv()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#deriv)
- [ `exp()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#exp)
- [ `floor()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#floor)
- [ `histogram_quantile()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#histogram_quantile)
- [ `holt_winters()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#holt_winters)
- [ `hour()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#hour)
- [ `idelta()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#idelta)
- [ `increase()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#increase)
- [ `irate()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#irate)
- [ `label_join()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#label_join)
- [ `label_replace()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#label_replace)
- [ `ln()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#ln)
- [ `log2()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#log2)
- [ `log10()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#log10)
- [ `minute()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#minute)
- [ `month()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#month)
- [ `predict_linear()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#predict_linear)
- [ `rate()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#rate)
- [ `resets()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#resets)
- [ `round()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#round)
- [ `scalar()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#scalar)
- [ `sgn()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#sgn)
- [ `sort()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#sort)
- [ `sort_desc()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#sort_desc)
- [ `sqrt()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#sqrt)
- [ `time()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#time)
- [ `timestamp()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#timestamp)
- [ `vector()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#vector)
- [ `year()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#year)
- [ `_over_time()` ](https://prometheus.io/docs/prometheus/latest/querying/functions/#aggregation_over_time)


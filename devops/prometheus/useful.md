> References:
> https://timber.io/blog/promql-for-humans
>
> https://www.robustperception.io/rate-then-sum-never-sum-then-rate
>
> https://github.com/infinityworks/prometheus-example-queries/blob/master/README.md



Filter by label

```
http_requests_total{job="prometheus", code="200"}
```

Check a substring using regex matching
```
http_requests_total{status_code=~"2.*"}
```

Increase of `http_requests_total` averaged over the last 5 minutes
```
rate(http_requests_total[5m])
```

Irate - looks at the 2 most recent samples (up to 5 minutes in the past), rather than averaging like `rate`
```
irate(http_requests_total[5m])
```

HTTP Requests in the last hour. This is equal to the `rate * # of seconds`
```
increase(http_requests_total[1h])
```

By Status Code
```
sum(rate(http_requests_total[5m]))
```

Sum by Status Code
```
sum by (status_code) (rate(http_requests_total[5m]))
```

API 5xxs are 10% of HTTP Requests
```
rate(http_requests_total{status_code=~"5.*"}[5m]) > .1 * rate(http_requests_total[5m])
```

CPU Usage by Instance
```
100 * (1 - avg by(instance)(irate(node_cpu{mode='idle'}[5m])))
```

Memory Usage
```
node_memory_Active / on (instance) node_memory_MemTotal
```

Disk Space
```
node_filesystem_avail{fstype!~"tmpfs|fuse.lxcfs|squashfs"} / node_filesystem_size{fstype!~"tmpfs|fuse.lxcfs|squashfs"}
```

HTTP Error Rates as a % of Traffic
```
rate(http_requests_total{status_code=~"5.*"}[5m]) / rate(http_requests_total[5m])
```

Alerts Firing in the last 24 hours
```
sum(sort_desc(sum_over_time(ALERTS{alertstate="firing"}[24h]))) by (alertname)
```



---



Aggregation is core functionality of Prometheus, and it's most commonly applied to counters. Counters only go up and reset. This has implications for what order you apply operations in.

The individual rates would be:

```
rate(http_requests_total{job="node"}[5m])
```

Now to aggregate those, you'd do:

```
sum by (job)(rate(http_requests_total{job="node"}[5m]))
```

A common mistake is to try to take the sum and then the rate. The only mathematical operations you can safely directly apply to a counter's values are `rate`, `irate`, `increase`, and `resets`.


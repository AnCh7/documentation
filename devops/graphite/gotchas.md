> References:
>
> https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/
>
> https://kevinmccarthy.org/2013/07/18/10-things-i-learned-deploying-graphite/



It is written in Python and is actually made up of 3 main parts:

1. Graphite-web: A Django project that provides a GUI for graphite.
2. Carbon: Carbon is a daemon that runs constantly in the background.  Its main job is to take in statistics from the network and write them to disk. It also does aggregation. 
3. Whisper: Whisper is a time-series database file format. It doesn't  actually run as a server or anything, it's just a standard format for  storing time-series data. These databases are a fixed size, and once the data gets old it falls off the other end.

---

Make sure that your StatsD flush interval is at least as long as your Graphite interval.

---

In StatsD, counters keep track of events that happen, as opposed to gauges, which keep track of a value that fluctuates. **Yourstat.count** keeps the total number of times that this event happened, and **Yourstat.rate** keeps the rate that this thing happens *per second*.

---

Retention is configured in a file called `storage-schemas.conf`:

```
[nginx_requests]
pattern = nginx\.requests\.count$  
retentions = 1s:7d,1m:30d,15m:5y  
```

It will keep 1 second resolution for 7 days, 1 minute resolution for 30  days, after that, and 15 minute resolution for 5 years after that.

Data files that store this information have a fixed size after creation.

---

Aggregation - you have to drop your resolution when you transition from higher  resolution to lower resolution. This means throwing away data. This is configurable by changing a setting called `xFilesFactor`.

For count statistics, we actually want to *sum* the number of times that something happened. You can set this with the `aggregationMethod` in `storage-aggregation.conf`

---

`hitcount()` is used for *rate* variables to turn them into *counts*, as well as changing the resolution of a graph.

`summarize()` just changes the resolution of the graph.

---

For network sniffing:

````
ngrep -d any -W byline port 2003 # carbon traffic 
ngrep -d any -W byline port 8125 # statsd traffic
````

Getting the json output from Graphite (just append `&format=json`) can be very helpful as well. Many dashboards, including [Grafana](http://www.grafana.org) already do this, so you can use the browser network inspector to  analyze requests. 

For the whisper files, Graphite comes with various  useful utilities such as whisper-info, whisper-dump, whisper-fetch, etc.

---

[**OMG! Did all our traffic just drop?**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#traffic.drop) Graphite will return data up until the current time, according to your data schema.

> **Example:** if your data is stored at 10s resolution, then at 10:02:35, it will show data up until 10:02:30. Once the clock hits 10:02:40, it will also include that point (10:02:40) in its response.

You can work around this with "null as null" in Grafana, transformNull() or keepLastValue() in Graphite, or plotting until a few seconds ago  instead of now.

---

2) [**Null handling in math functions.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#null.math.func) When you request something like `sumSeries(diskspace.server_*.bytes_free)` Graphite returns the sum of all bytes_free’s for all your servers, at  each point in time. If all of the servers have a null for a given  timestamp, the result will be null as well for that time. However, if  only some - but not all - of the terms are null, they are counted as 0.

> **Example:** The sum for a given point in time, 100 + 150 + null + null = 250.

Graphite could be changed to strictly follow nulls in an equation, as nulls.

But a low number of nulls in a large sum is usually not a big deal and  having a  result with the nulls counted as 0 can be more useful than no  result at all. Especially when averaging, since those points can  typically be safely excluded  without impact on the output. Graphite has the xFilesFactor option in storage-aggregation.conf, which lets you  configure the minimum fraction of non-null values, which it uses during  storage aggregation (rollups) to determine whether the input is  sufficiently known for output to be known, otherwise the output value  will be null.

---

- **storage consolidation aka aggregation, aka rollups**: data aggregated to optimize the cost of historical archives and make it faster to return large timeframes of data. Driven by  `storage-aggregation.conf`
- **runtime consolidation**: when you want to render a  graph but there are more datapoints than pixels for the graph, or more  than the maxDataPoints setting. Graphite will reduce the data in the  http response. Averages by default, but can be overridden through ` consolidateBy()`
- **Grafana consolidation**: Grafana can visualize stats such as min/max/avg which it computes on the data returned by your timeseries system.
- **statsd consolidation**: statsd is also commonly used  to consolidate usually very high rates of irregular incoming data in  properly spaced timeframes, before it goes into Graphite.
- Unlike all above mechanisms which work per series, the [sumSeries()](http://graphite.readthedocs.org/en/1.0/functions.html#graphite.render.functions.sumSeries), [saverageSeries()](http://graphite.readthedocs.org/en/1.0/functions.html#graphite.render.functions.averageSeries), [groupByNode()](http://graphite.readthedocs.org/en/1.0/functions.html#graphite.render.functions.groupByNode) Graphite functions work across series. They merge multiple series into a single or fewer series by aggregating points from different series at each point in time.
- And then there's [carbon-aggregator](http://graphite.readthedocs.org/en/latest/carbon-daemons.html#carbon-aggregator-py) and [carbon-relay-ng](https://github.com/graphite-ng/carbon-relay-ng) which operate on your metrics stream and can aggregate on per-series  level, as well as across series, as well as a combination thereof.

---

3) [**Null handling during runtime consolidation**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#null.runtime) when Graphite performs runtime consolidation it simply ignores null values. Imagine a series that measures throughput with  points 10, 12, 11, null, null, 10, 11, 12, 10. Let’s say it needs to  aggregate every 3 points with sum. this would return 33, 10, 33. This  will visually look like a drop in throughput, even though there probably was none.

---

4) [**No consolidation or aggregation for incoming data.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#consolidation.aggregation) If you submit multiple values into Graphite for the same interval, the  last one overwrites any previous values, no consolidation/aggregation  happens in this scenario.

Never send multiple values for the same interval. However, carbon-aggregator or carbon-relay-ng may be of help.

---

5) [**Limited storage aggregation options.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#limited.aggregation) In storage-aggregation.conf you configure which function to use for historical rollups.

---

6) [**Runtime consolidation is detached from storage aggregation.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#runtime.consolidation) The function chosen in storage-aggregation.conf is only used for rollups.

If Graphite performs any runtime consolidation it will always use average unless told otherwise through consolidateBy. 

---

7) [**Grafana consolidation is detached from storage aggregation and runtime consolidation.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#grafana.consolidation) Grafana can provide min/max/avg/… values from  the received data. For now, it just computes these from the received  data, which may already have been consolidated (twice: in storage and in runtime) using different functions, so these results may not be always  representative.

---

8) [**Aggregating percentiles.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#aggregating.percentiles) If you have  percentiles, such as those collected by statsd, (e.g. 95th percentile  response time for each server) there is in theory no proper way to  aggregate those numbers.

---

9) [**Deriving and integration in Graphite.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#deriving.integration)

- Graphite's [derivative](http://graphite.readthedocs.org/en/0.9.15/functions.html#graphite.render.functions.derivative) is not a derivative. It just returns the value deltas. Similar for [nonNegativeDerivative()](http://graphite.readthedocs.org/en/0.9.15/functions.html#graphite.render.functions.nonNegativeDerivative).  If you want an actual derivative, use the somewhat awkwardly named [perSecond()](http://graphite.readthedocs.org/en/0.9.15/functions.html#graphite.render.functions.perSecond) function.
- Graphite's integral is not an integral either. It just adds up the value differences.

---

10) [**Graphite quantization.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#graphite.quantization) Any data  point submitted gets the timestamp rounded down.

**Example**: You record points every 10 seconds but submit a point with timestamp at 10:02:59, in Graphite this will be stored at  10:02:50. 

---

11) [**statsd flush offset depends on when statsd was started.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#statsd.flush.offset) Statsd lets you configure a flushInterval, i.e. how often it should compute the output stats and submit them to your backend. However, the exact timing is pretty arbitrary and depends on when statsd is  started.

---

12) [**Improperly time-attributed metrics.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#time-attributed.metrics) You don’t tell statsd the timestamps of when things happened.  Statsd applies its own timestamp when it flushes the data. This could result in a metric being generated in a certain interval only arriving in statsd after the next interval has started.

---

13) [**The relation between timestamps and the intervals they describe.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#timestamps)

**Example**: With points every minute, and a spike at  10:17:00, does it mean the spike happened in the timeframe between 10:16 and 10:17, or between 10:17 and 10:18?

statsd postmarks, and many tools seem to do this, but some, including Graphite, premark.

---

14) [**The statsd timing type is not only for timings.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#statsd.timing) anything you want to compute summary  statistics (min, max, mean, percentiles, etc) for (for example message  or packet sizes) can be submitted as a timing metric. 

---

15) [**The choice of metric keys depends on how you deployed statsd.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#metric.keys) It's common to deploy a statsd server per machine or per cluster. However, if multiple statsd servers receive the same metric, they will  do their own independent computations and emit the same output metric,  overwriting each other. So if you run a statsd server per host, you should include the host in the metrics you’re sending into statsd.

---

16) [**statsd is “fire and forget”, “non-blocking” & UDP sends “have no overhead”.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#non-blocking.udp.sends)

UDP sends do have an overhead that can slow your application down. In specific, it depends on the following factors:

- your network stack.  UDP sends are not asynchronous. Udp write calls from userspace will block until the kernel has moved the data  through the networking stack, firewall rules, through the network driver, onto the network.
- other code running, and its performance.
- the performance of client code itself can wildly vary as well.
- sending to non-listening destinations may make your application slower. This is because destination hosts receiving UDP traffic for a closed  socket, will return ICMP rejects to the sender, which the sender then  has to process.

---

17) [**statsd sampling. **](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#statsd.sampling) If a certain statsd invocation causes too much statsd network traffic or  overloads the server, sample down the messages. 

---

18) [**As long as my network doesn’t saturate, statsd graphs should be accurate.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#saturate) statsd are not guaranteed to be accurate, because it uses UDP, so is prone to data loss and inaccurate graphs.

Tune the size of the incoming UDP buffer with the net.core.rmem_max and net.core.rmem_default sysctl's.

The kernel increments a counter every time it drops a UDP packet. You can see UDP packet drops with `netstat -s –udp` on the command  line.

---

19) [**Incrementing/decrementing gauges.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#gauges) Highly recommended not to use this particular feature, and always set gauge values explicitly.

---

20) [**You can't graph what you haven't seen**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#cant.graph.what.you.havent.seen). The metrics will only be created after the values have been sent. 

---

21) [**Don't let the data fool you: nulls and deleteIdleStats options.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#nulls.deleteIdleStats) statsd has the deleteIdleStats, deleteGauges and other options. By default they are disabled, meaning statsd will keep sending data for metrics it's no longer seeing.

To visualize it, make sure in Grafana to set the "null as null" option,  though "null as zero" can make sense for counters. You can also use the [transformNull()](http://graphite.readthedocs.org/en/0.9.15/functions.html#graphite.render.functions.transformNull) or [drawNullAsZero](http://graphite.readthedocs.org/en/0.9.15/render_api.html#drawnullaszero) options in Graphite for those  cases where you want to explicitly get 0's instead of nulls.

---

22) [**keepLastValue work...  almost always.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#keeplastvalue) Another Graphite function to change the semantics of nulls is [ keepLastValue](http://graphite.readthedocs.org/en/latest/functions.html#graphite.render.functions.keepLastValue), which causes null values to be represented by the last known value that precedes them.

---

23) [**statsd counters are not counters in the traditional sense.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#statsd.counters)

Statsd counters however, are typically either stored as the number of hits per flushInterval, or per second, or both. This is more vulnerable to loss of data.

---

24) [**What can I send as input?**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#input) Neither [Graphite](http://graphite.readthedocs.org/en/latest/feeding-carbon.html), nor [statsd](https://github.com/etsy/statsd/blob/master/docs/metric_types.md) does a great job specifying exactly what they allow as input.  Graphite timestamps are 32bit unix timestamp integers, while values for both Graphite and statsd can be integers or floats, up to float 64bit precision. For statsd, see the [statsd_spec](https://github.com/b/statsd_spec) project for more details.

You may want to use [ carbon-relay-ng](https://github.com/graphite-ng/carbon-relay-ng) which provides metric validation.

---

25) [**Hostnames and ip addresses in metric keys.**](https://grafana.com/blog/2016/03/03/25-graphite-grafana-and-statsd-gotchas/#hostnames.ip.addresses)

Hostnames, especially FQDN’s, and IP addresses (due to their dots), when included in metric keys, will be interpreted by Graphite as separate  pieces.


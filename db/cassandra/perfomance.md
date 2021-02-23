## Key performance metrics

https://www.datadoghq.com/blog/how-to-monitor-cassandra-performance-metrics/

### Throughput

| **Name** | **Description**           | **[Metric type](https://www.datadoghq.com/blog/monitoring-101-collecting-data/)** | **Availability**                |
| -------- | ------------------------- | ------------------------------------------------------------ | ------------------------------- |
| Reads    | Read requests per second  | Work: Throughput                                             | JConsole, JMX/Metrics reporters |
| Writes   | Write requests per second | Work: Throughput                                             | JConsole, JMX/Metrics reporters |

Monitoring the requests—both reads and writes—that Cassandra is  receiving at any given time gives you a high-level view of your  cluster’s activity levels. 

Consider alerting on sustained spikes (over historical baselines) or  sudden, unexpected drops (on a percentage basis over a short timeframe)  in throughput.

**Queries**:

   ```
TODO
   ```

### Latency

| **Name**           | **Description**                                              | **[Metric type](https://www.datadoghq.com/blog/monitoring-101-collecting-data/)** | **Availability**                          |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------- |
| Write latency      | Write response time, in microseconds                         | Work: Performance                                            | nodetool, JConsole, JMX/Metrics reporters |
| Read latency       | Read response time, in microseconds                          | Work: Performance                                            | nodetool, JConsole, JMX/Metrics reporters |
| Key cache hit rate | Fraction of read requests for which the key’s location on disk was found in the cache | Other                                                        | nodetool, JConsole, JMX/Metrics reporters |

Cassandra slices latency statistics into a number of different metrics,  but perhaps the simplest and highest-value metrics to monitor are the  current read and write latency. The Cassandra metric **write latency** measures the number of microseconds required to fulfill a write request, whereas **read latency** measures the same for read requests.

Cassandra uses a key cache to store the location of row keys in memory  so that rows can be accessed without having to first seek them on disk.  The **key cache hit rate** provides visibility into the  effectiveness of your key cache. If the key cache hit rate is  consistently high (above 0.85, or 85 percent), then the vast majority of read requests are being expedited through caching.

**Queries:**

```
SELECT mean("ReadLatency_FifteenMinuteRate") FROM "cassandra_Table" WHERE $timeFilter GROUP BY time($__interval) fill(null)
SELECT mean("WriteLatency_FifteenMinuteRate") FROM "cassandra_Table" WHERE $timeFilter GROUP BY time($__interval) fill(null)
SELECT mean("KeyCacheHitRate_Value") FROM "cassandra_Table" WHERE $timeFilter GROUP BY time($__interval) fill(null)
```

### Disk usage

| **Name**                   | **Description**                              | **[Metric type](https://www.datadoghq.com/blog/monitoring-101-collecting-data/)** | **Availability**                          |
| -------------------------- | -------------------------------------------- | ------------------------------------------------------------ | ----------------------------------------- |
| Load                       | Disk space used on a node, in bytes          | Resource: Utilization                                        | nodetool, JConsole, JMX/Metrics reporters |
| Total disk space used      | Disk space used by a column family, in bytes | Resource: Utilization                                        | nodetool, JConsole, JMX/Metrics reporters |
| Completed compaction tasks | Total compaction tasks completed             | Resource: Other                                              | JConsole, JMX/Metrics reporters           |
| Pending compaction tasks   | Total compaction tasks in queue              | Resource: Saturation                                         | nodetool, JConsole, JMX/Metrics reporters |

Cassandra exposes [several different disk metrics](https://wiki.apache.org/cassandra/Metrics), but the simplest ones to monitor are load or total disk space used.

Compaction activity can be tracked via metrics for **completed compaction tasks** and **pending compaction tasks**. Completed compactions will naturally trend upward with increased write  activity, but a growing queue of pending compaction tasks indicates that the Cassandra cluster is unable to keep pace with the workload, often  because of I/O limitations or resource contention that may require the  addition of new nodes.

**Queries:**

```
SELECT mean("used_percent") FROM "cassandra_disk" WHERE $timeFilter GROUP BY time($__interval) fill(null)
SELECT mean("PendingCompactions_Value") FROM "cassandra_Table" WHERE $timeFilter GROUP BY time($__interval) fill(null)
```

### Garbage collection

| **Name**                  | **Description**                                              | **[Metric type](https://www.datadoghq.com/blog/monitoring-101-collecting-data/)** | **Availability**                           |
| ------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------ |
| ParNew count              | Number of young-generation collections                       | Other                                                        | nodetool,* JConsole, JMX/Metrics reporters |
| ParNew time               | Elapsed time of young-generation collections, in milliseconds | Other                                                        | nodetool,* JConsole, JMX/Metrics reporters |
| ConcurrentMarkSweep count | Number of old-generation collections                         | Other                                                        | nodetool,* JConsole, JMX/Metrics reporters |
| ConcurrentMarkSweep time  | Elapsed time of old-generation collections, in milliseconds  | Other                                                        | nodetool,* JConsole, JMX/Metrics reporters |

**ParNew**, or young-generation, garbage collections  occur relatively often. ParNew is a stop-the-world garbage collection,  meaning that all application threads pause while garbage collection is  carried out, so any significant increase in ParNew latency will impact  Cassandra’s performance.

**ConcurrentMarkSweep**  (CMS) collections free up unused memory in the old generation of the  heap. CMS is a low-pause garbage collection, meaning that although it  does temporarily stop application threads, it does so only  intermittently. If CMS is taking a few seconds to complete, or is  occurring with increased frequency, your cluster may not have enough  memory to function efficiently.

**Queries:**

```
TODO
```

### Errors and overruns

| **Name**                | **Description**                                              | **[Metric type](https://www.datadoghq.com/blog/monitoring-101-collecting-data/)** | **Availability**                          |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ----------------------------------------- |
| Exceptions              | Requests for which Cassandra encountered an (often nonfatal) error | Work: Error                                                  | nodetool, JConsole, JMX/Metrics reporters |
| Timeout exceptions      | Requests not acknowledged within configurable timeout window | Work: Error                                                  | JConsole, JMX/Metrics reporters           |
| Unavailable exceptions  | Requests for which the required number of nodes was unavailable | Work: Error                                                  | JConsole, JMX/Metrics reporters           |
| Pending tasks           | Tasks in a queue awaiting a thread for processing            | Resource: Saturation                                         | nodetool, JConsole, JMX/Metrics reporters |
| Currently blocked tasks | Tasks that cannot yet be queued for processing               | Resource: Saturation                                         | nodetool, JConsole, JMX/Metrics reporters |

Cassandra’s count of **exceptions** thrown. In general, you want this number to be very small, although not all exceptions are created equal.

Cassandra’s **timeout exception** reflects the incomplete  (but not failed) handling of a request. Timeouts occur when the  coordinator node sends a request to a replica and does not receive a  response within the configurable timeout window. Timeouts are not  necessarily fatal—the coordinator will store the update and attempt to  apply it later—but they can indicate network issues or even disks  nearing capacity. 

**unavailable exception**, which indicates that Cassandra  was unable to meet the consistency requirements for a given request,  usually because one or more nodes were reported as down when the request arrived. An unavailable exception is the only exception that will cause a write  to fail, so any occurrences are serious. Cassandra’s inability to meet  consistency requirements can mean that several nodes are down or  otherwise unreachable, or that stringent consistency settings are  limiting the availability of your cluster.

Another way to detect signs of incipient problems is to monitor the  status of Cassandra’s tasks as it processes requests. Each type of task  in Cassandra (e.g., ReadStage tasks) has a queue for incoming tasks and  is allotted a certain number of threads for executing those tasks. If  all threads are in use, tasks will accumulate in the queue awaiting an  available thread. The number of tasks in a queue at any given moment is  described by the metric **pending tasks**. Once the queue of pending tasks fills up, Cassandra will register additional incoming tasks as **currently blocked**, although those tasks may eventually be accepted and executed.

**Queries:**

```
SELECT mean("Unavailables_FifteenMinuteRate") FROM "cassandra_ClientRequest" WHERE $timeFilter GROUP BY time($__interval) fill(null)
```
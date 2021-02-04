### Graphite

> References:
>
> https://graphite.readthedocs.io/en/latest/overview.html
>
> https://matt.aimonetti.net/posts/2013-06-practical-guide-to-graphite-monitoring/



## Feeding In Your Data

Plaintext, Pickle, and AMQP.

Data sent to Graphite is actually sent to the [Carbon and Carbon-Relay](https://graphite.readthedocs.io/en/latest/carbon-daemons.html), which then manage the data. The Graphite web interface reads this data back out, either from cache or straight off disk.

##### The plaintext protocol

The data sent must be in the following format: `<metric path> <metric value> <metric timestamp>`. 

```bash
PORT=2003
SERVER=graphite.your.org
echo "local.random.diceroll 4 `date +%s`" | nc ${SERVER} ${PORT}
```

##### The pickle protocol

Supports sending batches of metrics to Carbon in one go.

The general idea is that the pickled data forms a list of multi-level tuples:

```
[(path, (timestamp, value)), ...]
```

##### Using AMQP

When AMQP_METRIC_NAME_IN_BODY is set to True in your carbon.conf file,  the data should be of the same format as the plaintext protocol, e.g.  `echo local.random.diceroll 4 date +%s`. When AMQP_METRIC_NAME_IN_BODY is set to False, you should omit `local.random.diceroll`.

## Getting Your Data Into Graphite

##### Step 1 - Plan a Naming Hierarchy

Every series stored in Graphite has a unique identifier, which is composed of a metric name and optionally a set of tags.

In a traditional hierarchy, `website.orbitz.bookings.air` or something  like that would represent the number of air bookings on orbitz. Other examples:

```
<namespace>.<instrumented section>.<target (noun)>.<action (past tense verb)>
<namespace>.<client name>.http.<http verb>.<path>.<segments>.response_time

accounts.authentication.password.attempted
accounts.authentication.password.succeeded
accounts.authentication.password.failed

accounts.authentication.password.failure.no_email_found
accounts.authentication.password.failure.password_check_failed
accounts.authentication.password.failure.password_reset_required

accounts.authentication.password.failure.*
```

The disadvantage of a purely hierarchical system is that it is very  difficult to make changes to the hierarchy, since anything querying  Graphite will also need to be updated.  Additionally, there is no  built-in description of the meaning of any particular element in the  hierarchy.

To address these issues, Graphite also supports using tags to describe  your metrics, which makes it much simpler to design the initial  structure and to evolve it over time.  A tagged series is made up of a  name and a set of tags, like  `disk.used;datacenter=dc1;rack=a1;server=web01`. When series are named this way they can be selected using the [seriesByTag](https://graphite.readthedocs.io/en/latest/functions.html#graphite.render.functions.seriesByTag) function as described in [Graphite Tag Support](https://graphite.readthedocs.io/en/latest/tags.html) (for example: ```seriesByTag('name=~name.production.rollbacks.*'))```).

When using a tagged naming scheme it is much easier to add or alter  individual tags as needed.  It is important however to be aware that  changing the number of tags reported for a given metric or the value of a tag will create a new database file on disk, so tags should not be used for data that changes over the lifetime of a particular metric.

##### Step 2 - Configure your Data Retention

Graphite is built on fixed-size databases (see [Whisper.](https://graphite.readthedocs.io/en/latest/whisper.html)) so we have to configure in advance how much data we intend to store and at what level of precision.

To determine the best retention configuration, you must answer all of the following questions.

1. How often can you produce your data?
2. What is the finest precision you will require?
3. How far back will you need to look at that level of precision?
4. What is the coarsest precision you can use?
5. How far back would you ever need to see data? (yes it has to be finite, and determined ahead of time)

You need to create a schema by creating/editing the `/opt/graphite/conf/storage-schemas.conf` file.

When carbon receives a metric, it determines where on the filesystem the whisper data file should be for that metric. If the data file does not  exist, carbon knows it has to create it, but since whisper is a fixed  size database, some parameters must be determined at the time of file creation. Carbon looks at the schemas file, and in order of priority (highest to  lowest) looks for the first schema whose pattern matches the metric  name. If no schema matches the default schema (2 hours of minutely data) is used. Once the appropriate schema is determined, carbon uses the  retention configuration for the schema to create the whisper data file  appropriately.

##### Step 3 - Understanding the Graphite Message Format

```
metric_path value timestamp\n
```

`timestamp` is the number of seconds since unix epoch time. Carbon-cache will use the time of arrival if the `timestamp` is set to `-1`.


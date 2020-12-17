### Fluentd

[Fluentd](https://www.fluentd.org/) collects  events from various data sources and writes them to files, RDBMS, NoSQL, IaaS, SaaS, Hadoop and so on. Fluentd helps you unify your logging  infrastructure (Learn more about the [Unified Logging Layer](https://www.fluentd.org/blog/unified-logging-layer)).

An event consists of tag, time and record.  Tag is a string separated with '.' (e.g. myapp.access). It is used to  categorize events. Time is a UNIX time recorded at occurrence of an  event. Record is a JSON object.

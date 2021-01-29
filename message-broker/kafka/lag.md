### Lag in Kafka

```
lag = diff(last_consumed, last_produced) / producer rate
```

The offset is integer used to maintain the current position of a consumer in Kafka. 

Lag is the delta between last produced message and last committed offset.

Lag indicates how far behind your app is in processing up to date info.

Tools:

- [Kafka Lag Exporter](https://github.com/lightbend/kafka-lag-exporter)
- [Remora](https://github.com/zalando-incubator/remora)
- [Burrow](https://github.com/linkedin/Burrow)
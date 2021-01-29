### kafkacat

> References:
>
> https://github.com/edenhill/kafkacat

In **producer** mode kafkacat reads messages from stdin, delimited with a configurable delimiter (-D, defaults to newline), and produces them to the provided Kafka cluster (-b), topic (-t) and partition (-p).

In **consumer** mode kafkacat reads messages from a topic and partition and prints them to stdout using the configured message delimiter.

Examples:

```bash
# -b broker
# -t topic
# -K: key with delimeter
# -P producer mode
# -L metadata list mode
# -C consumer mode
# -f format of the output and the fields to include

# produce message
docker run --interactive \
           --network network-name \
           confluentinc/cp-kafkacat:latest \
           kafkacat -b kafka:29092 \
                    -t topic-name \
                    -K: \
                    -P <<EOF
1:{"id":1,"total":10.50,"name":"Average Joe"}
EOF

# list topics
docker run --tty \
           --network network-name \
           confluentinc/cp-kafkacat:latest \
           kafkacat -b kafka:29092 \
                    -L

# consume message
docker run --tty \
           --network network-name \
           confluentinc/cp-kafkacat:latest \
           kafkacat -b kafka:29092 \
                    -t topic-name \
                    -C \
                    -K: \
                    -f '\nKey (%K bytes): %k\t\nValue (%S bytes): %s\n\Partition: %p\tOffset: %o\n--\n'
```

Docker-compose:

```yaml
version: '2'
services:
  zookeeper:
    image: confluentinc/cp-zookeeper:latest
    ports:
      - 2181:2181
    environment:
      ZOOKEEPER_CLIENT_PORT: 2181
      ZOOKEEPER_TICK_TIME: 2000
  kafka:
    image: confluentinc/cp-kafka:latest
    depends_on:
      - zookeeper
    ports:
      - 9092:9092
    environment:
      KAFKA_BROKER_ID: 1
      KAFKA_ZOOKEEPER_CONNECT: zookeeper:2181
      KAFKA_ADVERTISED_LISTENERS: PLAINTEXT://kafka:29092,PLAINTEXT_HOST://localhost:9092
      KAFKA_LISTENER_SECURITY_PROTOCOL_MAP: PLAINTEXT:PLAINTEXT,PLAINTEXT_HOST:PLAINTEXT
      KAFKA_INTER_BROKER_LISTENER_NAME: PLAINTEXT
      KAFKA_OFFSETS_TOPIC_REPLICATION_FACTOR: 1
```


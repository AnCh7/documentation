## Monitoring via Telegraph

> References:
> http://cassandra.apache.org/doc/latest/operating/metrics.html
> https://jolokia.org/
> https://github.com/rhuss/jolokia
> https://github.com/influxdata/telegraf/tree/master/plugins/inputs/jolokia2

#### Jolokia

Download and unpack the jolokia agent to your Cassandra node:

```bash
mkdir /opt/jolokia && cd /opt/jolokia

sudo curl -o /opt/jolokia/jolokia-jvm-1.6.2-agent.jar -L http://search.maven.org/remotecontent?filepath=org/jolokia/jolokia-jvm/1.6.2/jolokia-jvm-1.6.2-agent.jar

chmod -R 777 /opt/jolokia/
```

Add the agent path as a JVM option to the end of your cassandra-env.sh file and restart cassandra

```bash
echo 'JVM_OPTS="$JVM_OPTS -javaagent:/opt/jolokia/jolokia-jvm-1.6.2-agent.jar=port=8778,host=0.0.0.0"' >> /etc/dse/cassandra/cassandra-env.sh
vi /etc/dse/cassandra/cassandra-env.sh

service dse restart

curl http://localhost:8778/jolokia/
```

#### InfluxDB connectivity

Firewall rules need to be updated. Please include Cassandra's IP range in it as a source filter.

```bash
# Check connectivity by running
curl -kI http://influxdb-url:8086/ping
```

#### Telegraf

Install the latest stable version of Telegraf using the yum package manager:

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/influxdb.repo
[influxdb]
name = InfluxDB Repository - RHEL \$releasever
baseurl = https://repos.influxdata.com/rhel/\$releasever/\$basearch/stable
enabled = 1
gpgcheck = 1
gpgkey = https://repos.influxdata.com/influxdb.key
EOF
```

Once repository is added to the yum configuration, install and start the Telegraf service by running:

```bash
sudo yum install telegraf
sudo systemctl start telegraf
```

Configuration:
```bash
cat /dev/null > /etc/telegraf/telegraf.conf
```

Telegraf configuration:
```bash
cat <<'EOF' >> /etc/telegraf/telegraf.conf
[[outputs.influxdb]]
urls = ["influxdb-url:8086"]
database = "statsd"
username = "statsd"
password = "xxxxxxxx"
skip_database_creation = true

[[inputs.diskio]]
	name_prefix = "cassandra_"

[[inputs.disk]]
	name_prefix = "cassandra_"

[[inputs.netstat]]
	name_prefix = "cassandra_"

[[inputs.procstat]]
  name_prefix = "cassandra_"
  pid_file = "/var/run/dse/dse.pid"

[[inputs.procstat]]
  name_prefix = "cassandra_jolokia_"
  pattern = "jolokia"
  pid_finder = "pgrep"

[[inputs.jolokia2_agent]]
  urls = ["http://localhost:8778/jolokia"]
  name_prefix = "cassandra_java_"
  [[inputs.jolokia2_agent]]
    urls = ["http://localhost:8778/jolokia"]
    name_prefix = "cassandra_java_"
  [[inputs.jolokia2_agent.metric]]
    name  = "BufferPool"
    mbean = "java.nio:name=*,type=BufferPool"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "OperatingSystem"
    mbean = "java.lang:name=*,type=OperatingSystem"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "GarbageCollector"
    mbean = "java.lang:name=*,type=GarbageCollector"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Memory"
    mbean = "java.lang:type=Memory"
  [[inputs.jolokia2_agent.metric]]
    name  = "MemoryPool"
    mbean = "java.lang:type=MemoryPool"

[[inputs.jolokia2_agent]]
  urls = ["http://localhost:8778/jolokia"]
  name_prefix = "cassandra_"
 [[inputs.jolokia2_agent.metric]]
    name  = "Table"
    mbean = "org.apache.cassandra.metrics:name=*,scope=*,keyspace=*,type=Table"
    tag_keys = ["name", "scope", "keyspace"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Keyspace"
    mbean = "org.apache.cassandra.metrics:name=*,scope=*,type=Keyspace"
    tag_keys = ["name", "scope"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "ThreadPools"
    mbean = "org.apache.cassandra.metrics:name=*,path=*,scope=*,type=ThreadPools"
    tag_keys = ["name", "path", "scope"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "ClientRequest"
    mbean = "org.apache.cassandra.metrics:name=*,scope=*,type=ClientRequest"
    tag_keys = ["name", "scope"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Cache"
    mbean = "org.apache.cassandra.metrics:name=*,scope=*,type=Cache"
    tag_keys = ["name", "scope"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "CQL"
    mbean = "org.apache.cassandra.metrics:name=*,type=CQL"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "DroppedMessage"
    mbean = "org.apache.cassandra.metrics:name=*,scope=*,type=DroppedMessage"
    tag_keys = ["name", "scope"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Streaming"
    mbean = "org.apache.cassandra.metrics:name=*,scope=*,type=Streaming"
    tag_keys = ["name", "scope"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Compaction"
    mbean = "org.apache.cassandra.metrics:name=*,type=Compaction"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "CommitLog"
    mbean = "org.apache.cassandra.metrics:name=*,type=CommitLog"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Storage"
    mbean = "org.apache.cassandra.metrics:name=*,type=Storage"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "HintedHandOffManager"
    mbean = "org.apache.cassandra.metrics:name=*,type=HintedHandOffManager"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "HintsService"
    mbean = "org.apache.cassandra.metrics:name=*,type=HintsService"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Index"
    mbean = "org.apache.cassandra.metrics:name=*,scope=*,type=Index"
    tag_keys = ["name", "scope"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "BufferPool"
    mbean = "org.apache.cassandra.metrics:name=*,type=BufferPool"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Client"
    mbean = "org.apache.cassandra.metrics:name=*,type=Client"
    tag_keys = ["name"]
    field_prefix = "$1_"
  [[inputs.jolokia2_agent.metric]]
    name  = "Batch"
    mbean = "org.apache.cassandra.metrics:name=*,type=Batch"
    tag_keys = ["name"]
    field_prefix = "$1_"
EOF
```

Test your configuration file like:
```bash
telegraf test --config /etc/telegraf/telegraf.conf
```

which will also print you the response  from the jolokia endpoints, if your setup is correct. Also you can make  direct http request to the jolokia endpoint, for me it really helped to  find what rules to put into my telegraf.conf file.

If you are happy with your configuration, you need to restart telegraf:
```bash
sudo service telegraf restart
```

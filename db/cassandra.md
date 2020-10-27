#### Logs

https://murukeshm.github.io/cassandra/trunk/troubleshooting/reading_logs.html

Cassandra has three main logs, the `system.log`, `debug.log` and `gc.log`.

These logs by default live in `${CASSANDRA_HOME}/logs`, but most Linux distributions relocate logs to `/var/log/cassandra`. 
- `system.log` - default Cassandra log.
```bash
grep 'WARN\|ERROR' system.log | tail
zgrep 'WARN\|ERROR' system.log.* | less
```
- `debug.log` - additional debugging information.
- `gc.log` - standard Java GC log.
```bash
grep stopped gc.log.0.current | tail
```
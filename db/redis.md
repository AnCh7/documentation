## Redis Persistence

> References:
> https://redis.io/topics/persistence

Redis provides a different range of persistence options:

- The RDB persistence performs point-in-time snapshots of your dataset at specified intervals.
- The AOF persistence logs every write operation received  by the server, that will be played again at server startup,  reconstructing the original dataset. Commands are logged using the same  format as the Redis protocol itself, in an append-only fashion. Redis is able to rewrite the log in the background when it gets too big.
- It is possible to combine both AOF and RDB in the same  instance.

---

By default Redis saves snapshots of the dataset on disk, in a binary file called `dump.rdb`. You can configure Redis to have it save the dataset every N seconds if there are at least M changes in the dataset, or you can manually call the [SAVE](https://redis.io/commands/save) or [BGSAVE](https://redis.io/commands/bgsave) commands.

For example, this configuration will make Redis automatically dump the dataset to disk every 60 seconds if at least 1000 keys changed:
```
save 60 1000
```

---

You can turn on the AOF in your configuration file:
```
appendonly yes
```

From now on, every time Redis receives a command that changes the dataset (e.g. [SET](https://redis.io/commands/set)) it will append it to the AOF.  When you restart            Redis it will re-play the AOF to rebuild the state.

---

#### How durable is the append only file?

You can configure how many times Redis will [`fsync`](http://linux.die.net/man/2/fsync) data on disk. There are three options:

- `appendfsync always`: `fsync`  every time new commands are appended to the AOF. 
- `appendfsync everysec`: `fsync`  every second. Fast enough and you can lose 1 second of data if there is a disaster.
- `appendfsync no`: Never `fsync`,  just put your data in the hands of the Operating System. Normally Linux will flush data every 30 seconds with  this configuration, but it's up to the kernel exact tuning.

---

#### Reading data

```bash
# Retrieving all keys
KEYS *

# Get key type
type xxxxxxxxx

# Get key value
string: get xxxxxxxxx
hash:   hgetall xxxxxxxxx
list:   lrange xxxxxxxxx 0 -1
set:    smembers xxxxxxxxx
zset:   zrange xxxxxxxxx 0 -1 withscores
```

#### Backup and restore

```bash
redis-cli config get dir
# 1) "dir"
# 2) "/data"

redis-cli save

ls -lah /data
# -rw-r--r-- 1 root root 8.8M Oct 26 16:17 appendonly.aof
# -rw-r--r-- 1 root root 9.5K Oct 26 16:16 dump.rdb

# start an Append Only File rewrite process. The rewrite will create a small optimized version of the current Append Only File.
redis-cli bgrewriteaof ;
# 1:M 26 Oct 20:16:43.351 * Background AOF rewrite terminated with success
# 1:M 26 Oct 20:16:43.351 * Residual parent diff successfully flushed to the rewritten AOF (0.00 MB)
# 1:M 26 Oct 20:16:43.351 * Background AOF rewrite finished successfully

# copy to local
kubectl cp default/pod-name:/data/ ~/Downloads/pod-name/data -c redis

# copy to new pod
kubectl cp ~/Downloads/pod-name/data/appendonly.aof default/pod-name:/data/appendonly.aof -c redis
kubectl cp ~/Downloads/pod-name/data/dump.rdb default/pod-name:/data/dump.rdb -c redis

# change file permissions
chown root:root appendonly.aof
chown root:root dump.rdb

# restart new pod

# check if keys are loaded
redis-cli info keyspace
# db0:keys=36,expires=3,avg_ttl=4412089
```


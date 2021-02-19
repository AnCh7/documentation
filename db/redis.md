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


#### Reading data

```
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



### etcd

etcd is a distributed reliable key-value store for the most critical data of a distributed system, with a focus on being:

- Simple: well-defined, user-facing API (gRPC)
- Secure: automatic TLS with optional client cert authentication
- Fast: benchmarked 10,000 writes/sec (written in Go)
- Reliable: properly distributed using [Raft](https://raft.github.io/) (manages a highly-available replicated log)


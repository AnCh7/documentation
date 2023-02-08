#### BadgerDB

- BadgerDB is an embeddable, persistent, and fast key-value (KV) database written in pure Go.
- It provides Get, Set, Delete, and Iterate functions. 
- It adds CompareAndSet and CompareAndDelete atomic operations
- It does not aim to be a database and hence does not provide transactions, versioning or snapshots.
- Badger separates keys from values. The keys are stored in LSM tree, while the values are stored in a write-ahead log called the value log. Keys tend to be smaller than values. Thus this set up produces much smaller LSM trees. When required, the values are directly read from the log stored on SSD, utilizing its vastly superior random read performance.
- By default, Badger ensures all the data is persisted to the disk.
- Badger provides a Stream framework, which concurrently iterates over all or a portion of the DB, converting data into custom key-values, and streams it out serially to be sent over network, written to disk, or even written back to Badger. This is a lot faster way to iterate over Badger than using a single Iterator. Stream supports Badger in both managed and normal mode.
- Badger relies on the client to perform garbage collection at a time of their choosing.
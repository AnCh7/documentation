##### /pkg is obsolete

Go distribution stored the standard library in a subdirectory called `pkg/` and the commands which built upon it in `cmd/`. This wasn’t so much a deliberate taxonomy but a by product of the original `make` based build system. In [September 2014](https://groups.google.com/forum/m/#!msg/golang-dev/c5AknZg3Kww/OFLmvGyfNR0J), the Go distribution dropped the `pkg/` subdirectory

##### internal packages

`internal/` is a special directory name recognised by the `go` tool which will prevent one package from being imported by another unless both share a common ancestor. Packages within an `internal/` directory are therefore said to be *internal packages*.
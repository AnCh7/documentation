### Effective Go

> References:
>
> https://golang.org/doc/effective_go.html



##### Formatting

The `gofmt` program (also available as `go fmt`) reads a Go program and emits the source in a standard style of indentation and vertical alignment, retaining and if necessary reformatting comments.

##### Commentary

Go provides C-style `/* */` block comments and C++-style `//` line comments.

Line comments are the norm; block comments appear mostly as package comments.

Every package should have a *package comment*, a block comment preceding the package clause.

The comments are uninterpreted plain text.

Every exported (capitalized) name in a program should have a doc comment.

##### Names

The visibility of a name outside a package is determined by whether its first character is upper case.

When a package is imported, the package name becomes an accessor for the contents.

```go
import "bytes"

bytes.Buffer
```

Packages are given lower case, single-word names; there should be no need for underscores or mixedCaps.

The package name is only the default name for imports; it need not be unique across all source code.

If you have a field called `owner` (lower case, unexported), the getter method should be called `Owner` (upper case, exported), not `GetOwner`.

A setter function, if needed, will likely be called `SetOwner`.

By convention, one-method interfaces are named by the method name plus an -er suffix or similar modification to construct an agent noun: `Reader`, `Writer`, `Formatter`, `CloseNotifier` etc.

The convention in Go is to use `MixedCaps` or `mixedCaps` rather than underscores to write multiword names.


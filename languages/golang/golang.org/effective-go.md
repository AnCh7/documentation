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

##### Semicolons

Like C, Go's formal grammar uses semicolons to terminate statements, but unlike in C, those semicolons do not appear in the source. Instead the lexer inserts semicolons automatically as it scans. This could be summarized as, "if the newline comes after a token that could end a statement, insert a semicolon".

```
break continue fallthrough return ++ -- ) }
```

Idiomatic Go programs have semicolons only in places such as `for` loop clauses and to separate multiple statements on a line.

##### Control structures

- no `do` or `while` loop, only a slightly generalized `for`
- `switch` is more flexible
- `if` and `switch` accept an optional initialization statement like that of `for`
- `break` and `continue` statements take an optional label to identify what to break or continue
- new control structures including a type switch and a multiway communications multiplexer, `select`. 
- no parentheses and the bodies must always be brace-delimited.

**If**

Mandatory braces encourage writing simple `if` statements on multiple lines. 

**For**

There are three forms of `for` loop, only one of which has semicolons.

```go
// Like a C for
for init; condition; post { }

// Like a C while
for condition { }

// Like a C for(;;)
for { }
```

If you're looping over an array, slice, string, or map, or reading from a channel, a `range` clause can manage the loop.

```go
for key, value := range oldMap {
    newMap[key] = value
}
```

If you only need the first item in the range (the key or index), drop the second:

```go
for key := range m {
    if key.expired() {
        delete(m, key)
    }
}
```

If you only need the second item in the range (the value), use the *blank identifier*, an underscore, to discard the first:

```go
sum := 0
for _, value := range array {
    sum += value
}
```

Go has no comma operator and `++` and `--` are statements not expressions.
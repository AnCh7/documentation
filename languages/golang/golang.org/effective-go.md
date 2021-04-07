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

##### If

Mandatory braces encourage writing simple `if` statements on multiple lines. 

##### For

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

##### Switch

- The expressions need not be constants or even integers.
- The cases are evaluated top to bottom until a match is found
- if the `switch` has no expression it switches on `true`.
- There is no automatic fall through, but cases can be presented in comma-separated lists.

Go's functions and methods can return multiple values.

The return or result "parameters" of a Go function can be given names and used as regular variables, just like the incoming parameters.

##### Defer

Go's `defer` statement schedules a function call (the *deferred* function) to be run immediately before the function executing the `defer` returns.

The arguments to the deferred function (which include the receiver if the function is a method) are evaluated when the *defer* executes, not when the *call* executes.

Deferred functions are executed in LIFO order.

##### Allocation

`new(T)` allocates zeroed storage for a new item of type `T` and returns its address, a value of type `*T`. Zero value of each type can be used without further initialization.

*composite literal* is an expression that creates a new instance each time it is evaluated.

`make(T, args)` creates slices, maps, and channels only, and it returns an *initialized* (not *zeroed*) value of type `T` (not `*T`).

##### Arrays

Arrays are values. Assigning one array to another copies all the elements.

If you pass an array to a function, it will receive a *copy* of the array, not a pointer to it.

The size of an array is part of its type.  The types `[10]int` and `[20]int` are distinct.

##### Slices

Slices hold references to an underlying array, and if you assign one slice to another, both refer to the same array.

If the data exceeds the capacity, the slice is reallocated.

##### Maps

The key can be of any type for which the equality operator is defined, such as integers, floating point and complex numbers, strings, pointers, interfaces (as long as the dynamic type supports equality), structs and arrays.

A set can be implemented as a map with value type `bool`.

The "comma ok" idiom.

```
seconds, ok = timeZone[tz]
```

##### Printing

You don't need to provide a format string - `Print` and `Println` functions do not take a format string but instead generate a default format for each argument. 

The formatted print functions `fmt.Fprint` and friends take as a first argument any object that implements the `io.Writer` interface.

##### Constants

In Go, enumerated constants are created using the `iota` enumerator. Since `iota` can be part of an expression and expressions can be implicitly repeated, it is easy to build intricate sets of values.

```go
type ByteSize float64

const (
    _           = iota // ignore first value by assigning to blank identifier
    KB ByteSize = 1 << (10 * iota)
    MB
    GB
    TB
    PB
    EB
    ZB
    YB
)
```

##### The init function

Each source file can define its own niladic `init` function to set up whatever state is required.  (Actually each file can have multiple `init` functions.) `init` is called after all the variable declarations in the package have evaluated their initializers, and those are evaluated only after all the imported packages have been initialized.

##### Methods

The rule about pointers vs. values for receivers is that value methods can be invoked on pointers and values, but pointer methods can only be invoked on pointers. This rule arises because pointer methods can modify the receiver; invoking them on a value would cause the method to receive a copy of the value, so any modifications would be discarded. The language therefore disallows this mistake.

##### Interfaces

Interfaces in Go provide a way to specify the behavior of an object: if something can do *this*, then it can be used *here*.

A type can implement multiple interfaces.

##### Interface conversions and type assertions 

A type assertion takes an interface value and extracts from it a value of the specified explicit type.

```go
value.(typeName)
```

To extract the string we know is in the value, we could write:

```go
if str, ok := value.(string); ok {
    return str
} else if str, ok := value.(Stringer); ok {
    return str.String()
}
```

##### Generality

If a type exists only to implement an interface and will never have exported methods beyond that interface, there is no need to export the type itself. In such cases, the constructor should return an interface value rather than the implementing type.

##### The blank identifier

The blank identifier can be assigned or declared with any value of any type, with the value discarded harmlessly. It's a bit like writing to the Unix `/dev/null` file: it represents a write-only value to be used as a place-holder where a variable is needed but the actual value is irrelevant.

To import the package only for its side effects, rename the package to the blank identifier:

```go
import _ "net/http/pprof"
```

If it's necessary only to ask whether a type implements an interface, without actually using the interface itself, perhaps as part of an error check, use the blank identifier to ignore the type-asserted value:

```go
if _, ok := val.(json.Marshaler); ok {
    fmt.Printf("value %v of type %T implements json.Marshaler\n", val, val)
}
```

##### Embedding

When we embed a type, the methods of that type become methods of the outer type, but when they are invoked the receiver of the method is the inner type, not the outer one.

##### Concurrency

Only one goroutine has access to the value at any given time. Data races cannot occur, by design.

Do not communicate by sharing memory; instead, share memory by communicating.

A goroutine is a function executing concurrently with other goroutines in the same address space.  It is lightweight - just an a allocation of stack space. Stacks start small, so they are cheap, and grow by allocating (and freeing) heap storage as required.

Goroutines are multiplexed onto multiple OS threads so if one should block, such as while waiting for I/O, others continue to run.  Their design hides many of the complexities of thread creation and management.

Function literals are closures: the implementation makes sure the variables referred to by the function survive as long as they are active.

```go
func Announce(message string, delay time.Duration) {
    go func() {
        time.Sleep(delay)
        fmt.Println(message)
    }()  // Note the parentheses - must call the function.
}
```

Unbuffered channels combine communication—the exchange of a value—with synchronization—guaranteeing that two calculations (goroutines) are in a known state.

Receivers always block until there is data to receive. If the channel is unbuffered, the sender blocks until the receiver has received the value.

If the channel has a buffer, the sender blocks only until the value has been copied to the buffer; if the buffer is full, this means waiting until some receiver has retrieved a value.


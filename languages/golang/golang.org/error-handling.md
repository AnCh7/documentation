### Error handling and Go

> https://blog.golang.org/error-handling-and-go

The `error` type is an interface type. An `error` variable represents any value that can describe itself as a string:

```go
type error interface {
    Error() string
}
```

You can construct errors with the `errors.New` function. It takes a string that it converts to an `errors.errorString` and returns as an `error` value.

```go
// New returns an error that formats as the given text.
func New(text string) error {
    return &errorString{text}
}
```

The [fmt](https://golang.org/pkg/fmt/) package formats an `error` value by calling its `Error() string` method. A useful function is the `fmt` package's `Errorf`. It formats a string according to `Printf`'s rules and returns it as an `error` created by `errors.New`.

The language's design and conventions encourage you to explicitly check for errors where they occur. In some cases this makes Go code verbose, but fortunately there are some techniques you can use to minimize repetitive error handling.

### Errors are values

> https://blog.golang.org/errors-are-values

Values can be programmed, and since errors are values, errors can be programmed.

With helper function:

```go
scanner := bufio.NewScanner(input)
for scanner.Scan() {
    token := scanner.Text()
    // process token
}
if err := scanner.Err(); err != nil {
    // process the error
}
```

Without helper function:

```go
scanner := bufio.NewScanner(input)
for {
    token, err := scanner.Scan()
    if err != nil {
        return err // or maybe break
    }
    // process token
}
```

Another example:

```go
write := func(buf []byte) {
    if err != nil {
        return
    }
    _, err = w.Write(buf)
}

write(p0[a:b])
write(p1[c:d])
write(p2[e:f])

if err != nil {
    return err
}
```

### Working with Errors in Go 1.13

> https://blog.golang.org/go1.13-errors

Compare an error to a known *sentinel* value, to see if a specific error has occurred:

```go
var ErrNotFound = errors.New("not found")

if err == ErrNotFound {
    // something wasn't found
}
```

Adding information:

```go
if err != nil {
    return fmt.Errorf("decompress %v: %v", name, err)
}
```

**The Unwrap method:**

An error which contains another may implement an `Unwrap` method returning the underlying error. If `e1.Unwrap()` returns `e2`, then we say that `e1` *wraps* `e2`, and that you can *unwrap* `e1` to get `e2`.

**Examining errors with Is and As:**

```go
// compares an error to a value
if errors.Is(err, ErrNotFound) {
    // something wasn't found
}

// tests whether an error is a specific type
var e *QueryError
if errors.As(err, &e) {
    // err is a *QueryError, and e is set to the error's value
}
```

**Wrapping errors with %w**

When `%w` is present, the error returned by `fmt.Errorf` will have an `Unwrap` method returning the argument of `%w`, which must be an error.

```go
if err != nil {
    // Return an error which unwraps to err.
    return fmt.Errorf("decompress %v: %w", name, err)
}
```

Wrap an error to expose it to callers. Do not wrap an error when doing so would expose implementation details.

Wrapping an error makes that error part of your API.

If we wish a function to return an identifiable error condition, we might return an error wrapping a sentinel.

```go
var ErrNotFound = errors.New("not found")

func FetchItem(name string) (*Item, error) {
    if itemNotFound(name) {
        return nil, fmt.Errorf("%q: %w", name, ErrNotFound)
    }
}
```

If a function is defined as returning an error wrapping some sentinel or type, do not return the underlying error directly.
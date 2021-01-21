### Defer, Panic, and Recover

> References:
>
> https://blog.golang.org/defer-panic-and-recover



A **defer statement** pushes a function call onto a list. The list of saved calls is executed after the surrounding function returns. Defer is commonly used to simplify functions that perform various clean-up actions.

The behavior of defer statements is straightforward and predictable:

1. A deferred function's arguments are evaluated when the defer statement is evaluated.

```go
// the expression "i" is evaluated when the Println call is deferred.
func a() {
    i := 0
    defer fmt.Println(i)
    i++
    return
}
```

1. Deferred function calls are executed in Last In First Out order after the surrounding function returns.

```go
// This function prints "3210"
func b() {
    for i := 0; i < 4; i++ {
        defer fmt.Print(i)
    }
}
```

1. Deferred functions may read and assign to the returning function's named return values.

```go
// A deferred function increments the return value i after the surrounding function returns
func c() (i int) {
    defer func() { i++ }()
    return 1
}
```

**Panic** is a built-in function that stops the ordinary flow of control and begins *panicking*. When the function F calls panic, execution of F stops, any deferred functions in F are executed normally, and then F returns to its caller.

**Recover** is a built-in function that regains control of a panicking goroutine. Recover is only useful inside deferred functions. During normal execution, a call to recover will return nil and have no other effect. If the current goroutine is panicking, a call to recover will capture the value given to panic and resume normal execution.

The convention in the Go libraries is that even when a package uses panic internally, its external API still presents explicit error return values.
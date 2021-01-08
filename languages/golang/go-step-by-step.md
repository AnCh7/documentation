## Go Programming Language: An Introductory Golang Tutorial

> References:
> https://www.toptal.com/go/go-programming-a-step-by-step-introductory-tutorial
> https://www.toptal.com/go/golang-oop-tutorial


It omits OOP idioms such as inheritance and polymorphism, in favor of composition and simple interfaces. It has no subclassing, and so there are no inheritance diamonds or super calls or  virtual methods to trip you up.

*Mixins* are available by embedding structs anonymously, allowing their methods to be called directly on the containing struct.

Promoting methods in this way is called *forwarding*, and it's not the  same as subclassing: the method will still be invoked on the inner,  embedded struct.

Embedding also doesn't imply polymorphism.

Go is also a great language for writing [*concurrent programs*](https://www.toptal.com/scala/concurrency-and-fault-tolerance-made-easy-an-intro-to-akka).

Example:

```go
package funding

type Fund struct {
    // balance is unexported (private), because it's lowercase
    balance int
}

// A regular function returning a pointer to a fund
func NewFund(initialBalance int) *Fund {
    // We can return a pointer to a new struct without worrying about
    // whether it's on the stack or heap: Go figures that out for us.
    return &Fund{
        balance: initialBalance,
    }
}

// Methods start with a *receiver*, in this case a Fund pointer
func (f *Fund) Balance() int {
    return f.balance
}

func (f *Fund) Withdraw(amount int) {
    f.balance -= amount
}
```

Benchmarks are like unit tests, but include a loop which runs the same code many times. This allows the framework to time how long each iteration takes.

```go
package funding

import "testing"

func BenchmarkFund(b *testing.B) {
    // Add as many dollars as we have iterations this run
    fund := NewFund(b.N)

    // Burn through them one at a time until they are all gone
    for i := 0; i < b.N; i++ {
        fund.Withdraw(1)
    }

    if fund.Balance() != 0 {
        b.Error("Balance wasn't zero:", fund.Balance())
    }
}
```

Goroutines are [green threads](https://en.wikipedia.org/wiki/Green_threads) – lightweight threads managed by the Go runtime, not by the operating system.

```go
// Returns immediately, without waiting for `DoSomething()` to complete
go DoSomething()
```

```go
go func() {
    // ... do stuff ...
}() // Must be a function *call*, so remember the ()
```

`defer` takes a function (or method) call and runs it immediately before the current function returns, after everything else is done. Deferred function will run *even if there is a panic* in the main function. Deferred functions will execute in the *reverse* order to which they were called.

```go
func() {
    resource.Lock()
    defer resource.Unlock()

    // Do stuff with resource
}()
```

Channels are the basic communication mechanism between goroutines. Values are sent to the channel (with `channel <- value`), and can be received on the other side (with `value = <- channel`). Channels are “goroutine safe”, meaning that any number of goroutines can send to and receive from them at the same time.

By default, Go channels are *unbuffered*. This means that sending a value to a channel will block until another goroutine is ready to  receive it immediately.  Go also supports fixed buffer sizes for  channels (using `make(chan someType, bufferSize)`).

An interface is a set of method signatures. Any type that implements all of those methods can be treated as that interface (without being  declared to do so).

```go
package funding

type FundServer struct {
    Commands chan interface{}
    fund Fund
}

func NewFundServer(initialBalance int) *FundServer {
    server := &FundServer{
        // make() creates builtins like channels, maps, and slices
        Commands: make(chan interface{}),
        fund: NewFund(initialBalance),
    }
    // Spawn off the server's main loop immediately
    go server.loop()
    return server
}

func (s *FundServer) loop() {
    // The built-in "range" clause can iterate over channels,
    // amongst other things
    for command := range s.Commands {
        // Handle the command
    }
}
```

---

Go do not support dynamic binding for non-interface  types. It enables the compiler to know which function will be called at  compile time and avoids the overhead of dynamic method dispatch. This also discourages the use of inheritance as a general composition  pattern. Instead, interfaces are the way to go.

The Go compiler treats a type as an implementation of an interface when it implements the declared functions (duck typing).

---

The Golang OOP guideline here is that **no constructor should call another constructor**. If this is thoroughly applied, every singleton used in an application will be created at the topmost level. 

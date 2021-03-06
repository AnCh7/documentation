### Go Concurrency Patterns: Context

> https://blog.golang.org/context

`context` package makes it easy to pass request-scoped values, cancelation signals, and deadlines across API boundaries to all the goroutines involved in handling a request.

The `Done` method returns a channel that acts as a cancelation signal to functions running on behalf of the `Context`: when the channel is closed, the functions should abandon their work and return.

The `Err` method returns an error indicating why the `Context` was canceled.

A `Context` does *not* have a `Cancel` method for the same reason the `Done` channel is receive-only: the function receiving a cancelation signal is usually not the one that sends the signal.

A `Context` is safe for simultaneous use by multiple goroutines. Code can pass a single `Context` to any number of goroutines and cancel that `Context` to signal all of them.

`Value` allows a `Context` to carry request-scoped data.

The `context` package provides functions to *derive* new `Context` values from existing ones. These values form a tree: when a `Context` is canceled, all `Contexts` derived from it are also canceled.



### Package context

> https://golang.org/pkg/context/

Package context defines the Context type, which carries deadlines, cancellation signals, and other request-scoped values across API boundaries and between processes.

Incoming requests to a server should create a Context, and outgoing calls to servers should accept a Context. 

The chain of function calls between them must propagate the Context, optionally replacing it with a derived Context created using WithCancel, WithDeadline, WithTimeout, or WithValue. When a Context is canceled, all Contexts derived from it are also canceled.

The WithCancel, WithDeadline, and WithTimeout functions take a Context (the parent) and return a derived Context (the child) and a CancelFunc. Calling the CancelFunc cancels the child and its children, removes the parent's reference to the child, and stops any associated timers.

Do not store Contexts inside a struct type; instead, pass a Context explicitly to each function that needs it. The Context should be the first parameter, typically named ctx:

```go
func DoSomething(ctx context.Context, arg Arg) error {
	// ... use ctx ...
}
```

Do not pass a nil Context, even if a function permits it. Pass context.TODO if you are unsure about which Context to use.

Use context Values only for request-scoped data that transits processes and APIs, not for passing optional parameters to functions.

The same Context may be passed to functions running in different goroutines; Contexts are safe for simultaneous use by multiple goroutines.

---

**Background** returns a non-nil, empty Context. It is never canceled, has no values, and has no deadline. It is typically used by the main function, initialization, and tests, and as the top-level Context for incoming requests.

---

**TODO** returns a non-nil, empty Context. Code should use context.TODO when it's unclear which Context to use or it is not yet available (because the surrounding function has not yet been extended to accept a Context parameter).



### Contexts and structs

> References:
> https://blog.golang.org/context-and-structs


When designing an API with context, remember the advice: pass `context.Context` in as an argument; don't store it in structs.

##### Prefer contexts passed as arguments

With this pass-as-argument design, users can set per-call deadlines, cancellation, and metadata. There's no expectation that a `context.Context` passed to one method will be used by any other method. Context is scoped to as small an operation as it needs to be, which greatly  increases the utility and clarity of `context` in this package.

##### Storing context in structs leads to confusion

When you store the context in a struct, you obscure lifetime to the callers, or worse intermingle two scopes together in unpredictable ways. The API would need a good deal of documentation to explicitly tell the user exactly what the `context.Context` is used for. The user might also have to read code rather than being able to rely on the structure of the API conveys.

##### Preserving backwards compatibility

The duplicate approach should be preferred over the context-in-struct. However, in some cases it's impractical: for example, if your API  exposes a large number of functions, then duplicating them all might be  infeasible.
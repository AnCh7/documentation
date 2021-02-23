## Pipelines and cancellation

> References:
>
> https://blog.golang.org/pipelines



A pipeline is a series of *stages* connected by channels, where each stage is a group of goroutines running the same function.

 In each stage, the goroutines

- receive values from *upstream* via *inbound* channels
- perform some function on that data, usually producing new values
- send values *downstream* via *outbound* channels

Each stage has any number of inbound and outbound channels, except the first and last stages, which have only outbound or inbound channels, respectively.  The first stage is sometimes called the *source* or *producer*; the last stage, the *sink* or *consumer*.

#### Fan-out, fan-in

Multiple functions can read from the same channel until that channel is closed; this is called *fan-out*. 

A function can read from multiple inputs and proceed until all are closed by multiplexing the input channels onto a single channel that's closed when all the inputs are closed.  This is called *fan-in*.

The `merge` function converts a list of channels to a single channel by starting a goroutine for each inbound channel that copies the values to the sole outbound channel.  Once all the `output` goroutines have been started, `merge` starts one more goroutine to close the outbound channel after all sends on that channel are done.

Sends on a closed channel panic, so it's important to ensure all sends are done before calling close.  The [`sync.WaitGroup`](https://golang.org/pkg/sync/#WaitGroup) type provides a simple way to arrange this synchronization:

```go
func merge(cs ...<-chan int) <-chan int {
    var wg sync.WaitGroup
    out := make(chan int)

    // Start an output goroutine for each input channel in cs.  output
    // copies values from c to out until c is closed, then calls wg.Done.
    output := func(c <-chan int) {
        for n := range c {
            out <- n
        }
        wg.Done()
    }
    wg.Add(len(cs))
    for _, c := range cs {
        go output(c)
    }

    // Start a goroutine to close out once all the output goroutines are
    // done.  This must start after the wg.Add call.
    go func() {
        wg.Wait()
        close(out)
    }()
    return out
}
```

Goroutines are not garbage collected; they must exit on their own.

#### Explicit cancellation

A way to tell an unknown and unbounded number of goroutines to stop sending their values downstream in Go is to close a channel, because [a receive operation on a closed channel can always proceed immediately, yielding the element type's zero value.](https://golang.org/ref/spec#Receive_operator)

Here are the guidelines for pipeline construction:

- stages close their outbound channels when all the send operations are done.
- stages keep receiving values from inbound channels until those channels are closed or the senders are unblocked.

Pipelines unblock senders either by ensuring there's enough buffer for all the values that are sent or by explicitly signalling senders when the receiver may abandon the channel.
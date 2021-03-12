## Waiting on Goroutines

> References:
> https://scene-si.org/2020/05/29/waiting-on-goroutines



Go program doesn't wait for your goroutines to finish. You have to ensure a way where your invoking function will wait for some sort of a signal to continue execution.

### The channel

```go
finished := make(chan bool) // the messaging channel

greet := func() {
	time.Sleep(time.Second)
	fmt.Println("Hello 👋")
	finished <- true // we send to the channel when done
}

go greet()

<-finished // we are waiting for greet to finish
```



### The context

```go
ctx, cancel := context.WithCancel(context.Background())

greet := func() {
	defer cancel() // we cancel the ctx when done

	time.Sleep(time.Second)
	fmt.Println("Hello 👋")
}

go greet()

<-ctx.Done() // wait here :)
```

```go
ctx := context.Background()
ctx = context.WithTimeout(ctx, time.Second)
ctx, cancel := context.WithCancel(ctx)
```

### The lock

```go
var lock sync.Mutex

greet := func() {
	defer lock.Unlock() // we unlock when done

	time.Sleep(time.Second)
	fmt.Println("Hello 👋")
}

// get the lock before invoking greet
lock.Lock()

go greet()

// get lock again - this will only happen when greet() will finish
lock.Lock() // try to get lock again
```

### The group

```go
var wg sync.WaitGroup

greet := func() {
	defer wg.Done() // we unlock when done

	time.Sleep(time.Second)
	fmt.Println("Hello 👋")
}

// how many functions will we call?
wg.Add(1)

go greet()

wg.Wait() // wait here :)
```

### Atomic operations

```go
type WG struct {
	wait  chan struct{}
	state int64
}

func NewWG() *WG {
	return &WG{
		wait: make(chan struct{}),
	}
}

func (wg *WG) Add(x int64) {
	atomic.AddInt64(&wg.state, x)
}

func (wg *WG) Done() {
	after := atomic.AddInt64(&wg.state, -1)
	if after == 0 {
		wg.wait <- struct{}{}
	}
}

func (wg *WG) Wait() {
	<-wg.wait
}
```

### Error groups

```go
var group errgroup.Group

greet := func() error {
	time.Sleep(time.Second)
	fmt.Println("Hello 👋")
	return nil
}

group.Go(greet)

// get the first error from the group
if err := group.Wait(); err != nil {
	log.Fatal("We have an error:", err)
}
```

```go
errFinished := errors.New("done")

// the produced context is meant to be used in the passed functions to group.Go for cancelation
group, ctx := errgroup.WithContext(context.Background())

greet := func() error {
	time.Sleep(time.Second)
	fmt.Println("Hello 👋")
	return errFinished
}

group.Go(greet)

// if we don't return an error, this would cause a deadlock - no running goroutines
<-ctx.Done()

// get the first error from the group
if err := group.Wait(); err != nil {
	log.Fatalf("We have an error, err=%s, ctx.err=%s", err, ctx.Err())
}
```


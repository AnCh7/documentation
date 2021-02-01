### Garbage collection

> References:
>
> https://stackoverflow.com/questions/7823725/what-kind-of-garbage-collection-does-go-use
>
> https://blog.golang.org/ismmkeynote



Go 1.0 garbage collector:

- same as Go 1.1, but instead of being mostly precise the garbage  collector is conservative. The conservative GC is able to ignore objects such as []byte.

Go 1.1 garbage collector:

- mark-and-sweep (parallel implementation)
- non-generational
- non-compacting
- mostly precise (except stack frames)
- stop-the-world
- bitmap-based representation
- zero-cost when the program is not allocating memory (that is:  shuffling pointers around is as fast as in C, although in practice this  runs somewhat slower than C because the Go compiler is not as advanced  as C compilers such as GCC)
- supports finalizers on objects
- there is no support for weak references

Go 1.3 garbage collector updates on top of Go 1.1:

- concurrent sweep (results in smaller pause times)
- fully precise

Plans for Go 1.4+ garbage collector:

- hybrid stop-the-world/concurrent collector
- stop-the-world part limited by a 10ms deadline
- CPU cores dedicated to running the concurrent collector
- tri-color mark-and-sweep algorithm
- non-generational
- non-compacting
- fully precise
- incurs a small cost if the program is moving pointers around
- lower latency, but most likely also lower throughput, than Go 1.3 GC

Replacing the GC with a different one is controversial, for example:

- except for very large heaps, it is unclear whether a generational GC would be faster overall
- package "unsafe" makes it hard to implement fully precise GC and compacting GC

Prior to Go 1.5, Go has used a **parallel stop-the-world** (STW) collector. While STW collection has many downsides, it does at least have predictable and controllable heap growth behavior.

Go 1.5 introduces a **concurrent collector**. This has many advantages over STW collection, but it **makes heap growth harder to control because the application can allocate memory while the garbage collector is running**. 

---

Go programs have hundreds of thousands of stacks. They are managed by the Go scheduler and are always preempted at GC safepoints. The Go scheduler multiplexes Go routines onto OS threads which hopefully run with one OS thread per HW thread. We manage the stacks and their size by copying them and updating pointers in the stack. It's a local operation so it scales fairly well.

Go is a value-oriented language in the tradition of C-like systems languages rather than reference-oriented language in the tradition of most managed runtime languages.

Value-orientation also helps with the foreign function interfaces. We have a fast FFI with C and C++. 

Go can have pointers and in fact they can have interior pointers.

Binary contains the entire runtime. There is no JIT recompilation.

GCPercent - is a knob that adjusts how much CPU you want to use and how much memory you want to use.

MaxHeap - lets the programmer set what the maximum heap size should be.

If the GC sees memory pressure it informs the application that it should shed load. Once things are back to normal the GC informs the application that it can go back to its regular load.

The decision was to do a tri-color concurrent algorithm. We went with a size segregated span.

The write barrier is on only during the GC.

GC Pacer - is basically based on a feedback loop that determines when to best start a GC cycle. If the system is in a steady state and not in a phase change, marking will end just about the time memory runs out. Pacer slows down allocation while speeding up marking. When all of this is done the Pacer takes what it has learnt from this GC cycle as well as previous ones and projects when to start the next GC.


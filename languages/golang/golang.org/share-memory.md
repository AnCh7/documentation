### Share Memory By Communicating

> https://blog.golang.org/codelab-share
>
> https://golang.org/doc/codewalk/sharemem/

Traditional threading models (commonly used when writing Java, C++, and Python programs, for example) require the programmer to communicate between threads using shared memory. Typically, shared data structures are protected by locks, and threads will contend over those locks to access the data.

Go encourages the use of channels to pass references to data between goroutines.

> Do not communicate by sharing memory; instead, share memory by communicating.

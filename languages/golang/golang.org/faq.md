### FAQ

> References:
> https://golang.org/doc/faq

### What are Go's ancestors?

Go is mostly in the C family (basic syntax), with significant input from the Pascal/Modula/Oberon family (declarations, packages), plus some ideas from languages inspired by Tony Hoare's CSP, such as Newsqueak and Limbo (concurrency).

### Do Go programs link with C/C++ programs?

These are gc, the default compiler, **gccgo**, which uses the GCC back end, and a somewhat less mature **gollvm**, which uses the LLVM infrastructure.

### Does Go have a runtime? 

Go does have an extensive library, called the *runtime*, that is part of every Go program. The runtime library implements garbage collection, concurrency, stack management, and other critical features of the Go language. Although it is more central to the language, Go's runtime is analogous to `libc`, the C library.

Go's runtime does not include a virtual machine.

### How do I get dynamic dispatch of methods? 

The only way to have dynamically dispatched methods is through an interface. Methods on a struct or any other concrete type are always resolved statically.

### Why is there no type inheritance? 

A type automatically satisfies any interface that specifies a subset of its methods. Because there are no explicit relationships between types and interfaces, there is no type hierarchy to manage or discuss.

### How can I guarantee my type satisfies an interface? 

You can ask the compiler to check that the type `T` implements the interface `I` by attempting an assignment using the zero value for `T` or pointer to `T`, as appropriate:

```go
type T struct{}
var _ I = T{}       // Verify that T implements I.
var _ I = (*T)(nil) // Verify that *T implements I.
```

### Why is my nil error value not equal to nil? 

Under the covers, interfaces are implemented as two elements, a type `T` and a value `V`. For instance, if we store the `int` value 3 in an interface, the resulting interface value has, schematically, (`T=int`, `V=3`). The value `V` is also known as the interface's *dynamic* value.

An interface value is `nil` only if the `V` and `T` are both unset, (`T=nil`, `V` is not set).

If we store a `nil` pointer of type `*int` inside an interface value, the inner type will be `*int` regardless of the value of the pointer: (`T=*int`, `V=nil`).

### When are function parameters passed by value? 

Everything in Go is passed by value. For instance, passing an `int` value to a function makes a copy of the `int`, and passing a pointer value makes a copy of the pointer, but not the data it points to.

### When should I use a pointer to an interface? 

Almost never. Pointers to interface values arise only in rare, tricky situations involving disguising an interface value's type for delayed evaluation.

### Should I define methods on values or pointers? 

```go
func (s *MyStruct) pointerMethod() { } // method on pointer
func (s MyStruct)  valueMethod()   { } // method on value
```

If method needs to modify the receiver, the receiver *must* be a pointer.
In Java method receivers are always pointers.
If the receiver is large - use a pointer receiver. 
If some of the methods of the type must have pointer receivers, the rest should too.

### How do I know whether a variable is allocated on the heap or the stack? 

When possible, the Go compilers will allocate variables that are local to a function in that function's stack frame.  However, if the compiler cannot prove that the variable is not referenced after the function returns, then the compiler must allocate the variable on heap. Also, if a local variable is very large, it will be stored on the heap.

if a variable has its address taken, that variable is a candidate for allocation on the heap.


### Why do garbage collection?  Won't it be too expensive? 

The current implementation is a mark-and-sweep collector. If the machine is a multiprocessor, the collector runs on a separate CPU core in parallel with the main program.


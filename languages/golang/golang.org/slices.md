### Go Slices: usage and internals

> References:
>
> https://blog.golang.org/slices-intro

Slices are analogous to arrays in other languages, but have some unusual properties.

The slice type is an abstraction built on top of Go's array type. An array type definition specifies a length and an element type. An array's size is fixed; its length is part of its type. Arrays can be indexed in the usual way, so the expression `s[n]` accesses the nth element, starting from zero.

Arrays do not need to be initialized explicitly; the zero value of an array is a ready-to-use array whose elements are themselves zeroed.

Go's arrays are values. An array variable denotes the entire array; it is not a pointer to the first array element.

---

The type specification for a slice is `[]T`, where `T` is the type of the elements of the slice. Unlike an array type, a slice type has no specified length.

A slice can be created with the built-in function called `make`.

```go
func make([]T, len, cap) []T
```

The length and capacity of a slice can be inspected using the built-in `len` and `cap` functions. 

The zero value of a slice is `nil`. The `len` and `cap` functions will both return 0 for a nil slice.

A slice can also be formed by "slicing" an existing slice or array.

A slice is a descriptor of an array segment. It consists of a pointer to the array, the length of the segment, and its capacity (the maximum length of the segment).

  ![img](.slices-images/slice-struct.png)

The length is the number of elements referred to by the slice. The capacity is the number of elements in the underlying array (beginning at the element referred to by the slice pointer).

Slicing does not copy the slice's data. It creates a new slice value that points to the original array. This makes slice operations as efficient as manipulating array indices.

A slice cannot be grown beyond its capacity.

To increase the capacity of a slice one must create a new, larger slice and copy the contents of the original slice into it. The `copy` function supports copying between slices of different lengths. 

The `append` function appends the elements `x` to the end of the slice `s`, and grows the slice if a greater capacity is needed.

To append one slice to another, use `...` to expand the second argument to a list of arguments.

Since the zero value of a slice (`nil`) acts like a zero-length slice, you can declare a slice variable and then append to it in a loop.


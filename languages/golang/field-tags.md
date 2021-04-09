### Tags

A field declaration may be followed by an optional string literal *tag*, which becomes an attribute for all the fields in the corresponding field declaration. An empty tag string is equivalent to an absent tag. The tags are made visible through a [reflection interface](https://golang.org/pkg/reflect/#StructTag) and take part in [type identity](https://golang.org/ref/spec#Type_identity) for structs but are otherwise ignored.

A tag for a field allows you to attach meta-information to the field.

By convention the value of a tag string is a space-separated list of `key:"value"` pairs.

```golang
type User struct {
    Name string `json:"name" xml:"name"`
}
```

If multiple information is to be passed in the `"value"`, usually it is specified by separating it with a comma (`','`).

```golang
Name string `json:"name,omitempty" xml:"name"`
```

Usually a dash value (`'-'`) for the `"value"` means to exclude the field from the process.


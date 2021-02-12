# Jsonnet

> References: 
> https://jsonnet.org/learning/tutorial.html
> https://github.com/google/jsonnet
> https://jsonnet.org/articles/kubernetes.html



## Syntax

Any [JSON](https://json.org) document is a valid Jsonnet program, so we'll focus on what Jsonnet adds to JSON.  Let's start with an example that does not involve any computation but uses new syntax.
- Fields that happen to be valid identifiers have no quotes.
- *Trailing* commas at the end of arrays / objects.
- Comments.
- String literals use `"` or `'`.  The single quote is easier on the eyes but either can be used to avoid escaping the other, e.g. `"Farmer's Gin"` instead of `'Farmer\'s Gin'`.
-  Text blocks `|||` allow verbatim text across multiple lines. 
- Verbatim strings `@'foo'` and `@"foo"` are for single lines.

## Variables

Variables are the simplest way to avoid duplication. 
- The `local` keyword defines a variable.
- Variables defined next to fields end with a comma (`,`).
- All other cases end with a semicolon (`;`).

## References

Another way to avoid duplication is to refer to another part of the structure. 
- `self` refers to the current object.
- `$` refers to the outer-most object.
- `['foo']` looks up a field.
- `.f` can be used if the field name is an identifier.
- `[10]` looks up an array element.
- Arbitrarily long paths are allowed.
- Array slices like `arr[10:20:2]` are allowed, like in Python.
- Strings can be looked up / sliced too, by unicode codepoint.

## Arithmetic

Arithmetic includes numeric operations like multiplication but also various operations on other types.
- Use floating point arithmetic, bitwise ops, boolean logic.
- Strings may be concatenated with `+`, which implicitly converts one operand to string if needed.
- Two strings can be compared with `<` (unicode codepoint order).
- Objects may be combined with `+` where the right-hand side wins field conflicts.
- Test if a field is in an object with `in`.
- `==` is deep value equality.
- Python-compatible string formatting is available via `%`.  When combined with `|||` this can be used for templating text files.

## Functions

Like Python, functions have positional parameters, named parameters, and default arguments. Closures are also supported.  The examples below should demonstrate the syntax.  Many functions are already defined in the [standard library](https://jsonnet.org/ref/stdlib.html).

## Conditionals

Conditional expressions look like `if b then e else e`. The `else` branch is optional and defaults to `null`. 

## Computed Field Names

Jsonnet objects can be used like a `std::map` or similar datastructures from regular languages.
- Recall that a field lookup can be computed with `obj[e]`
- The definition equivalent is `{[e]: `... `}`
- `self` or object locals cannot be accessed when field names are being computed, since the object is not yet constructed
- If a field name evaluates to `null` during object construction, the field is omitted.  This works nicely with the default false branch of a conditional (see below).

## Array and Object Comprehension

What if you want to make an array or object and you don't know how many elements / fields they will have at run-time?  Jsonnet has Python-style array and object comprehension constructs to allow this. 
- Any nesting of `for` and `if` can be used.
- The nest behaves like a loop nest, although the body is written first.

## Imports

It's possible to import both code and raw data from other files.
- The `import` construct is like copy/pasting Jsonnet code.
- Files designed for import by convention end with `.libsonnet`
- Raw JSON can be imported this way too.
- The `importstr` construct is for verbatim UTF-8 text.

Usually, imported Jsonnet content is stashed in a top-level local variable.  This resembles the way other programming languages handle modules.  Jsonnet libraries typically return an object, so that they can easily be extended.  Neither of these conventions are enforced.

## Errors

Errors can arise from the language itself (e.g. an array overrun) or thrown from Jsonnet code.  Stack traces provide context for the error.
- To raise an error: `error "foo"`
- To assert a condition before an expression: `assert "foo";`
- A custom failure message: `assert "foo" : "message";`
- Assert fields have a property: `assert self.f == 10,`
- With custom failure message: `assert "foo" : "message",`

Jsonnet is a lazy  language, so variable initializers are not evaluated until the variable is used.

## Parameterize Entire Config

Jsonnet is hermetic: It always generates the same data no matter the execution environment. This is an important property but there are times when you want a few carefully chosen parameters at the top level.  There are two ways to do this, with different properties.
- **External variables**, which are accessible anywhere in the config, or any file, using `std.extVar("foo")`. 
- **Top-level arguments**, where the whole config is expressed as a function.

Any Jsonnet value can be bound to an external variable, even functions.
- `prefix` is bound to the string `"Happy Hour "`
- `brunch` is bound to `true`

### External variables

The values are configured when the Jsonnet virtual machine is initialized, by passing either
1) Jsonnet code (which evaluates to the value)
2) or a raw string. 
The latter is just a convenience, because escaping a string to pass it as Jsonnet code can be tedious.  To make this concrete, the above variables can be configured with the following commandline:

```bash
jsonnet --ext-str prefix="Happy Hour " \
        --ext-code brunch=true ...
```

### Top-level arguments

Alternatively the same code can be written using top-level arguments, where the whole config is written as a function.  How does this differ to using external variables?
- Values must be explicitly threaded through files
- Default values can be provided
- The config can be imported as a library and called as a function

Generally, top-level arguments are the safer and easier way to parameterize an entire config, because the variables are not global and it's clear what parts of the config are dependent on their environment.  However, they do require more explicit threading of the values into other imported code.  Here's the equivalent invocation of the Jsonnet command-line tool:
```bash
jsonnet --tla-str prefix="Happy Hour " \
        --tla-code brunch=true ...
```

## Object-Orientation

In general, object-orientation makes it easy to define many variants from a single "base". Unlike Java, C++ and Python, where classes extend other classes, in Jsonnet, objects extend other objects.  We have already discussed some of the raw ingredients for this, albeit in isolation:
- Objects (which we inherit from JSON).
- The object composition operator `+`, which merges two objects, choosing the right hand side when fields collide.
- The `self` keyword, a reference to the current object.

When these features are combined together and with the following *new* features, things get a lot more interesting:
- Hidden fields, defined with `::`, which do not appear in generated JSON.
- The `super` keyword, which has its usual meaning.
- The `+:` field syntax for overriding deeply nested fields.

The `+` operator is actually implicit in these examples. In the common case where you write `foo + { `...` }`, i.e. the `+` is immediately followed by a `{`, then the `+` can be elided. The key to making Jsonnet object-oriented is that the `self` keyword be `late bound`.
A `mixin` allows you to take some "overrides" and instead of just applying them to an existing object to get a new object, you   can pass them around as a first class value and apply them to any object you want.

## Formatter 

The formatter is a command-line utility bundled with the C++ build of Jsonnet.  You can reformat files in place as so:
```
jsonnet fmt -i *.jsonnet
```

## Using Jsonnet With Kubernetes

There are three ways to configure sets of Kubernetes objects
* Use YAML [stream](https://jsonnet.org/learning/getting_started.html#stream) output
* [multi-file](https://jsonnet.org/learning/getting_started.html#multi) output
* a single kubectl list object.

This latter option is provided by Kubernetes without requiring any special support from Jsonnet.  It allows us to group several Kubernetes objects into a single object.  This is the most popular option because it does not require intermediate files, and other tools don't always reliably support YAML streams.

## Custom Output Formats

The `INI format` provides a simple example.
- The content of the INI file has been represented within the JSON object model, i.e. using objects to represent the nesting of the sections.
- The special `main` section holds the top-level keys (ones not in any section).
- The function `std.manifestIni` (written in Jsonnet) serializes the structure to a string.
- Jsonnet is run in string output mode `-S` to print the string verbatim rather than encoded as JSON.

The code below could be executed with the command:
```
$ jsonnet -S ini.jsonnet
```

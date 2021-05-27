## Templates

> References:
>
> https://helm.sh/docs/chart_best_practices/templates/
>
> https://helm.sh/docs/chart_template_guide/
>
> https://pkg.go.dev/text/template



### Best practices

The `templates/` directory should be structured as follows:

- Template files should have the extension `.yaml` if they produce YAML output. The extension `.tpl` may be used for template files that produce no formatted content.
- Template file names should use dashed notation (`my-example-configmap.yaml`), not camelcase.
- Each resource definition should be in its own template file.
- Template file names should reflect the resource kind in the name. e.g. `foo-pod.yaml`, `bar-svc.yaml`

---

Defined templates (templates created inside a `{{ define }}` directive) are globally accessible. All defined template names should be namespaced.

---

Templates should be indented using *two spaces* (never tabs).

Template directives should have whitespace after the opening braces and before the closing braces:

```yaml
{{ .foo }}
{{ print "foo" }}
{{- print "bar" -}}
```

---

YAML is a superset of JSON. In some cases, using a JSON syntax can be more readable than other YAML representations.

### Chart template guide

Helm charts are structured like this:

```fallback
mychart/
  Chart.yaml
  values.yaml
  charts/
  templates/
  ...
```

When Helm evaluates a chart, it will send all of the files in the `templates/` directory through the template rendering engine. It then collects the results of those templates and sends them on to Kubernetes.

The `values.yaml` file is also important to templates. This file contains the *default values* for a chart. These values may be overridden by users during `helm install` or `helm upgrade`.

The `Chart.yaml` file contains a description of the chart.

Each file begins with `---` to indicate the start of a YAML document.

A template directive is enclosed in `{{` and `}}` blocks.

The template directive `{{ .Release.Name }}` injects the release name into the template. The values that are passed into a template can be thought of as *namespaced objects*, where a dot (`.`) separates each namespaced element.

The leading dot before `Release` indicates that we start with the top-most namespace for this scope.

---

Objects are passed into a template from the template engine.

`Release` is one of the top-level objects that you can access in your templates.

`Values`: Values passed into the template from the `values.yaml` file and from user-supplied files. By default, `Values` is empty.

`Chart`: The contents of the `Chart.yaml` file. Any data in `Chart.yaml` will be accessible here. 

`Files`: This provides access to all non-special files in a chart. While you cannot use it to access templates, you can use it to access other files in the chart. 

`Capabilities`: This provides information about what capabilities the Kubernetes cluster supports.

`Template`: Contains information about the current template that is being executed.

---

`Values` provides access to values passed into the chart. Its contents come from multiple sources:

- The `values.yaml` file in the chart
- If this is a subchart, the `values.yaml` file of a parent chart
- A values file if passed into `helm install` or `helm upgrade` with the `-f` flag (`helm install -f myvals.yaml ./mychart`)
- Individual parameters passed with `--set` (such as `helm install --set foo=bar ./mychart`)

Values files are plain YAML files.

---

Helm has over 60 available functions. Some of them are defined by the [Go template language](https://godoc.org/text/template) itself. Most of the others are part of the [Sprig template library](https://masterminds.github.io/sprig/).

---

Drawing on a concept from UNIX, `pipelines` are a tool for chaining  together a series of template commands to compactly express a series of  transformations.

```yaml
drink: {{ .Values.favorite.drink | quote }}
```

---

`Default`function allows you to specify a default value inside of the  template, in case the value is omitted. Let’s use it to modify the drink example above:

```yaml
drink: {{ .Values.favorite.drink | default "tea" | quote }}
```

---

The `lookup` function can be used to *look up* resources in a running cluster. The synopsis of the lookup function is `lookup apiVersion, kind, namespace, name -> resource or resource list`.

| Behavior                               | Lookup function                           |
| -------------------------------------- | ----------------------------------------- |
| `kubectl get pod mypod -n mynamespace` | `lookup "v1" "Pod" "mynamespace" "mypod"` |

When `lookup` returns an object, it will return a dictionary.  When `lookup` returns a list of objects, it is possible to access the object list via the `items` field.

Keep in mind that Helm is not supposed to contact the Kubernetes API Server during a `helm template` or a `helm install|update|delete|rollback --dry-run`, so the `lookup` function will return an empty list (i.e. dict) in such a case.

---

Operators are implemented as functions that return a boolean value. To use `eq`, `ne`, `lt`, `gt`, `and`, `or`, `not` etcetera place the operator at the front of the statement followed by  its parameters just as you would a function. To chain multiple  operations together, separate individual functions by surrounding them  with parentheses.

---

The basic structure for a conditional `if`/`else` block:

```yaml
{{ if PIPELINE }}
  # Do something
{{ else if OTHER PIPELINE }}
  # Do something else
{{ else }}
  # Default case
{{ end }}
```

A pipeline is evaluated as *false* if the value is:

- a boolean false
- a numeric zero
- an empty string
- a `nil` (empty or null)
- an empty collection (`map`, `slice`, `tuple`, `dict`, `array`)

`{{-` (with the dash and space added) indicates that whitespace should be chomped left, while `-}}` means whitespace to the right should be consumed. 

---

`with` actin controls variable scoping. For example, `.Values` tells the template to find the `Values` object in the current scope.

```yaml
{{ with PIPELINE }}
  # restricted scope
{{ end }}
```

----

`range` the way to iterate through a collection. We can use `$` for accessing the list `Values.pizzaToppings` from the parent scope. `$` is mapped to the root scope when template execution begins and it does not change during template execution.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
data:
  myvalue: "Hello World"
  {{- with .Values.favorite }}
  drink: {{ .drink | default "tea" | quote }}
  food: {{ .food | upper | quote }}
  toppings: |-
    {{- range $.Values.pizzaToppings }}
    - {{ . | title | quote }}
    {{- end }}
  {{- end }}
```

The `|-` marker in YAML takes a multi-line string. This can  be a useful technique for embedding big blocks of data inside of your  manifests, as exemplified here.

---

In Helm templates, a variable is a named reference to another object. It follows the form `$name`. Variables are assigned with a special assignment operator: `:=`. They are scoped to the block in which they are declared. 

Variables are particularly useful in `range` loops. They can be used on list-like objects to capture both the index and the value:

```yaml
  toppings: |-
    {{- range $index, $topping := .Values.pizzaToppings }}
      {{ $index }}: {{ $topping }}
    {{- end }}
```

Global - `$` - this variable will always point to the root context.

---

A `named template` (sometimes called a partial or a subtemplate) is simply a template defined inside of a file, and given a name. Template names are global.

Most files in `templates/` are treated as if they contain Kubernetes manifests. (except `NOTES.txt`). Also files with name begins with an underscore (`_`) are assumed to not have a manifest inside. These files are not rendered to Kubernetes  object definitions, but are available everywhere within other chart  templates for use. These files are used to store partials and helpers. 

The `define` action allows us to create a named template inside of a template file:

```yaml
{{ define "MY.NAME" }}
  # body of template here
{{ end }}
```

`template` will render that template inline.

**Template names are global**. As a result of this, if two templates are declared with the same name the last occurrence will be the one that is used.

We can pass a scope to the template. Note that we pass `.` at the end of the `template` call.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
  {{- template "mychart.labels" . }}
```

---

It is considered preferable to use `include` over `template` in Helm templates simply so that the output formatting can be handled better for YAML documents.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: {{ .Release.Name }}-configmap
  labels:
    {{- include "mychart.app" . | nindent 4 }}
```

---

Charts must be smaller than 1M because of the storage limitations of Kubernetes objects.

Some files cannot be accessed through the `.Files` object, usually for security reasons.

- Files in `templates/` cannot be accessed.
- Files excluded using `.helmignore` cannot be accessed.
- Charts do not preserve UNIX mode information, so file-level permissions will have no impact on the availability of a file when it comes to the `.Files` object.

Helm provides access to files through the `.Files` object.

```yaml
data:
  {{- $files := .Files }}
  {{- range tuple "config1.toml" "config2.toml" "config3.toml" }}
  {{ . }}: |-
    {{ $files.Get . }}
  {{- end }}
```

Path helpers:

- Base
- Dir
- Ext
- IsAbs
- Clean
- `{{ (.Files.Glob "bar/*").AsSecrets | indent 2 }}`
- `{{ .Files.Get "config1.toml" | b64enc }}`
- `{{ range .Files.Lines "foo/bar.txt" }}`

`.Glob` returns a `Files` type, so you may call any of the `Files` methods on the returned object.

There is no way to pass files external to the chart during `helm install`. So if you are asking users to supply data, it must be loaded using `helm install -f` or `helm install --set`.

---

Charts can have dependencies, called subcharts, that also have their own values and templates.

1. A subchart is considered "stand-alone", which means a subchart can never explicitly depend on its parent chart.
2. For that reason, a subchart cannot access the values of its parent.
3. A parent chart can override values for subcharts.
4. Helm has a concept of *global values* that can be accessed by all charts.

We can modify subchart values:

```yaml
favorite:
  drink: coffee
  food: pizza
pizzaToppings:
  - mushrooms
  - cheese
  - peppers
  - onions

mysubchart:
  dessert: ice cream
```

Global values are values that can be accessed from any chart or subchart by exactly the same name. Globals require explicit declaration. You can't use an existing non-global as if it were a global.

```yaml
global:
  salad: caesar
```

Parent charts and subcharts can share templates. Any defined block in any chart is available to other charts:

```yaml
{{- define "labels" }}from: mychart{{ end }}
```

---

The `.helmignore` file is used to specify files you don't want to include in your helm chart.

The `.helmignore` file supports Unix shell glob matching, relative path matching, and negation (prefixed with !). Only one pattern per line is considered.

---

There are a few commands that can help you debug:

- `helm lint` verifying that your chart follows best practices
- `helm install --dry-run --debug` or `helm template --debug`: render your templates, then return the resulting manifest file.
- `helm get manifest`: see what templates are installed on the server.

---

The two types of collections are maps and sequences:

```yaml
map:
  one: 1
  two: 2
  three: 3

sequence:
  - one
  - two
  - three
```

If an integer or float is an unquoted bare word, it is typically treated as a numeric type:

```yaml
count: 1
size: 2.34
```

But if they are quoted, they are treated as strings:

```yaml
count: "1" # <-- string, not int
size: '2.34' # <-- string, not float
```

The same is true of booleans.

The word for an empty value is `null` (not `nil`).

You can force a particular type inference using YAML node tags:

```yaml
coffee: "yes, please"
age: !!str 21
port: !!int "80"
```

There are three "inline" ways of declaring a string:

```yaml
way1: bare words
way2: "double-quoted strings"
way3: 'single-quoted strings'
```

All inline styles must be on one line.

- Bare words are unquoted, and are not escaped. For this reason, you have to be careful what characters you use.
- Double-quoted strings can have specific characters escaped with `\`. For example `"\"Hello\", she said"`. You can escape line breaks with `\n`.
- Single-quoted strings are "literal" strings, and do not use the `\` to escape characters. The only escape sequence is `''`, which is decoded as a single `'`.

In addition to the one-line strings, you can declare multi-line strings:

```yaml
coffee: |
  Latte
  Cappuccino
  Espresso
```

Strip off the trailing newline, add a `-` after the `|`:

```yaml
coffee: |-
  Latte
  Cappuccino
  Espresso
```

Preserve all trailing whitespace with the `|+` notation:

```yaml
coffee: |+
  Latte
  Cappuccino
  Espresso

another: value
```

To declare a folded block, use `>` instead of `|`:

```yaml
coffee: >
  Latte
  Cappuccino
  Espresso
```

It is possible to place more than one YAML documents into a single file. This is done by prefixing a new document with `---` and ending the document with `...`

```yaml
---
document:1
...
---
document: 2
...
```

In many cases, either the `---` or the `...` may be omitted.

Because YAML is a superset of JSON, any valid JSON document *should* be valid YAML.

---

Variables in templates are *typed*:

- string: A string of text
- bool: a `true` or `false`
- int: An integer value (there are also 8, 16, 32, and 64 bit signed and unsigned variants of this)
- float64: a 64-bit floating point value (there are also 8, 16, and 32 bit varieties of this)
- a byte slice (`[]byte`), often used to hold (potentially) binary data
- struct: an object with properties and methods
- a slice (indexed list) of one of the previous types
- a string-keyed map (`map[string]interface{}`) where the value is one of the previous types

The easiest way to debug an object's type is to pass it through `printf "%t"` in a template, which will print the type.

### pkg.go.dev/text/template

Templates are executed by applying them to a data structure. Execution of the template walks the structure and sets the cursor, represented by a period '.' and called "dot", to the value at the current location in the structure as execution proceeds.

By default, all text between actions is copied verbatim when the template is executed.

If an action's left delimiter (by default "{{") is followed immediately by a minus sign and white space, all trailing white space is trimmed.

Here is the list of actions:

```yaml
{{/* a comment */}}
{{- /* a comment with white space trimmed from preceding and following text */ -}}
	A comment; discarded. May contain newlines.
	Comments do not nest and must start and end at the
	delimiters, as shown here.

{{pipeline}}
	The default textual representation (the same as would be
	printed by fmt.Print) of the value of the pipeline is copied
	to the output.

{{if pipeline}} T1 {{end}}
	If the value of the pipeline is empty, no output is generated;
	otherwise, T1 is executed. The empty values are false, 0, any
	nil pointer or interface value, and any array, slice, map, or
	string of length zero.
	Dot is unaffected.

{{if pipeline}} T1 {{else}} T0 {{end}}
	If the value of the pipeline is empty, T0 is executed;
	otherwise, T1 is executed. Dot is unaffected.

{{if pipeline}} T1 {{else if pipeline}} T0 {{end}}
	To simplify the appearance of if-else chains, the else action
	of an if may include another if directly; the effect is exactly
	the same as writing
		{{if pipeline}} T1 {{else}}{{if pipeline}} T0 {{end}}{{end}}

{{range pipeline}} T1 {{end}}
	The value of the pipeline must be an array, slice, map, or channel.
	If the value of the pipeline has length zero, nothing is output;
	otherwise, dot is set to the successive elements of the array,
	slice, or map and T1 is executed. If the value is a map and the
	keys are of basic type with a defined order, the elements will be
	visited in sorted key order.

{{range pipeline}} T1 {{else}} T0 {{end}}
	The value of the pipeline must be an array, slice, map, or channel.
	If the value of the pipeline has length zero, dot is unaffected and
	T0 is executed; otherwise, dot is set to the successive elements
	of the array, slice, or map and T1 is executed.

{{template "name"}}
	The template with the specified name is executed with nil data.

{{template "name" pipeline}}
	The template with the specified name is executed with dot set
	to the value of the pipeline.

{{block "name" pipeline}} T1 {{end}}
	A block is shorthand for defining a template
		{{define "name"}} T1 {{end}}
	and then executing it in place
		{{template "name" pipeline}}
	The typical use is to define a set of root templates that are
	then customized by redefining the block templates within.

{{with pipeline}} T1 {{end}}
	If the value of the pipeline is empty, no output is generated;
	otherwise, dot is set to the value of the pipeline and T1 is
	executed.

{{with pipeline}} T1 {{else}} T0 {{end}}
	If the value of the pipeline is empty, dot is unaffected and T0
	is executed; otherwise, dot is set to the value of the pipeline
	and T1 is executed.
```

An argument is a simple value, denoted by one of the following.

```
- A boolean, string, character, integer, floating-point, imaginary
  or complex constant in Go syntax. These behave like Go's untyped
  constants. Note that, as in Go, whether a large integer constant
  overflows when assigned or passed to a function can depend on whether
  the host machine's ints are 32 or 64 bits.
- The keyword nil, representing an untyped Go nil.
- The character '.' (period):
	.
  The result is the value of dot.
- A variable name, which is a (possibly empty) alphanumeric string
  preceded by a dollar sign, such as
	$piOver2
  or
	$
  The result is the value of the variable.
  Variables are described below.
- The name of a field of the data, which must be a struct, preceded
  by a period, such as
	.Field
  The result is the value of the field. Field invocations may be
  chained:
    .Field1.Field2
  Fields can also be evaluated on variables, including chaining:
    $x.Field1.Field2
- The name of a key of the data, which must be a map, preceded
  by a period, such as
	.Key
  The result is the map element value indexed by the key.
  Key invocations may be chained and combined with fields to any
  depth:
    .Field1.Key1.Field2.Key2
  Although the key must be an alphanumeric identifier, unlike with
  field names they do not need to start with an upper case letter.
  Keys can also be evaluated on variables, including chaining:
    $x.key1.key2
- The name of a niladic method of the data, preceded by a period,
  such as
	.Method
  The result is the value of invoking the method with dot as the
  receiver, dot.Method(). Such a method must have one return value (of
  any type) or two return values, the second of which is an error.
  If it has two and the returned error is non-nil, execution terminates
  and an error is returned to the caller as the value of Execute.
  Method invocations may be chained and combined with fields and keys
  to any depth:
    .Field1.Key1.Method1.Field2.Key2.Method2
  Methods can also be evaluated on variables, including chaining:
    $x.Method1.Field
- The name of a niladic function, such as
	fun
  The result is the value of invoking the function, fun(). The return
  types and values behave as in methods. Functions and function
  names are described below.
- A parenthesized instance of one the above, for grouping. The result
  may be accessed by a field or map key invocation.
	print (.F1 arg1) (.F2 arg2)
	(.StructValuedMethod "arg").Field
```

---

A pipeline is a possibly chained sequence of "commands".

---

A pipeline inside an action may initialize a variable to capture the result. The initialization has syntax

```
$variable := pipeline
```

Variables previously declared can also be assigned, using the syntax

```
$variable = pipeline
```

A variable's scope extends to the "end" action of the control structure ("if", "with", or "range") in which it is declared, or to the end of the template if there is no such control structure. 

---

Predefined global functions are named as follows.

```
and
	Returns the boolean AND of its arguments by returning the
	first empty argument or the last argument, that is,
	"and x y" behaves as "if x then y else x". All the
	arguments are evaluated.
call
	Returns the result of calling the first argument, which
	must be a function, with the remaining arguments as parameters.
	Thus "call .X.Y 1 2" is, in Go notation, dot.X.Y(1, 2) where
	Y is a func-valued field, map entry, or the like.
	The first argument must be the result of an evaluation
	that yields a value of function type (as distinct from
	a predefined function such as print). The function must
	return either one or two result values, the second of which
	is of type error. If the arguments don't match the function
	or the returned error value is non-nil, execution stops.
html
	Returns the escaped HTML equivalent of the textual
	representation of its arguments. This function is unavailable
	in html/template, with a few exceptions.
index
	Returns the result of indexing its first argument by the
	following arguments. Thus "index x 1 2 3" is, in Go syntax,
	x[1][2][3]. Each indexed item must be a map, slice, or array.
slice
	slice returns the result of slicing its first argument by the
	remaining arguments. Thus "slice x 1 2" is, in Go syntax, x[1:2],
	while "slice x" is x[:], "slice x 1" is x[1:], and "slice x 1 2 3"
	is x[1:2:3]. The first argument must be a string, slice, or array.
js
	Returns the escaped JavaScript equivalent of the textual
	representation of its arguments.
len
	Returns the integer length of its argument.
not
	Returns the boolean negation of its single argument.
or
	Returns the boolean OR of its arguments by returning the
	first non-empty argument or the last argument, that is,
	"or x y" behaves as "if x then x else y". All the
	arguments are evaluated.
print
	An alias for fmt.Sprint
printf
	An alias for fmt.Sprintf
println
	An alias for fmt.Sprintln
urlquery
	Returns the escaped value of the textual representation of
	its arguments in a form suitable for embedding in a URL query.
	This function is unavailable in html/template, with a few
	exceptions.
```

The boolean functions take any zero value to be false and a non-zero value to be true.

There is also a set of binary comparison operators defined as functions:

```
eq
	Returns the boolean truth of arg1 == arg2
ne
	Returns the boolean truth of arg1 != arg2
lt
	Returns the boolean truth of arg1 < arg2
le
	Returns the boolean truth of arg1 <= arg2
gt
	Returns the boolean truth of arg1 > arg2
ge
	Returns the boolean truth of arg1 >= arg2
```

---

When parsing a template, another template may be defined and associated with the template being parsed. Template definitions must appear at the top level of the template, much like global variables in a Go program.

```
`{{define "T1"}}ONE{{end}}
{{define "T2"}}TWO{{end}}
{{define "T3"}}{{template "T1"}} {{template "T2"}}{{end}}
{{template "T3"}}`
```


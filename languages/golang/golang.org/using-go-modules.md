### Using go modules

> References:
> https://blog.golang.org/using-go-modules
> https://blog.golang.org/migrating-to-go-modules
> https://blog.golang.org/publishing-go-modules
> https://blog.golang.org/v2-go-modules
> https://blog.golang.org/module-compatibility
> https://golang.org/doc/modules/managing-dependencies



A module is a collection of [Go packages](https://golang.org/ref/spec#Packages) stored in a file tree with a `go.mod` file at its root. The `go.mod` file defines the module’s *module path*, which is also the import path used for the root directory, and its *dependency requirements*, which are the other modules needed for a successful build. Each dependency requirement is written as a module path and a specific [semantic version](http://semver.org/).

The `go.mod` file only appears in the root of the module. Packages in subdirectories have import paths consisting of the module path plus the path to the subdirectory.

In the `go list` output, the current module, also known as the *main module*, is always the first line, followed by dependencies sorted by module path.

---

The `golang.org/x/text` version `v0.0.0-20170915032832-14c0d48ead0c` is an example of a [pseudo-version](https://golang.org/cmd/go/#hdr-Pseudo_versions), which is the `go` command's version syntax for a specific untagged commit.

In addition to `go.mod`, the `go` command maintains a file named `go.sum` containing the expected [cryptographic hashes](https://golang.org/cmd/go/#hdr-Module_downloading_and_verification) of the content of specific module versions.

---

Both `go.mod` and `go.sum` should be checked into version control.

---

Upgrading dependencies:

```
go list -m -versions rsc.io/sampler
rsc.io/sampler v1.0.0 v1.2.0 v1.2.1 v1.3.0 v1.3.1 v1.99.99

go get rsc.io/sampler@v1.3.1
```

---

Each different major version (`v1`, `v2`, and so on) of a Go module uses a different module path: starting at `v2`, the path must end in the major version. In the example, `v3` of `rsc.io/quote` is no longer `rsc.io/quote`: instead, it is identified by the module path `rsc.io/quote/v3`. This convention is called [semantic import versioning](https://research.swtch.com/vgo-import), and it gives incompatible packages (those with different major versions) different names.

The `go` command allows a build to include at most one version of any particular module path, meaning at most one of each major version: one `rsc.io/quote`, one `rsc.io/quote/v2`, one `rsc.io/quote/v3`, and so on. It is impossible for a program to build with both `rsc.io/quote v1.5.2` and `rsc.io/quote v1.6.0`.

At the same time, allowing different major versions of a module (because they have different paths) gives module consumers the ability to upgrade to a new major version incrementally.

---

The `go mod tidy` command cleans up unused dependencies.

---

To convert a project that already uses a dependency management tool, run the following commands:

```
go mod init github.com/my/project
```

`go mod init` creates a new go.mod file and automatically imports dependencies from `Godeps.json`, `Gopkg.lock`, or a number of [other supported formats](https://go.googlesource.com/go/+/362625209b6cd2bc059b6b0a67712ddebab312d9/src/cmd/go/internal/modconv/modconv.go#9). The argument to `go mod init` is the module path, the location where the module may be found.

---

For a Go project without a dependency management system, run `go mod tidy` to add the module's dependencies.

---

Every required module in a `go.mod` has a [semantic version](https://semver.org), the minimum version of that dependency to use to build the module.

A semantic version has the form `vMAJOR.MINOR.PATCH`.

- Increment the `MAJOR` version when you make a [backwards incompatible](https://golang.org/doc/go1compat) change to the public API of your module. This should only be done when absolutely necessary.
- Increment the `MINOR` version when you make a backwards compatible change to the API, like changing dependencies or adding a new function, method, struct field, or type.
- Increment the `PATCH` version after making minor changes that don't affect your module's public API or dependencies, like fixing a bug.

You can specify pre-release versions by appending a hyphen and dot separated identifiers (for example, `v1.0.1-alpha` or `v2.2.2-beta.2`). `v0` major versions and pre-release versions do not guarantee backwards compatibility.

The version referenced in a `go.mod` may be an explicit release tagged in the repository (for example, `v1.5.2`), or it may be a [pseudo-version](https://golang.org/cmd/go/#hdr-Pseudo_versions) based on a specific commit (for example, `v0.0.0-20170915032832-14c0d48ead0c`). Pseudo-versions are a special type of pre-release version.

Do not delete version tags from your repo. If you find a bug or a security issue with a version, release a new version. If people depend on a version that you have deleted, their builds may fail. Similarly, once you release a version, do not change or overwrite it. The [module mirror and checksum database](https://blog.golang.org/module-mirror-launch) store modules, their versions, and signed cryptographic hashes to ensure that the build of a given version remains reproducible over time.

---

A `v0` version does not make any stability guarantees, so nearly all projects should start with `v0` as they refine their public API.

```bash
git tag v0.1.0
git push origin v0.1.0
```

---

A `v1` major version communicates to users that no incompatible changes will be made to the module's API. They can upgrade to new `v1` minor and patch releases, and their code should not break. Function and method signatures will not change, exported types will not be removed, and so on. If there are changes to the API, they will be backwards compatible and will be included in a new minor release. If there are bug fixes, they will be included in a patch release.

Sometimes, maintaining backwards compatibility can lead to awkward APIs. That's OK. An imperfect API is better than breaking users' existing code.

---

Modules formalized an important principle in Go, the [**import compatibility rule**](https://research.swtch.com/vgo-import):

```
If an old package and a new package have the same import path,
the new package must be backwards compatible with the old package.
```

The need for major version suffixes is one of the ways Go modules differs from most other dependency management systems.

---

The recommended strategy is to develop `v2+` modules in a directory named after the major version suffix.

---

Call compatibility is not enough for backward compatibility. There is,  in fact, no backward-compatible change you can make to a function’s  signature.

Instead of changing a function’s signature, add a new function.

If you anticipate that a function may need more arguments in the future, you can plan ahead by making optional arguments a part of the  function’s signature. The simplest way to do that is to add a single  struct argument.

Sometimes the techniques of adding a new function and adding options can be combined by making the options struct a method receiver.

```go
func Listen(network, address string) (Listener, error)

type ListenConfig struct {
    Control func(network, address string, c syscall.RawConn) error
}

func (*ListenConfig) Listen(ctx context.Context, network, address string) (Listener, error)
```

Another way to provide new options in the future is the “Option types”  pattern, where options are passed as variadic arguments, and each option is a function that changes the state of the value being constructed.

```go
grpc.Dial("some-target",
  grpc.WithAuthority("some-authority"),
  grpc.WithMaxDelay(time.Second),
  grpc.WithBlock())
```

---

Working with interfaces - the basic idea is to define a new interface with the new method, and  then wherever the old interface is used, dynamically check whether the  provided type is the older type or the newer type.

```go
func (r *Reader) Read(b []byte) (int, error) {
  if rs, ok := r.r.(io.Seeker); ok {
    // Use more efficient rs.Seek.
  }
  // Use less efficient r.r.Read.
}
```

When designing constructors prefer to return concrete  types. Working with concrete types allows you to add methods in the  future without breaking users, unlike interfaces.

---

If you have an exported struct type, you can almost always add a field  or remove an unexported field without breaking compatibility.

To keep a struct comparable, don’t add non-comparable fields to it. You can write a test for that, or rely on the upcoming [gorelease](https://pkg.go.dev/golang.org/x/exp/cmd/gorelease?tab=doc) tool to catch it.
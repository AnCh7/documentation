### Understanding Golang Project Structures

> References:
>
> https://raygervais.dev/articles/2020/11/understanding-golang-project-structure/



```bash
/build
/cmd
/docs
/internal
/pkg
/vendor
```



### /cmd

- Contains the `main.go`, which is the entrypoint of your application.
- Should be followed directly with your binary name, for example `/cmd/example/main.go`
- Should contain only main execution code, with all logic being imported from `/pkg` or `/internal`.

### /internal

- For application configurations and libraries which you don’t want imported by other applications. *NOTE*, this is enforced by the compiler as of Go 1.4.
- For sanity, you could extend `/internal` to emulate the appropriate file structure (ex. `/internal/pkg`) where appropriate.

### /pkg

- Shared library code, which can be imported by both your application and other applications.
- One of the most common layout patterns for bigger applications, for smaller applications this may be considered overkill.

### /vendor

- Manually managed application dependencies (such as environment binaries) are stored here.

### /build

- Contains all container (ex. `Dockerfile`), packages (`.deb`, `.rpm`, `.zip`) and scripts into `/build/packages`.
- CI configurations and scripts get placed in `/build/ci`.

### /docs

- Contains both generated documentation via `godoc` and user design documents.
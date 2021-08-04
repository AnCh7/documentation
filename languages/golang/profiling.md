## Profiling Go Programs

> References:
> https://blog.intelligentbee.com/2017/08/01/profiling-web-applications-golang/
> https://blog.golang.org/pprof


In order to use the profiling tool, you need to import the `net/http/pprof` package and register routes.

```go
func main() {
	// Create a new HTTP multiplexer
	mux := http.NewServeMux()
 
	// Register our handler for the / route
	mux.HandleFunc("/", handler)
 
	// Add the pprof routes
	mux.HandleFunc("/debug/pprof/", pprof.Index)
	mux.HandleFunc("/debug/pprof/cmdline", pprof.Cmdline)
	mux.HandleFunc("/debug/pprof/profile", pprof.Profile)
	mux.HandleFunc("/debug/pprof/symbol", pprof.Symbol)
	mux.HandleFunc("/debug/pprof/trace", pprof.Trace)
 
	mux.Handle("/debug/pprof/block", pprof.Handler("block"))
	mux.Handle("/debug/pprof/goroutine", pprof.Handler("goroutine"))
	mux.Handle("/debug/pprof/heap", pprof.Handler("heap"))
	mux.Handle("/debug/pprof/threadcreate", pprof.Handler("threadcreate"))
 
	// Start listening on port 8080
	if err := http.ListenAndServe(":8080", mux); err != nil {
		log.Fatal(fmt.Sprintf("Error when starting or running http server: %v", err))
	}
}
```

So you will need to run the profiling tool:

`go tool pprof -seconds 30 myserver http://localhost:8080/debug/pprof/profile`

While that’s running, run the benchmark:

`ab -k -c 8 -n 100000 "http://127.0.0.1:8080/"`

You can run commands to show you how much of CPU time each function took and other useful information, for example `top5`, `web`, `list`.

Profile memory:

`go tool pprof -alloc_objects myserver http://localhost:8080/debug/pprof/heap`


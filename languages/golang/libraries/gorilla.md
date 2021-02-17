## gorilla/mux

> References:
>
> https://github.com/gorilla/mux



The name mux stands for "HTTP request multiplexer". 

The main features are:

- It implements the `http.Handler` interface so it is compatible with the standard `http.ServeMux`.
- Requests can be matched based on URL host, path, path prefix,  schemes, header and query values, HTTP methods or using custom matchers.
- URL hosts, paths and query values can have variables with an optional regular expression.
- Registered URLs can be built, or "reversed", which helps maintaining references to resources.
- Routes can be used as subrouters: nested routes are only tested if  the parent route matches. This is useful to define groups of routes that share common conditions like a host, a path prefix or other repeated  attributes.

Paths can have variables:

```go
r.HandleFunc("/articles/{category}/{id:[0-9]+}", ArticleHandler)

func ArticlesCategoryHandler(w http.ResponseWriter, r *http.Request) {
    vars := mux.Vars(r)
    w.WriteHeader(http.StatusOK)
    fmt.Fprintf(w, "Category: %v\n", vars["category"])
}
```

Matching Routes

```go
r := mux.NewRouter()
r.Host("www.example.com")
r.Host("{subdomain:[a-z]+}.example.com")
r.PathPrefix("/products/")
r.Methods("GET", "POST")
r.Schemes("https")
r.Headers("X-Requested-With", "XMLHttpRequest")
r.Queries("key", "value")
```

Subrouting

```go
r := mux.NewRouter()
s := r.Host("www.example.com").Subrouter()
s.HandleFunc("/products/", ProductsHandler)
```

Registered URLs

```go
r := mux.NewRouter()
r.HandleFunc("/articles/{category}/{id:[0-9]+}", ArticleHandler).
  Name("article")
url, err := r.Get("article").URL("category", "technology", "id", "42")
```

Middleware

```go
func loggingMiddleware(next http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        log.Println(r.RequestURI)
        next.ServeHTTP(w, r)
    })
}

r := mux.NewRouter()
r.HandleFunc("/", handler)
r.Use(loggingMiddleware)
```

Testing Handlers

```go

func TestHealthCheckHandler(t *testing.T) {
    req, err := http.NewRequest("GET", "/health", nil)
    if err != nil {
        t.Fatal(err)
    }

    rr := httptest.NewRecorder()
    handler := http.HandlerFunc(HealthCheckHandler)

    handler.ServeHTTP(rr, req)

    if status := rr.Code; status != http.StatusOK {
        t.Errorf("handler returned wrong status code: got %v want %v",
            status, http.StatusOK)
    }

    expected := `{"alive": true}`
    if rr.Body.String() != expected {
        t.Errorf("handler returned unexpected body: got %v want %v",
            rr.Body.String(), expected)
    }
}
```


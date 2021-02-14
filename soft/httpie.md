### HTTPIE

> References:
>
> https://github.com/httpie/httpie

```bash
http PUT pie.dev/put X-API-Token:123 name=John

# output options:
http -v pie.dev/get

# redirected input:
http pie.dev/post < files/data.json

# redirected output:
http pie.dev/image/png > image.png
```


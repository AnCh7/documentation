# Squid

> References: 
> [proxy-server](../devops/proxy-server.md)



[Squid](http://www.squid-cache.org/) is a caching proxy for the Web supporting HTTP, HTTPS, FTP, and more. It reduces bandwidth and improves response times by caching and reusing  frequently-requested web pages. Squid has extensive access controls and  makes a great server accelerator.

When a browser comes across an https:// URL, it does one of two things: 
- opens an SSL/TLS connection directly to the origin server or 
- opens a TCP tunnel through Squid to the origin server using the CONNECT request method. 

The CONNECT method is a way to tunnel any kind of connection through an HTTP proxy. By default, the proxy establishes a TCP connection to the specified  server, responds with an HTTP 200 (Connection Established) response, and then shovels packets back and forth between the client and the server,  without understanding or interpreting the tunnelled traffic.

When a browser establishes a CONNECT tunnel through Squid, [Access Controls](https://wiki.squid-cache.org/SquidFaq/SquidAcl) are able to control CONNECT requests, but only limited information is  available. For example, many common parts of the request URL do not  exist in a CONNECT request: 

- the URL scheme or protocol (e.g., http://, https://, ftp://, voip://, itunes://, or telnet://), 
- the URL path (e.g., `/index.html` or `/secure/images/`), 
- and query string (e.g. `?a=b&c=d`) 

With HTTPS, the above parts are present in encapsulated HTTP requests that flow through the tunnel, but Squid does not have  access to those encrypted messages. Other tunnelled protocols may not even use HTTP messages and URLs (e.g., telnet).

Squid [SslBump](https://wiki.squid-cache.org/Features/SslBump) and associated features can be used to decrypt HTTPS CONNECT tunnels  while they pass through a Squid proxy. This allows dealing with  tunnelled HTTP messages as if they were regular HTTP messages, including applying detailed access controls and performing content adaptation  (e.g., check request bodies for information leaks and check responses  for viruses).

The config file consists of directives. They do 3 things:

1. Who and how can access the proxy, and what they can access:
   - `acl`: define access control lists (source, destination, protocol etc.)
   - `http_access`: control access for ACLs (checked in order, first rejection rejects request)
2. How the proxy can be reached and what it actually does to incoming requests:
   - `http_port, https_port`: where the proxy listens
   - `ssl_bump`: splice, peek and bump (intercept/inspect) some SSL connections
   - `cache_peer`: forward some requests to another (caching) proxy
3. Misc I/O, caching & debugging stuff:
   - `logformat, access_log`: specify logging
   - `refresh_pattern, cache_dir`: configure caching
   - `debug_options`: additional debug logging
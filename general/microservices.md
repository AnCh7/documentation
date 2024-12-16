## Microservices

```
graph TD

subgraph Client
        web
        mobile
        pc
    end

Client -->|Checks first| CDN-CloudFlare
Client --> LB(LoadBalancer-NGINX) --> APIGateway

APIGateway --> ID(IdentityProvider)

APIGateway --> Kafka

subgraph Microservices
    subgraph ProductService
        API1
        API2 
    end
    API2 --> Database(DB)

    subgraph ShippingService
        API3
    end

    API3 --> Database(DB)
    end

APIGateway --> ProductService
APIGateway --> ShippingService
APIGateway --> ServiceRegistry

ServiceRegistry -->|Manages| Microservices
```

1. Client: The client-side component includes various platforms like web, mobile, and PC. These platforms initiate requests to the system.
2. CDN (Content Delivery Network): The CDN helps deliver content to clients efficiently. The client checks the CDN first to retrieve static content.
3. LoadBalancer (LB): The LoadBalancer distributes incoming requests from the client to the appropriate services within the system.
4. APIGateway: The APIGateway is a crucial component responsible for routing and managing requests to various microservices.
5. IdentityProvider (ID): The IdentityProvider handles user authentication and authorization, ensuring secure access to the system.
6. Kafka: Kafka is a message broker that can be used for asynchronous communication or event streaming between microservices.
7. Microservices: The diagram shows two main microservice groups: "ProductService" and "ShippingService." Each group contains multiple APIs and connects to their respective databases. These microservices are part of the core processing units of the system.
8. ServiceRegistry: The ServiceRegistry serves as a central repository for service discovery and registration, helping microservices locate and communicate with each other.

##### An async microservice:

1. Does not make a request to other microservices while processing requests or
2. makes a request to other microservices while processing requests and does not wait for the result.

### Api Gateway

An API gateway is a central entry point that manages, secures, and optimizes communication between multiple services, allowing them to work together efficiently.

ELI5 Example: Imagine the API gateway as a traffic cop directing and regulating the flow of cars (services) on a busy road (network), ensuring safe and efficient travel.

##### Common API Gateways

- Nginx
- Traefix

##### Request Process

1. User makes request (HTTP request)
2. API Gateway entry point - acts as a reverse proxy
3. Request routing - examines request and determines destination
4. Authentication and authorization - API gateway enforces auth/authorization and identity and permission
5. Rate limiting and throttling
6. Request transformation - modify request headers or payload
7. Load balancing - distributes requests evently and ensure HA
8. Caching - implement response caching
9. Logging and monitoring - request details, errors, responses
10. Request forwarding - forwards to backend service
11. Request handling - processes request and generates response
12. Request transformation - adding response headers or altering payload format
13. Security and filtering - scan for vulnerabilities or threats
14. Response caching & routing
15. Delivery to user

##### Forward / Reverse Proxies

Forward proxies intercept traffic from the client side

- Block access to content
- Protect identity

Reverse proxies intercept traffic from the server side

- Prevent DDOS
- Load balancing
- Caching content

### Caches

| Caching Strategy  | Description                                                  | Use Cases                                                    | Strategy |
| ----------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------- |
| **Cache-Aside**   | Applications are responsible for loading data into the cache. Data is fetched from the database and then stored in the cache when needed. If the data is already in the cache, it can be read directly. | Suitable for read-heavy workloads where not all data is frequently accessed. <br />Useful for scenarios with large datasets, where preloading the cache entirely may not be practical. | Read     |
| **Read-Through**  | Application does not interact directly with the cache. Instead, it requests data from the cache. If the data is not found, the cache fetches it from the database and then stores it in the cache for future use. | Effective for read-heavy workloads with frequently accessed data. <br />Ensures that the cache is always up-to-date with the database. | Read     |
| **Write-Around**  | Allows data to be written directly to the database without updating the cache. The cache is only populated when a read request occurs. This strategy avoids caching less frequently accessed data and is often used for write-heavy workloads. | Useful for scenarios where writes significantly outnumber reads. <br />Can be beneficial for caching infrequently accessed data, reducing cache pollution. | Write    |
| **Write-Back**    | Involves writing data to the cache first and then asynchronously updating the database. The data is considered updated once it's in the cache, and the database is updated in the background. This strategy optimizes write performance. | Suitable for write-heavy workloads where low-latency writes are critical. - Effective for scenarios with data that can tolerate eventual consistency. | Write    |
| **Write-Through** | Data is written to both the cache and the database simultaneously. Any write request updates the cache and the database in a synchronous manner. This strategy ensures data consistency between the cache and the database. | Appropriate for scenarios where data consistency is crucial. <br />Useful for read-modify-write patterns where updated data must be immediately visible. | Write    |

### Communication

##### Status Codes

- 100 - 199: Informational
- 200 - 299: Success
- 300 - 399: Redirection
- 400 - 499: Client Error (bad request, unauthorized, forbiddent)
- 500 - 599: Server Error (not implemented, bad gateway, gateway timeout, service unavailable)

##### RESTful

RESTful APIs adhere to the principles of REST, using HTTP methods and resources to interact with data. Resources can perform CRUD - Create, Read, Update, Delete on resources GET, POST, PUT, and DELETE

Challenges:

- many roundtrips to assemble data needed from endpoints
- not suitable for real time applications
- stateless, must contain all information from client

##### GraphQL

Query language used for faster responses and reduced network overhead. Exposes a single endpoint to query data needed.

Challenges:

- complicated queries
- more effort to setup initially
- lack of caching support

##### gRPC

Using protocol buffers over HTTP2, with strongly typed contracts. Faster than using JSON, normally used between microservices

Challenges:

- Binary format + protobuf might be unfamiliar
- non-human readable
- limited browser support

##### Websocket

Real time bidirectional connections designed for low latency and efficient data transfer

##### Webhook

Event-driven callbacks, async. Means call back a certain URL after a request is processed. Often called a reverse API because server sends request to client.

Challenges:

- server availability, if suffers for downtime or connectivity issues
- security concerns if not correct configured
- mainteanance and monitoring of errors, retries, deliveries, etc

##### Improving API performance

Pagination:

- Divides large data sets into smaller, manageable chunks.
- Reduces response times and server load by fetching only the required data.
- Enhances user experience by avoiding long wait times.
- Ideal for data-intensive applications like e-commerce and social media.

Asynchronous Logging:

- Delays non-critical operations like logging to improve API response times.
- Prevents blocking API requests for mission-critical tasks.
- Enhances scalability and allows the server to handle more concurrent requests.
- Suitable for applications where immediate logging is not essential.

Caching:

- Stores frequently accessed data or responses in memory or storage for quick retrieval.
- Reduces the need to recompute or re-fetch data, saving processing time.
- Enhances API performance by minimizing redundant work.
- Effective for read-heavy applications and public APIs.

Payload Compression:

- Reduces the size of data payloads for transmission.
- Decreases network latency and bandwidth usage, improving API response times.
- Especially beneficial for applications serving large files or extensive JSON/XML data.
- Requires decompression on the client side.

Connection Pool:

- Manages a pool of reusable database or network connections.
- Avoids the overhead of repeatedly opening and closing connections.
- Enhances scalability and resource utilization.
- Ideal for applications with frequent database or network interactions, such as web applications and microservices.

### Security

##### Common Auth Mechanisms

1. SSH Keys
2. OAuth Tokens
3. SSL Certificates
4. Credentials

##### SSH Keys

SSH keys are cryptographic pairs (public and private) for secure server access, offering strong authentication and encryption in remote connections.

##### OAuth

OAuth (Open Authorization) is an open standard protocol for enabling secure and delegated access to web resources. It allows third-party applications to access a user's data or services without requiring the user to share their credentials, ensuring enhanced security and privacy. In an OAuth flow:

1. The client (third-party application) requests access to a user's resources.
2. The user authenticates themselves with the authorization server.
3. Upon successful authentication, the user grants permission to the client.
4. The authorization server issues an access token to the client.
5. The client uses the access token to access the user's protected resources on the resource server, all without exposing the user's credentials.

![image-20240622143556094](.microservices-images/image-20240622143556094.png)

##### SSL Certificates

SSL certificates are digital files ensuring encrypted web communication, authenticated website identity, and secure data transmission, enhancing online security and trust.

##### Credentials

Credentials are user-provided information (username and password) or authentication data for accessing accounts, systems, and applications, validating user identity for secure access control.

##### JWT

JWT stands for "JSON Web Token." It is a compact, self-contained, and digitally signed token format often used for securely transmitting information between parties. JWTs are commonly employed for authentication and authorization in web applications and APIs. They consist of three parts: a header, a payload, and a signature. The header describes the token and its signing algorithm, the payload contains claims or data, and the signature is used to verify the token's authenticity. JWTs are typically used to authenticate users, share claims about them, and can be easily validated by the receiving party, making them a popular choice for implementing authentication and authorization in a stateless and distributed manner.




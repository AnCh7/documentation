## gRPC Health Checking Protocol

> References:
>
> https://github.com/grpc/grpc/blob/v1.15.0/doc/health-checking.md



A GRPC service is used as the health checking mechanism for both simple client-to-server scenario and other control systems such as load-balancing:

- since it is a GRPC service itself, doing a health check is in the same format as a normal rpc.
- has rich semantics such as per-service health status. 
- as a GRPC service, it is able reuse all the existing billing, quota infrastructure, etc, and thus the server has full control over the access of the health checking service.

Service Definition:

```protobuf
syntax = "proto3";

package grpc.health.v1;

message HealthCheckRequest {
  string service = 1;
}

message HealthCheckResponse {
  enum ServingStatus {
    UNKNOWN = 0;
    SERVING = 1;
    NOT_SERVING = 2;
  }
  ServingStatus status = 1;
}

service Health {
  rpc Check(HealthCheckRequest) returns (HealthCheckResponse);
}
```

A client can query the server's health status by calling the `Check` method, and a deadline should be set on the rpc. The suggested format of service name is `package_names.ServiceName`, such as `grpc.health.v1.Health`.

The server should register all the services manually and set the individual status, including an empty service name and its status.
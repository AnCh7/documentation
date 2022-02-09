### Dynatrace

> References:
>
> https://www.dynatrace.com/support/help/get-started



Dynatrace is a software intelligence platform:

-  monitor and optimize performance across infrastructure and applications
- visualize performance starting with user experiences in front end applications, flowing through our api gateways, backend services, and all the way down to database transactions and caching system calls
- track the impact of external services



Most of our instrumented services are using Dynatrace’s [OneAgent](https://www.dynatrace.com/support/help/setup-and-configuration/dynatrace-oneagent) solution which typically requires no code changes. For services running on Kubernetes clusters with OneAgent operators installed, processes  running the supported technologies are automatically detected and  instrumented.

Here are a few examples of Dockerfile and Makefile changes to enable Dynatrace instrumentation in Golang services:

```
RUN apk add gcc g++
RUN CGO_ENABLED=1 CC_FOR_TARGET=gcc CXX_FOR_TARGET=g++ -ldflags="-linkmode external"
```
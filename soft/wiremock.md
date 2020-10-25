### Documentation

WireMock is an HTTP mock server: http://wiremock.org/docs/getting-started.

It also has an assortment of other useful features including record/playback of interactions with other APIs, injection of faults and delays, simulation of stateful behaviour.

It can be used as a library by any JVM application, or run as a standalone process either on the same host as the system under test or a remote server.

- The JUnit rule provides a convenient way to include WireMock in your test cases. It handles the lifecycle for you, starting the server before each test method and stopping afterwards.
```java
@Rule
public WireMockRule wireMockRule = new WireMockRule(8089); // No-args constructor defaults to port 8080
```

* If you’re not using JUnit or neither of the WireMock rules manage its lifecycle in a suitable way you can construct and start the server directly:
```java
WireMockServer wireMockServer = new WireMockServer(wireMockConfig().port(8089)); //No-args constructor will start on port 8080, no HTTPS
wireMockServer.start();

// Do some stuff
WireMock.reset();

// Finish doing stuff
wireMockServer.stop();
```

* The WireMock server can be run in its own process, and configured via the Java API, JSON over HTTP or JSON file. http://wiremock.org/docs/running-standalone/
```bash
java -jar wiremock-standalone-2.24.1.jar
```
​       When running the standalone JAR, files placed under the `__files` directory will be served up as if from under the docroot, except if stub mapping matching the URL exists.

​       A GET request to the root admin URL e.g `http://localhost:8080/__admin` will return all currently registered stub mappings.


#### Verifying

The WireMock server records all requests it receives in memory (at least until it is [reset](http://wiremock.org/docs/stubbing#reset)). This makes it possible to verify that a request matching a specific pattern was received, and also to fetch the requests’ details.


#### Request Matching

WireMock supports matching of requests to stubs and verification queries using the following attributes:
- URL
- HTTP Method
- Query parameters
- Headers
- Basic authentication (a special case of header matching)
- Cookies
- Request body
- Multipart/form-data


#### Proxying

WireMock has the ability to selectively proxy requests through to other hosts. This supports a proxy/intercept setup where requests are by default proxied to another (possibly real, live) service, but where specific stubs are configured these are returned in place of the remote service’s response. Responses that the live service can’t be forced to generate on demand can thus be injected for testing. Proxying also supports [record and playback](http://wiremock.org/docs/record-playback/).


#### Record and Playback 

WireMock can create stub mappings from requests it has received. Combined with its proxying feature this allows you to “record” stub mappings from interaction with existing APIs.


#### Response Templating

Response headers and bodies, as well as proxy URLs, can optionally be rendered using [Handlebars templates](http://handlebarsjs.com/). 


#### Simulating Faults

One of the main reasons it’s beneficial to use web service fakes when testing is to inject faulty behaviour that might be difficult to get the real service to produce on demand. In addition to being able to send back any HTTP response code indicating an error, WireMock is able to generate a few other types of problem.


### Stateful Behaviour

WireMock supports state via the notion of scenarios. A scenario is essentially a state machine whose states can be arbitrarily assigned. It starting state is always `Scenario.STARTED`. Stub mappings can be configured to match on scenario state, such that stub A can be returned initially, then stub B once the next scenario state has been triggered.


#### HTTPS

WireMock can optionally accept requests over HTTPS. By default it will serve its own self-signed TLS certificate, but this can be overridden if required by providing a keystore containing another certificate.


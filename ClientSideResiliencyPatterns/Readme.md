# Client-side resiliency patterns

Client resiliency software patterns are focused on protecting a remote resource’s (another microservice call or database lookup) client from crashing when the remote resource is failing because that remote service is throwing errors or performing poorly. The goal of these patterns is to allow the client to “fail fast,” not consume valuable resources such as database connections and thread pools, and prevent the problem of the remote service from spreading “upstream” to consumers of the client. There are four client resiliency patterns:

* Client-side load balancing
* Circuit breakers
* Fallbacks
* Bulkheads

<img src="https://github.com/KiraDiShira/Spring/blob/master/ClientSideResiliencyPatterns/Images/csrp1.PNG" />

## Client-side load balancing

Because the client-side load balancer sits between the service client and the service consumer, the load balancer can detect if a service instance is throwing errors or behaving poorly. If the client-side load balancer detects a problem, it can remove that service instance from the pool of available service locations and prevent any future service calls from hitting that service instance. This is exactly the behavior that Netflix’s Ribbon libraries provide out of the box with no extra configuration.

## Circuit breaker

The circuit breaker pattern is a client resiliency pattern that’s modeled after an electrical circuit breaker. In an electrical system, a circuit breaker will detect if too much current is flowing through the wire. If the circuit breaker detects a problem, it will break the connection with the rest of the electrical system and keep the downstream components from the being fried. With a software circuit breaker, when a remote service is called, the circuit breaker will monitor the call. If the calls take too long, the circuit breaker will intercede and kill the call. In addition, the circuit breaker will monitor all calls to a remote resource and if enough calls fail, the circuit break implementation will pop, failing fast and preventing future calls to the failing remote resource.

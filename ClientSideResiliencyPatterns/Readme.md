# Client-side resiliency patterns

Client resiliency software patterns are focused on protecting a remote resource’s (another microservice call or database lookup) client from crashing when the remote resource is failing because that remote service is throwing errors or performing poorly. The goal of these patterns is to allow the client to “fail fast,” not consume valuable resources such as database connections and thread pools, and prevent the problem of the remote service from spreading “upstream” to consumers of the client. There are four client resiliency patterns:

* Client-side load balancing
* Circuit breakers
* Fallbacks
* Bulkheads

<img src="https://github.com/KiraDiShira/Spring/blob/master/ClientSideResiliencyPatterns/Images/csrp1.PNG" />

## Client-side load balancing

Because the client-side load balancer sits between the service client and the service consumer, the load balancer can detect if a service instance is throwing errors or behaving poorly. If the client-side load balancer detects a problem, it can remove that service instance from the pool of available service locations and prevent any future service calls from hitting that service instance. This is exactly the behavior that Netflix’s Ribbon libraries provide out of the box with no extra configuration.

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

## Fallback processing

With the fallback pattern, when a remote service call fails, rather than generating an exception, the service consumer will execute an alternative code path and try to carry out an action through another means. This usually involves looking for data from another data source or queueing the user’s request for future processing. The user’s call will not be shown an exception indicating a problem, but they may be notified that their request will have to be fulfilled at a later date. For instance, suppose you have an e-commerce site that monitors your user’s behavior and tries to give them recommendations of other items they could buy. Typically, you might call a microservice to run an analysis of the user’s past behavior and return a list of recommendations tailored to that specific user. However, if the preference service fails, your fallback might be to retrieve a more general list of preferences that’s based off all user purchases and is much more generalized. This data might come from a completely different service and data source.

## Bulkheads

By using the bulkhead pattern, you can break the calls to remote resources into their own thread pools and reduce the risk that a problem with one slow remote resource call will take down the entire application. The thread pools act as the bulkheads for your service. Each remote resource is segregated and assigned to the thread pool. If one service is responding slowly, the thread pool for that one type of service call will become saturated and stop processing requests. Service calls to other services won’t become saturated because they’re assigned to other thread pools.

 ```  
https://spring.io/guides/gs/circuit-breaker/

```

```xml

<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
	</dependency>

```

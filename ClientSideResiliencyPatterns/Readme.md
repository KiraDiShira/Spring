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

```java

@EnableCircuitBreaker
public class LicensingServiceApplication {

```
```java
	@HystrixCommand
	private Organization getOrganization(String organizationId) {
		return organizationRestClient.getOrganization(organizationId);
	}
```

Now, with @HystrixCommand annotation in place, the licensing service will interrupt a call out to its database if the query takes too long. If the database calls take longer than 1,000 milliseconds to execute the Hystrix code wrapping, your service call will throw a com.nextflix.hystrix.exception.HystrixRuntimeException exception.

While using the @HystrixCommand is easy to implement, you do need to be careful about using the default @HystrixCommand annotation with no configuration on the annotation. By default, when you specify a @HystrixCommand annotation without properties, the annotation will place all remote service calls under the same thread pool. This can introduce problems in your application. Later in the chapter when we talk about implementing the bulkhead pattern, we’ll show you how to segregate these remote service calls into their own thread pools and configure the behavior of the thread pools to be independent of one another.

how they can customize the amount of time before a call is interrupted by Hystrix. This is easily accomplished by passing additional parameters into the @HystrixCommand annotation:

https://github.com/Netflix/Hystrix/wiki/Configuration

```java

@HystrixCommand(fallbackMethod = "buildFallbackLicenseList")
	public List<License> getLicensesByOrg(String organizationId) {
		randomlyRunLong();

		return licenseRepository.findByOrganizationId(organizationId);
	}

	private List<License> buildFallbackLicenseList(String organizationId) {
		List<License> fallbackList = new ArrayList<>();
		License license = new License().withId("0000000-00-00000").withOrganizationId(organizationId)
				.withProductName("Sorry no licensing information currently available");
		fallbackList.add(license);
		return fallbackList;
	}
	
```
- 1) Fallbacks are a mechanism to provide a course of action when a resource has timed out or failed. If you find yourself using fallbacks to catch a timeout exception and then doing nothing more than logging the error, then you should probably use a standard try.. catch block around your service invocation, catch the HystrixRuntimeException, and put the logging logic in the try..catch block.

- 2) Be aware of the actions you’re taking with your fallback functions. If you call out to another distributed service in your fallback service you may need to wrap the fallback with a @HystrixCommand annotation. Remember, the same failure that you’re experiencing with your primary course of action might also impact your secondary fallback option. Code defensively. I have been bitten hard when I failed to take this into account when using fallbacks.

<img src="https://github.com/KiraDiShira/Spring/blob/master/ClientSideResiliencyPatterns/Images/csrp2.PNG" />

<img src="https://github.com/KiraDiShira/Spring/blob/master/ClientSideResiliencyPatterns/Images/csrp3.PNG" />

<img src="https://github.com/KiraDiShira/Spring/blob/master/ClientSideResiliencyPatterns/Images/csrp4.PNG" />

The first thing you should notice is that we’ve introduced a new attribute, thread-Poolkey, to your @HystrixCommand annotation. This signals to Hystrix that you want to set up a new thread pool. If you set no further values on the thread pool, Hystrix sets up a thread pool keyed off the name in the threadPoolKey attribute, but will use all default values for how the thread pool is configured. To customize your thread pool, you use the threadPoolProperties attribute on the @HystrixCommand. This attribute takes an array of HystrixProperty objects. These HystrixProperty objects can be used to control the behavior of the thread pool. You can set the size of the thread pool by using the coreSize attribute. You can also set up a queue in front of the thread pool that will control how many requests will be allowed to back up when the threads in the thread pool are busy. This queue size is set by the maxQueueSize attribute. Once the number of requestsexceeds the queue size, any additional requests to the thread pool will fail until there is room in the queue. 

Note two things about the maxQueueSize attribute. First, if you set the value to -1, a Java SynchronousQueue will be used to hold all incoming requests. A synchronous queue will essentially enforce that you can never have more requests in process then the number of threads available in the thread pool. Setting the maxQueueSize to a value greater than one will cause Hystrix to use a Java LinkedBlockingQueue. The use of a LinkedBlockingQueue allows the developer to queue up requests even if all threads are busy processing requests. The second thing to note is that the maxQueueSize attribute can only be set when the thread pool is first initialized (for example, at startup of the application). Hystrix does allow you to dynamically change the size of the queue by using the queue- SizeRejectionThreshold attribute, but this attribute can only be set when the maxQueueSize attribute is a value greater than 0. What’s the proper sizing for a custom thread pool? Netflix recommends the following formula: 

`(requests per second at peak when the service is healthy * 99th percentile latency in seconds) + small amount of extra threads for overhead`

You often don’t know the performance characteristics of a service until it has been under load. A key indicator that the thread pool properties need to be adjusted is when a service call is timing out even if the targeted remote resource is healthy. 

# Service discovery

## Why

First, it offers the application team the ability to quickly **horizontally scale up and down** the number of service instances running in an environment. The service consumers are abstracted away from the physical location of the service via service discovery. Because the service consumers don’t know the physical location of the actual service instances, new service instances can be added or removed from the pool of available services.

The second benefit of service discovery is that it helps increase application **resiliency**. When a microservice instance becomes unhealthy or unavailable, most service discovery engines will remove that instance from its internal list of available services.

<img src="https://github.com/KiraDiShira/Spring/blob/master/ServiceDiscovery/Images/sd1.PNG" />

Any services failing to return a good health check will be removed from the pool of available service instances.

<img src="https://github.com/KiraDiShira/Spring/blob/master/ServiceDiscovery/Images/sd2.PNG" />

[Getting started](https://spring.io/guides/gs/service-registration-and-discovery/)

 ` ` `
http://localhost:8761/eureka/apps/ORGANIZATION-SERVICE
 ` ` `

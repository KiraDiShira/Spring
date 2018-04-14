# Services gateway

<img src="https://github.com/KiraDiShira/Spring/blob/master/ServicesGateway/Images/sg1.PNG" />

<img src="https://github.com/KiraDiShira/Spring/blob/master/ServicesGateway/Images/sg2.PNG" />

Because a service gateway sits between all calls from the client to the individual services, it also acts as a central **Policy Enforcement Point (PEP)** for service calls.

The use of a centralized PEP means that cross-cutting service concerns can be implemented in a single place without the individual development teams having to implement these concerns. Examples of cross-cutting concerns that can be implemented in a service
gateway include:

- **Static routing**. A service gateway places all service calls behind a single URL and API route. This simplifies development as developers only have to know about one service endpoint for all of their services.
- **Dynamic routing**. A service gateway can inspect incoming service requests and, based on data from the incoming request, perform intelligent routing based on who the service caller is. For instance, customers participating in a beta program might have all calls to a service routed to a specific cluster of services that are running a different version of code from what everyone else is using.
- **Authentication and authorization**. Because all service calls route through a service gateway, the service gateway is a natural place to check whether the caller of a service has authenticated themselves and is authorized to make the service call.
- **Metric collection and logging**. A service gateway can be used to collect metrics and log information as a service call passes through the service gateway. You can also use the service gateway to ensure that key pieces of information are in place on the user request to ensure logging is uniform. This doesn’t mean that shouldn’t you still collect metrics from within your individual services, but rather a services gateway allows you to centralize collection of many of your basic metrics, like the number of times the service is invoked and service response time.

Wait—isn’t a service gateway a single point of failure and potential bottleneck?

Earlier in chapter 4 when I introduced Eureka, I talked about how centralized load balancers can be single point of failure and a bottleneck for your services. A service gateway, if not implemented correctly, can carry the same risk. Keep the following in mind as you build your service gateway implementation. Load balancers are still useful when out in front of individual groups of services. In this case, a load balancer sitting in front of multiple service gateway instances is an appropriate design and ensures your service gateway implementation can scale. Having a load balancer sit in front of all your service instances isn’t a good idea because it becomes a bottleneck.

Keep any code you write for your service gateway stateless. Don’t store any information in memory for the service gateway. If you aren’t careful, you can limit the scalability of the gateway and have to ensure that the data gets replicated across all service gateway instances. Keep the code you write for your service gateway light. The service gateway is the “chokepoint” for your service invocation. Complex code with multiple database calls can be the source of difficult-to-track-down performance problems in the service gateway.

```
http://localhost:5555/organization-service/v1/organizations/442adb6e-fa58-47f3-9ca2-ed1fecdfe86c
```
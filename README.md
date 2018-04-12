# Spring

**Spring Actuator**, which is included with the Spring Boot framework, exposes information about the operational health of the service along with information about the services runtime.

```
http://localhost:8080/actuator/health
```
**Spring Web**

```java
@RestController
@RequestMapping(value = "/v1/organizations/{organizationId}/licenses")
public class LicenseServiceController {

	@RequestMapping(value = "/{licenseId}", method = RequestMethod.GET)
	public License getLicenses(@PathVariable("organizationId") String organizationId,
			@PathVariable("licenseId") String licenseId) {
		return new License().withId(licenseId).withProductName("Teleco").withLicenseType("Seat")
				.withOrganizationId("TestOrg");
	}
}
```

## Configuration

### The role of configuration in microservices

Well first off you **remove some of the settings from your compiled code**. So get rid of some of these environmentally sensitive settings from your compiled, your deployed code base, connection strings, things, the like, get them out of your code, get them into something that can be externally managed. That's how people often use configurations is trying to make sure their code is portable between environments, and the configurations can change as they target dev, or test, or prod. 

Another useful function is actually **influencing the runtime behavior** using things like logging levels, feature toggles, and the like. So these help services change their behavior easily and quickly. That's possible when you have a configuration store that makes it easy for you to refresh these values. So you use configuration in microservices when you want to be able to change behavior without maybe deploying the entire application again. 

**Enforcing consistency across the service**. So having a configuration story helps you make sure that if you're just embedding this in code you could end up with different versions as you're scaling. In this case when you externalize the configuration you stand a better chance of having consistency as you build this out. 

And then finally there's a good chance that you can actually **use caching**, that comes along with configuration, to reduce the load on databases. So instead of going to a database every time to look up, let's say, screen settings of what you should display, or again, connection information, or even URLs for services. Instead of pounding a database to get those values, if you use a good configuration service or configuration in general, these values can be cached by the application, and therefore, you're not going to the database every time you need these values.

### Problems with the Status Quo

So local configuration files can **fall out of sync**. I force a full continuous integration process or continuous delivery process to make a single change. Even if I'm flipping a feature flag, depending on how I measure production, if I don't let people log in and SSH in, and change values, even to config files, I might need to push a full deployment of my application again just to make a config change. 

Another challenge is if you do use things like environment variables. That's one way to externalize the configuration out of your application, but you **don't have any change history**, typically, with something like environment variables. Someone could go in and change those values, and I don't know what they were before. So it's useful to use environment variables, but you don't really have any history there, so it can be a challenge to actually maintain this consistently in production. 

In some cases, configuration changes could **require a restart**. It could mean that the application instance has to restart to see these new values and be able to use them. Sometimes that may be fine, but in other cases you may not want to have to start rebooting services just to access the latest config values. 

Microservices often are part of organizational change. You have new teams, you have new structures, you you have independents. So if **each team does it their own way** you can have a security and management challenge with your configurations if everybody manages their configs in a unique fashion.

### Spring cloud configuration server

[Getting started](https://spring.io/guides/gs/centralized-configuration/)

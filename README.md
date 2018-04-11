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

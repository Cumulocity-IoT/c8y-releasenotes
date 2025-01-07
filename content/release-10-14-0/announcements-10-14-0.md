---
weight: 12
title: Important announcements
layout: bundle
---

### REST API changes

#### Planned

##### Breaking change in the Application API - the ability to change the application key and contextPath will be blocked

As of release 10.16+, it will no longer be possible to change the contextPath and the key of an application. This is a breaking change and API requests trying to update the values of these application properties will fail.

The reason for this change is that changing the values of these properties would break previous application binaries.

How will users be affected? If the user is hosting an application with a key and a binary with a manifest matching the respective key, and if the key in the application is changed, the binary will no longer work.

##### Breaking change in the Measurements API - several APIs will no longer be supported when *enhanced time-series support* is enabled

As of release 10.16+, new tenants can utilize a new enhanced time-series support for the Cumulocity IoT Operational Store. As a result, several APIs will no longer be supported when the feature is enabled.

The following APIs will be removed without any replacement:

* `GET /measurement/measurements/{id}`
* `DEL /measurement/measurements/{id}`

The following API is partially supported:

* `DEL /measurements/measurement/`

In release 10.16, the parameters `dateFrom` and `dateTo` are not supported. Instead, retention rules can be used to remove expired measurements data from the Operational Store.

In 10.17+ the parameters `dateFrom` and `dateTo` must be truncated to full hours (for example, 2022-08-19T14:00:00.000Z), otherwise an error will be returned.

#### Implemented

##### Breaking change in the Inventory API - restrictions for a set of properties

As announced with [release 10.13](/release-10-13-0/announcements-10-13-0) as of release 10.14, for the Inventory API, a set of properties has been restricted for internal system usage and cannot be set by external users. This change is motivated by performance improvements.

In case of a request sent with these properties, they will be ignored by the platform and not set.

The mentioned internal fragments are: `_c8y_Internal`, `_hierarchyCalculated`, `_hierarchy:root` and every fragment which starts with `_parent:`.

For example, if a user sends a request in the following format:

```
{
	"name": "testDevice",
    "owner": "device_654321",
    "c8y_IsDevice": {},
    "_parent:3":{}
}
```

prior to this change the whole payload was saved. Now, the `"_parent:3"` fragment will be ignored and not saved.


### Security changes

#### Planned

##### Improved validation of administrator password

To improve the security of Cumulocity IoT, in a future release 10.15+ the administrator password when creating a new tenant will be validated according to the tenant policy selected. If no policy is selected, the password will be validated according to the options set in the parent tenant.

##### Improved security for OAI-Secure users

With the 10.16+ release, when an administrator changes permissions inside a role, users who have this specific role assigned and use OAI-Secure will be logged out.

The purpose of this measure is to enhance security and to make sure that all OAI-Secure users have access to the relevant information in Cumulocity IoT, without compromising secure access to the information. We recommend that administrators announce this change and notify about the change, because the logged-in users will immediately be logged out, and all their changes will get lost. Therefore this action requires scheduling.

Basic authentication users will not be affected by this change.


### SDK changes

#### Planned

##### Updating the Web SDK to Angular 14

As of release 10.16 we will update the Cumulocity IoT Web SDK to Angular 14. See the [Angular upgrade guide](https://v14.angular.io/guide/update-to-latest-version) for more information on what must be changed in your custom applications. Refer to our [default upgrade instructions](https://cumulocity.com/guides/10.14.0/web/upgrade/#update-to-an-newer-version) to update the files provided by the Cumulocity IoT CLI tool.

##### Removal of support for Angular Schema Form

As of release 10.15 we will stop the support for [Angular Schema Form](http:/schemaform.io/) in the `filteringConfig` of device grid columns in favor of the more modern [Angular Formly](https://formly.dev/). After this change it will no longer be feasible to build an application until all device grid custom column definitions are migrated to use the new definitions.

In the future, we also plan to stop the support for Angular Schema Form in other parts of the application including AngularJS components. For more information on Angular Formly, see [their official documentation page](https://formly.dev/examples/introduction).


#### Implemented

##### Removing PlatformImpl Spring bean from Microservice SDK

With release 10.14 we closed a design gap, which previously existed in Cumulocity IoT and allowed to wrongly use Cumulocity IoT APIs (by mixing Spring injection with raw Java).

PlatformImpl will no longer be exposed as a Spring Bean and it will not be feasible to inject it.

Instead of creating new instances using PlatformImpl, you should always inject Cumulocity IoT API beans, such as InventoryApi, AlarmApi, IndentityApi, for example:

```
@Autowired //injection
private InventoryApi inventoryApi;
```

### Streaming Analytics

#### Planned

##### Removal of Esper

Further to the CEL (Esper) deprecation notice in [release 10.5](/release-10-5-0/migration-10-5-0/) and the subsequent announcement of end of support in [release 10.7](/release-10-7-0/announcements-10-7-0/) it has been possible to continue using Esper in an unsupported mode. With effect from release 10.15 the CEL (Esper) functionality will be removed completely and cannot be used anymore.

If you need assistance to migrate your streaming analytics logic from Esper to Apama, please contact [Cumulocity Support](https://cumulocity.com/docs/additional-resources/contacting-support/).

#### Implemented

##### Analytics Builder - renaming of frgment property name

The **Measurement Input** block sends the property name that is used for a fragment as part of its output value.
In previous versions, this property name was `frgment`. This has now been changed to `fragment`.
Currently, both property names `frgment` and `fragment` are supported.
`frgment`, however, is now deprecated and will be removed in a future release.
Therefore it is recommended that you now change `frgment` to `fragment` in all of your blocks
(for example, the value of the **Property Path** parameter in the **Extract Property** block).

##### Cumulocity IoT transport in Apama

As of release 10.14, sending email requests from Apama to Cumulocity IoT requires the user to have the ROLE_EMAIL_CREATE permission.
This means that the user name that is used in Software AG Designer projects and any Apama applications connecting externally to the Cumulocity IoT platform must have this permission.

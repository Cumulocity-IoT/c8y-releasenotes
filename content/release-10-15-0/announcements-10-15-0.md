---
weight: 12
title: Important announcements
layout: bundle
---

### REST API changes

#### Planned

##### Breaking change in the Application API - the ability to change the application key and contextPath will be blocked

As announced with [release 10.14](/release-10-14-0/announcements-10-14-0), as of release 10.16+ it will no longer be possible to change the contextPath and the key of an application. This is a breaking change and API requests trying to update the values of these application properties will fail.

The reason for this change is that changing the values of these properties would break previous application binaries.

How will users be affected? If the user is hosting an application with a key and a binary with a manifest matching the respective key, and if the key in the application is changed, the binary will no longer work.

##### Breaking change in the Measurements API - several APIs will no longer be supported when "enhanced time-series support" is enabled

As announced with [release 10.14](/release-10-14-0/announcements-10-14-0), as of release 10.16+ new tenants can utilize a new enhanced time-series support for the Cumulocity IoT Operational Store. As a result, several APIs will no longer be supported when the feature is enabled.

The following APIs will be removed without any replacement:

* `GET /measurement/measurements/{id}`
* `DEL /measurement/measurements/{id}`

The following API is partially supported:

* `DEL /measurements/measurement/`

In release 10.16, the parameters `dateFrom` and `dateTo` are not supported. Instead, retention rules can be used to remove expired measurements data from the Operational Store.

In 10.17+ the parameters `dateFrom` and `dateTo` must be truncated to full hours (for example, 2022-08-19T14:00:00.000Z), otherwise an error will be returned.

##### Breaking change in the Alarms, Events, Measurements APIs - required parameters will be introduced

As of release 10.17, at least one query parameter limiting the affected data will be required to prevent accidental deletion of too many objects during a bulk delete operation.
The change affects given APIs:

* `DELETE /alarm/alarms` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`, `createdFrom`, `createdTo`
* `DELETE /event/events` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`, `createdFrom`, `createdTo`
* `DELETE /measurements/measurement` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`

##### Breaking change in SmartREST 2.0 - DATE field used as custom property will be stored as string

As of release 10.17, a SmartREST 2.0 DATE field used as a custom property will be stored as a string in the Cumulocity IoT database.
This affects the REST response format.

Example of previous response format (note `aCustomDateField` field):

```json
{
   "source":{
      "id":"91123"
   },
   "type":"TYPE-911",
   "aCustomDateField":{
      "date":{
         "date":14,
         "seconds":15,
         "hours":12,
         "month":10,
         "year":122,
         "timezoneOffset":-60,
         "minutes":41,
         "time":1668426075840,
         "day":1
      },
      "offset":120
   },
   "c8y_TemperatureMeasurement":{
      "T":{
         "unit":"C",
         "value":10
      }
   }
}
```

New format:

```json
{
   "source":{
      "id":"91123"
   },
   "type":"TYPE-911",
   "aCustomDateField":"2022-11-14T12:44:11.481+01:00",
   "c8y_TemperatureMeasurement":{
      "T":{
         "unit":"C",
         "value":10
      }
   }
}
```
The change will be introduced to improve the consistency between different Cumulocity IoT protocols.

### Security changes

##### Deprecation of SMS TFA feature

The SMS TFA (Two-Factor Authentication) feature is deprecated. With release 10.17+, it will be removed and not be functional any longer, and we will no longer support SMS TFA.

What does this mean for users?

We recommend you to gradually start switching off SMS TFA in earlier versions (10.15 or 10.16) in order to be able to detect any issues, and be prepared when it is unavailable as of release 10.17. Instead of TFA SMS you can use TFA TOTP.

If you don´t know what is required to switch off SMS TFA, please contact our customer service at Software AG.

#### Implemented

##### Cumulocity IoT microservice user privilege deprecation

**What will happen**

With release 10.15, Software AG announces the availability of the Microservice API version 2 and the deprecation of Microservice API version 1 to comply with new security requirements.

Microservice API version 2 provides an improved microservice container security context restricting the invocation of privileged Linux Kernel APIs. More precisely, all capabilities are granted to microservices with API version 1 whereas only the capability `CAP_NET_BIND_SERVICE` is granted to microservices with API version 2.

Refer to [this Linux man page](https://man7.org/linux/man-pages/man7/capabilities.7.html) for more information about these capabilities.

**What you must do by the 10.15 release**

Migrate your microservice to the new API version 2. In the simplest case it is sufficient to set the API version 2 in your microservice manifest. However, for microservices which currently make use of Linux Kernel API which requires one of the above-mentioned user privileges you additionally must refactor the source code so that the service doesn’t require the invocation of these privileged Linux Kernel APIs anymore. For details refer to [Microservice migration to API version 2](https://cumulocity.com/guides/10.15.0/microservice-sdk/concept/#migration) in the <i>Microservice SDK guide</i>.

**How to check whether your microservice is impacted?**

Set the API version field in the microservice manifest to “2” and deploy this service to your Cumulocity IoT test environment. This environment must be in version 10.15. Verify that the functionality provided by the microservice still works as expected.

**How to identify deprecated microservices?**

An administrator can use the query language of the Inventory REST API to identify deprecated microservices. The microservice manifest of all owned microservices is saved in the inventory of the tenant. The respective microservice manifest objects are marked with a dedicated field, namely `com_cumulocity_model_application_MicroserviceManifest`.

**What happens if your Cumulocity IoT microservice is still using one of these user privileges after the upgrade of the environment to the 10.16 release**

By default, microservices using the deprecated API version 1 still work in version 10.15. But the behavior is configurable per Cumulocity IoT environment (see *Operational procedures* in the *Cumulocity IoT Core - Operations guide*), and it might happen, depending on the configuration of this environment, that your microservice using the deprecated API version 1 will no longer work.

#### Not implemented

##### Improved security for OAI-Secure users

In [release 10.14](/release-10-14-0/announcements-10-14-0) we announced a planned change, that when an administrator changes permissions inside a role, users who have this specific role assigned and use OAI-Secure will be logged out.

Such a forced log out is no longer needed and will not be implemented, as permission changes are immediately reflected on the active user sessions. Session invalidation on permission changes is not required any more.

### SDK changes

#### Planned

##### Updating the Web SDK to Angular 14

As of release 10.16 we plan to update the Web SDK to Angular 14. See the [Angular upgrade guide](https://v14.angular.io/guide/update-to-latest-version) for more information on what must be changed in your custom application. You can use our [default upgrade instructions](https://cumulocity.com/guides/10.15.0/web/upgrade/#update-to-an-newer-version) to update the files provided by the Cumulocity CLI tool.

##### New default branding

As of Release 10.17, we will update the default branding for all Cumulocity IoT default applications. Branded applications will not be affected, however, as the new navigator changes from a dark color to a light color, the default navigator font color might not work with your current branding settings. You might end up with a dark font color on a dark background, which might not be readable or accessible by your users. You can review this by opening the branding editor in the Administration application and check what font color you are using in the current version.

##### Removal of Cumulocity IoT Device SDKs in favor of thin-edge.io

As of release 10.16 we will remove the Cumulocity IoT Device SDKs. This includes the removal of the [C++ Device SDK instructions](https://cumulocity.com/guides/10.15.0/device-sdk/cpp/), and the [Linux Agent guides](https://cumulocity.com/guides/10.15.0/device-sdk/linux-agent-user-guide/) from our public documentation. While they are not supported or further maintained, the code can be found in our [open-source repositories](https://github.com/SoftwareAG) in case you still have them in use.

As a recommended mechanism to integrate Linux-based devices into Cumulocity IoT we advise you to use [thin-edge.io](https://thin-edge.io/), which is actively developed by Software AG and open-source community partners. For customers with enterprise-grade support & maintenance needs, a commercial variant (Cumulocity IoT Thin Edge) can be purchased.

#### Implemented

##### Removal of support for Angular Schema Form

As announced with [release 10.14](/release-10-14-0/announcements-10-14-0), as of release 10.15 the support for [Angular Schema Form](http:/schemaform.io/) in the `filteringConfig` of device grid columns has stopped in favor of the more modern [Angular Formly](https://formly.dev/). It is now no longer feasible to build an application until all device grid custom column definitions are migrated to use the new definitions.

In the future, we also plan to stop the support for Angular Schema Form in other parts of the application including AngularJS components. For more information on Angular Formly, see [their official documentation page](https://formly.dev/examples/introduction).


### Streaming Analytics

#### Planned

##### Deprecated events in com.apama.cumulocity

The `SMSResponse` and `SMSResourceReference` events in the `com.apama.cumulocity` package are now deprecated. They are not used by Cumolocity IoT and will therefore be removed in a future release.

#### Implemented

##### Analytics Builder - removal of frgment property

As announced in [release 10.14](/release-10-14-0/announcements-10-14-0/), the `frgment` property has been removed in 10.15.
If you have not already done so, you must now change `frgment` to `fragment` in all of your blocks
(for example, the value of the **Property Path** parameter in the **Extract Property** block).

##### Changed events in com.apama.cumulocity

The `GenericRequest` event in the `com.apama.cumulocity` package has been changed to improve consistency in error handling.
In all cases of a response from the server, a `GenericResponseComplete` event is now sent to the correct response channel.
In addition, the `GenericResponseComplete` event now has the following additional members:

- `boolean error` - Set to `true` if the `GenericRequest` received either an error content type or a non-2xx HTTP return code.
- `integer status` - The HTTP return code.
- `string details` - Details of the response which were not sent as a `GenericResponse`.

It is recommended that you now use the updated `GenericResponseComplete` event instead of the `Error` event
since the `GenericResponseComplete` event is sent to the same channel as the `GenericResponse` event.

In addition, there is a change in behavior where previously some `GenericRequest` events which received non-2xx response codes were still sending `GenericResponse` events.
As of this version, only requests where `error` is `false` and the payload is JSON will send `GenericResponse` events.
For more details, see the `com.apama.cumulocity` package in the [API Reference for EPL (ApamaDoc)](https://documentation.softwareag.com/pam/10.15.0/en/webhelp/related/ApamaDoc/index.html).

In previous versions, no response events were sent at all in some cases.
As of this version, the `GenericResponseComplete` event is always sent for all `GenericRequest` events in any error case.

##### Cumulocity IoT transport in Apama

To improve performance, the Cumulocity IoT transport now sends requests to the Cumulocity IoT platform concurrently using multiple clients.
New API and configuration options have been added to control this behavior.
For a complete description of the new concurrency behavior and options to control it,
see [Optimizing requests to Cumulocity IoT with concurrent connections](https://documentation.softwareag.com/pam/10.15.0/en/webhelp/pam-webhelp/index.html#page/pam-webhelp%2Fco-ConApaAppToExtCom_cumulocity_optimizing_requests_to_cumulocity_iot_with_concurrent_connections.html)
in the Apama documentation.

The Cumulocity IoT transport now supports multi-tenant deployment. For this purpose, the following new configuration options are now available:

- `CUMULOCITY_MULTI_TENANT_APPLICATION`
- `CUMULOCITY_MULTI_TENANT_MICROSERVICE_NAME`

See also [Configuring the Cumulocity IoT transport](https://documentation.softwareag.com/pam/10.15.0/en/webhelp/pam-webhelp/index.html#page/pam-webhelp%2Fco-ConApaAppToExtCom_cumulocity_configuring_the_cumulocity_transport.html)
and [Working with multi-tenant deployments](https://documentation.softwareag.com/pam/10.15.0/en/webhelp/pam-webhelp/index.html#page/pam-webhelp%2Fco-ConApaAppToExtCom_cumulocity_working_with_multi_tenant_deployments.html)
in the Apama documentation.

The following new configuration properties, which correspond to existing configuration options in the YAML configuration file, are now available:

- `CUMULOCITY_REQUEST_ALL_DEVICES` (corresponds to `requestAllDevices`)
- `CUMULOCITY_SUBSCRIBE_ALL_MEASUREMENTS` (corresponds to `subscribeToAllMeasurements`)
- `CUMULOCITY_SUBSCRIBE_DEVICES` (corresponds to `subscribeToDevices`)
- `CUMULOCITY_SUBSCRIBE_OPERATIONS` (corresponds to `subscribeToOperations`)

It is recommended to use the new configuration properties instead of editing the YAML configuration file.
See also [Configuring the Cumulocity IoT transport](https://documentation.softwareag.com/pam/10.15.0/en/webhelp/pam-webhelp/index.html#page/pam-webhelp%2Fco-ConApaAppToExtCom_cumulocity_configuring_the_cumulocity_transport.html)
in the Apama documentation.

##### Docker images

Apama 10.15.0 introduces several new container images provided via Docker Hub and some of the existing container images have changed content.
See also [Published Apama container images](https://documentation.softwareag.com/pam/10.15.0/en/webhelp/pam-webhelp/index.html#page/pam-webhelp%2Fco-DepAndManApaApp_published_apama_container_images.html)
in the Apama documentation.

When building images for use as a Cumulocity IoT microservice, then you must use the
[softwareag/apama-cumulocity-jre](https://hub.docker.com/r/softwareag/apama-cumulocity-jre) image with the new
[softwareag/apama-cumulocity-builder](https://hub.docker.com/r/softwareag/apama-cumulocity-builder) image as a builder image.
To do this with the default project Dockerfile created by Software AG Designer, either change the `FROM` lines in the Dockerfile appropriately or build using the following flags:

```
--build-arg APAMA_BUILDER=softwareag/apama-cumulocity-builder:10.15 --build-arg APAMA_IMAGE=softwareag/apama-cumulocity-jre:10.15
```

##### Support lifetimes for connectivity components

With Apama 10.15.0, the support lifetimes for the components for connectivity to Cumulocity IoT, and the Apama Docker images with that connectivity,
have been aligned with the Cumulocity IoT support schedule. For more details, refer to the *Release Availability* document for version 10.15.
This is available from the following web page: https://documentation.softwareag.com/apama/index.htm.

##### Removal of Esper

Further to the CEL (Esper) deprecation notice in [release 10.5](/release-10-5-0/migration-10-5-0/) and the subsequent announcement of end of support in [release 10.7](/release-10-7-0/announcements-10-7-0/) it has been possible to continue using Esper in an unsupported mode. With effect from release 10.15 the CEL (Esper) functionality has been removed completely and cannot be used anymore.

If you need assistance to migrate your streaming analytics logic from Esper to Apama, please contact [Cumulocity support](https://cumulocity.com/docs/additional-resources/contacting-support/).


### Machine Learning Workbench

#### Implemented

- Currently, both "Machine Learning Manager" and "Machine Learning Admin" user groups can execute Python scripts, run Python commands using Jupyter notebook, train workflows, and train neural network models.
To further enhance the security, the above functionalities will be limited to the "Machine Learning Admin" user group only.

- The "Machine Learning Admin" user group should be treated as a privileged user group.

- The following APIs are only accessible to  the "Machine Learning Admin" user group now:

   GET - jnb-sessions:

    ```
    {{url}}/service/mlw/jnb-sessions
    ```
   GET - jnb-content:

    ```
    {{url}}/service/mlw/projects/{{projectID}}/resources/{{resourceID}}/jnb-content
    ```
   PUT - jnb-content:

    ```
    {{url}}/service/mlw/projects/{{projectID}}/resources/{{resourceID}}/jnb-content
    ```

   POST - training workflows:

    ```
    {{url}}/service/mlw/projects/{{projectID}}/resources/{{resourcesID}}/workflow
    ```
   POST - code execution:

    ```
    {{url}}/service/mlw/projects/{{projectID}}/resources/{{resourcesID}}/execute
    ```
   POST - neural networks:

    ```
    {{url}}/service/mlw/projects/{{projectID}}/resources/{{resourcesID}}/trainNN
    ```

- The "Machine Learning Manager" user group has been restricted from executing Jupyter notebooks and accessing the assets section as well.

- The "Machine Learning User" user group has been restricted from accessing the assets section.

- This access limitation will be a breaking change for the existing "Machine Learning Manager" user group.

- Those users who are currently in the "Machine Learning Manager" group and need code execution access must be accordingly added to a higher privileged group, for example, "Machine Learning Admin".


### Cumulocity IoT DataHub

#### Planned

##### Breaking change in the offloading mechanism - deprecation of support for mixed types

In a future release of Cumulocity IoT DataHub a newer version of Dremio will be adopted that removes support for mixed data types in a single column. The offloading process will be adapted so that mixed types will be automatically resolved during offloading. Cumulocity IoT DataHub will not be able to read data lake contents containing mixed data types, so corresponding data must be converted.

##### Breaking change in the offloading mechanism - switch to new table format may introduce incompatibilities

In future releases of Cumulocity IoT DataHub the offloading mechanism may leverage the Apache Iceberg table format, which is an open format supported by Dremio. Due to this change, current offloading configurations may fail. For example, they may fail due to rarely used data types which are incompatible with the Apache Iceberg format. Further details and remediation actions will be provided closer to the respective release.

---
weight: 12
title: Important announcements
layout: bundle
---

### Transition to continuous updates

For all public cloud instances, we are moving from quarterly releases towards a continuous deployment of software updates. The 10.18 release will be the last release deployed using our current release model. After the 10.18 release, the different components of our platform will be updated on a continuous basis.

Continuous updates will provide you with a steady stream of enhancements, bug fixes, and exciting new functionalities as they become available. This change allows us to respond swiftly to your feedback and ensure you always have the most up-to-date version of our product.

If you own a dedicated Cumulocity IoT instance, we will collaborate with you on an individual basis to plan the transition to either continuous deployment or the alternative yearly release model.

We appreciate your support as we embark on this transition. Your feedback and suggestions are invaluable as we shape the future of our product. You will find more details about the planned changes in the 10.18 release announcement.

Find more details in the document [Continuous Deployment for Cumulocity IoT](/files/202311-CumulocityIoT-CD-Customer-Communication-v1.3.pdf).

### Changes in build components

Cumulocity IoT releases consist of various components such as Core (backend) and UI (frontend). In earlier releases these components were released as one build component at the same time with the same [maintenance release version](/about/release-types). This has changed in release 10.17.

Starting from release 10.17, Core, UI, and several other components such as Messaging Service are released as separate build components with independent maintenance release versions, that is, fix release versions might be different for each component. Consequently, the release notes entries now show the build component (Core, UI, Messaging Service) in conjunction with the component's build version.


### REST API changes

#### Planned

##### Breaking change in the Inventory API -- change of the return code for the deletion of managed objects

As announced with [release 10.17](/release-10-17-0/announcements-10-17-0), in a version after 10.18, if the deletion of a managed object and its dependencies doesn´t finish immediately, the platform will return a 202 (Accepted) HTTP status code instead of 204 (No content).
The return code will still be 204 if the control can be returned immediately.
This change is required to make the API consistent with the HTTP protocol semantics (asynchronous request).

##### Breaking change in the Inventory API -- change of the default value for the `withChildren` parameter

As announced with [release 10.17](/release-10-17-0/announcements-10-17-0), in a version after 10.18, the default value for the `withChildren` parameter of the `GET /inventory/managedObjects` endpoint will be changed from `true` to `false`.
As a consequence, if a request is supposed to return child assets, devices and additions, a parameter `?withChildren=true` has to be provided in the query.
This change is necessary to improve the Inventory API performance.

##### Breaking change in all REST APIs

As announced with [release 10.17](/release-10-17-0/announcements-10-17-0), for versions after 10.18,
the `totalPages` statistics value by default is no longer returned for REST API requests without search criteria.
A `withTotalPages=true` parameter has to be explicitly provided to the requests when `totalPages` is expected.

For example:
`GET /user/users?pageSize=1000` does not return `totalPages` statistics.
`GET /user/users?withTotalPages=true` does return `totalPages` statistics.

The change is enforced by performance reasons.


#### Implemented

##### Breaking change in the Alarms, Events, Measurements APIs - required parameters will be introduced

As announced earlier, see [release 10.17](/release-10-17-0/announcements-10-17-0), at least one query parameter limiting the affected data will be required to prevent accidental deletion of too many objects during a bulk delete operation.
This change affects the following APIs:

* `DELETE /alarm/alarms` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`, `createdFrom`, `createdTo`
* `DELETE /event/events` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`, `createdFrom`, `createdTo`
* `DELETE /measurements/measurement` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`


##### Breaking change in the Inventory API - restrictions for a set of properties

As announced earlier, see [release 10.17](/release-10-17-0/announcements-10-17-0), a set of properties in the Inventory API is now restricted to internal system usage and cannot be set by external users. This change is motivated by planned features and future use cases.

In case of a request sent with these properties, they will be ignored by the platform and not set.

This change applies to the following fragments: `c8y_LatestMeasurements`, `c8y_LatestEvents`, `c8y_LatestAlarms`, `c8y_LatestOperations`, `c8y_LastAlarm`, `c8y_LastEvent`, `c8y_LastOperation` .

For example, if a user sends a request in the following format:

```
{
    "name": "testDevice",
    "owner": "device_654321",
    "c8y_IsDevice": {},
    "c8y_LatestMeasurements":{
      "c8y_Temperature":{
         "T": :{
            "value" : 25.4,
            "unit" : "C"
         }
      }
    }
}
```

Since this release, the `c8y_LatestMeasurements` fragment will be ignored and not saved.

##### Breaking change in all REST APIs

As announced earlier, see [release 10.17](/release-10-17-0/announcements-10-17-0), wrong values for `pageSize` or `currentPage` now result in a 422 (Unprocessable entity) HTTP status code instead of a 500 (Internal server error) HTTP status code.

### Security changes

#### Planned

##### User administrator can no longer set password for other users

As announced with [release 10.17](/release-10-17-0/announcements-10-17-0), to improve security, in versions higher than 10.18, user administrators will no longer be able to explicitly set passwords or email addresses for other users in the tenant.
This change prevents that an attacker could have access to all users, in case the administrator account was compromised.
Note that the administrator will still have the option to force the user to reset the password on the next login or disable the user.


### Microservice infrastructure changes

#### Planned

##### Breaking change: Use of Linux cgroup v2

As announced earlier, see [release 10.17](/release-10-17-0/announcements-10-17-0), in Cumulocity IoT versions after 10.18, microservices must use a Linux cgroup v2 aware application runtime. When executing microservices which are not compatible with cgroup v2 on Cumulocity IoT in these versions it might happen that the information provided by the application runtime concerning available CPU and memory is not correct. This might lead to incorrect memory and thread allocation in the microservice container process.

cgroup is a Linux kernel feature to organize processes hierarchically and distribute system resources along the hierarchy in a controlled and configurable manner. Every process in the system belongs to one and only one cgroup. In Cumulocity IoT cgroups are used to enforce container resource limits.

In versions after 10.18 it will be necessary for all microservices to be compatible with Linux cgroup v2. This updated version brings significant improvements and enhanced functionality for resource management and isolation, ensuring better performance and scalability of your applications. Linux cgroup v2 was released with kernel version 4.5 in March 2016.

When using the Cumulocity IoT Microservice SDK for developing microservices, ensure to configure a Java version which is cgroup v2 aware when building your microservice. When using Java 8, ensure to use openjdk8u372 or higher. When using Java 11, use Java 11.0.16 or higher, or use Java 15 or higher.
When using a server runtime other than OpenJDK Java as microservice application runtime, refer to the documentation of the provider.

##### Alarms when using Microservice API version 1

With [release 10.15](/release-10-15-0/announcements-10-15-0), we announced the availability of the Microservice API version 2 and the deprecation of Microservice API version 1 to comply with new security requirements.
By default, microservices using API version 1 still work in release 10.18. However, in Cumulocity IoT versions after 10.18 a warning might show up indicating that attempts to deploy such a microservice might fail in the future.

### SDK changes

#### Planned

##### Deprecating @c8y/cli in favor of @angular/cli
To improve our tooling and align closer to the Angular standard, we will deprecate @c8y/cli and the `c8ycli` command in a future version. Instead, users must install the standard CLI from Angular (@angular/cli) and start the application via `ng` command. The Web SDK can then simply be added to the Angular CLI by running the command `ng add @c8y/websdk`. A detailed description on how you can update your existing application will be added to the Web SDK documentation.

##### Breaking change in the Web SDK - deprecation of HOOK_ prefixed injection tokens

`HOOK_` prefixed injection tokens, like for example `HOOK_COMPONENTS`, `HOOK_ROUTE` or `HOOK_NAVIGATOR_NODES`, are deprecated. In version 10.17 an alternative to these hooks has been introduced, which allows typed usage. This is a significant benefit for Web SDK users since they no longer have to guess the types and attributes offered by these hooks. The deprecated functionality will be removed in a future version of Cumulocity IoT.

A migration is only required for users who used the Web SDK for customizing their user interface.

For example the `HOOK_ROUTE` would be migrated from:

```
providers: [
   {
      provide: HOOK_ROUTE,
      useValue: [{
         context: ViewContext.Device,
         path: 'hello',
         component: HelloComponent,
         label: 'hello',
         priority: 100,
         icon: 'rocket'
      }],
      multi: true
   }
]
```

to the new hook function:

```
providers: [
   hookRoute([
      {
         context: ViewContext.Device,
         path: 'hello',
         component: HelloComponent,
         label: 'hello',
         priority: 100,
         icon: 'rocket'
      }
   ]);
]
```

All of the impacted injection tokens have been marked as deprecated with 10.18, and references to their replacements are provided in the respective documentation and deprecation notice.

##### The Web SDK extension HOOK_SEARCH will be refactored

The HOOK_SEARCH can be used by developers to extend the web integration of the search in a custom web application. As announced with [release 10.17](/release-10-17-0/announcements-10-17-0), as of a future version, we will refactor the HOOK_SEARCH interface. If you use this interface in a custom-developed UI application, you must migrate to the new version. Details on how to use the new interface will be provided in the Web SDK documentation of the respective release.

##### Deprecation of Russian localization

The Russian localization of the Cumulocity IoT platform UI is no longer supported. It will be removed from the product in a future version, at the earliest in July 2024.

If you have a continued need for a Russian localization of the Cumulocity IoT UI, please contact [product support](https://cumulocity.com/docs/additional-resources/contacting-support/).


#### Implemented


##### Changes in Gainsight tracking

A banner informing users about our cookie and privacy policy (Privacy Notice) is enabled for all users on our Public cloud instances.

As part of our efforts to continuously improve the overall user experience, anonymous statistical data collection is now activated for all users accepting only our required cookies. This helps us gather valuable insights and improve our services without compromising your privacy. Anonymous tracking doesn't collect any Personal Identifiable Information (PII) such as email addresses or names. Users can change their cookie and tracking preferences at any time from their personal profiles. Our Privacy Notice can be found here: [https://www.softwareag.com/en_corporate/privacy.html](https://www.softwareag.com/en_corporate/privacy.html)

As part of our data collection policy, we only collect data from users who have been informed about our cookies and privacy policies (Privacy Notice) through the cookies banner.

For customers accessing our public cloud products, we might use in-product communication for improved user experience (popups, guides, tours and surveys). Users can disable or enable in-product engagements at any time from their user profile settings. However, if you use our custom branding functionalities, these in-product communication features will be disabled by default.

##### Breaking change in the Map widget

As announced with [release 10.17](/release-10-17-0/announcements-10-17-0), the "Map" widget has been migrated and support for real-time updates on all devices has been replaced by a configurable refresh interval. This change allows us to display more then 100 devices on the map by default. Additionally, the "Map" widget no longer supports the "show track" option.

##### Deprecation of the Microservice SDK for C#

With the introduction of auto-generated SDKs, we provide an always up-to-date developer library with the purpose to facilitate the development of Cumulocity IoT microservices in different programming languages including C#.

The new auto-generated SDKs make our existing Microservice SDK for C# obsolete. For this reason, the Microservice SDK for C# has been removed from our public product documentation.

To learn more about using the auto-generated SDKs, refer to our respective [GitHub repository](https://github.com/SoftwareAG/cumulocity-clients-dotnet).


### Streaming Analytics

{{< c8y-admon-important >}}
Streaming Analytics release 10.18.0 has already been made available with Cumulocity IoT release 10.17.0.
{{< /c8y-admon-important >}}

#### Planned

##### Deprecation of Machine Learning

As announced with [release 10.16](/release-10-16-0/announcements-10-16-0), the **Machine Learning** block in Analytics Builder is deprecated and will be removed in a future release.
This is in line with the deprecation of [Machine Learning Engine](/release-10-16-0/announcements-10-16-0/#machine-learning-deprecation) announced in release 10.16.

#### Implemented

##### New sample for calling another microservice

As announced with [release 10.16](/release-10-16-0/announcements-10-16-0), the EPL Apps sample "Call Zementis microservice" sample has been removed.
It has been replaced by a new "Call another microservice" sample which now uses the `/health` endpoint of an Apama-ctrl microservice.
See also [Connecting Apama to other microservices](https://cumulocity.com/guides/10.18.0/streaming-analytics/epl-apps/#microservices) in the *Streaming Analytics guide*.

##### Breaking change in Analytics Builder - Acknowledged alarms

The [Alarm Output](https://cumulocity.com/guides/10.18.0/streaming-analytics/block-reference/#alarm-output) block
no longer reactivates "Acknowledged" alarms with every modification.
See also the fix release note for [10.16.0.4](/release-10-16-0/streaming-analytics-10-16-0/#10_16_0_4).

##### Analytics Builder - Change of type for Clear Alarm input port

In the [Alarm Output](https://cumulocity.com/guides/10.18.0/streaming-analytics/block-reference/#alarm-output) block,
the type of the **Clear Alarm** input port has changed from `boolean` to `pulse`.
Thus any existing model which has a Boolean input to the port will now only trigger a clear alarm on the transition
from `false` to `true`, instead of the entire time that the input remains `true`.
If you wish to retain the old behavior, connect a
[Pulse](https://cumulocity.com/guides/10.18.0/streaming-analytics/block-reference/#pulse)
block to the **Clear Alarm** input port to send a sequence of pulse signals into the **Alarm Output** block.
However, it is unlikely that a model should be attempting to clear the same alarm multiple times,
and the previous behavior had the effect of sending numerous unnecessary HTTP requests, potentially causing a drop in performance.
For more information on the `pulse` type and how inputs are converted, see
[The pulse type](https://cumulocity.com/guides/10.18.0/streaming-analytics/analytics-builder/#the-pulse-type) and
[Type conversions](https://cumulocity.com/guides/10.18.0/streaming-analytics/analytics-builder/#type-conversions),
both in the *Streaming Analytics guide*.

##### Changes to microservice health endpoint

For reasons of security and performance, the REST endpoint `/service/cep/health` no longer returns a comprehensive list of status values. All of the same information is still available from REST endpoints under `/service/cep/diagnostics/...`.

##### Removal of application_queue_full alarm type

The `application_queue_full` alarm type has been removed. It has been replaced by three new types of
performance alarms which give a better explanation of why the input and output queues are filling up.
For more details, see the Streaming Analytics release notes for
[release 10.18.0](/release-10-18-0/streaming-analytics-10-18-0).

##### Removal of required roles from the manifest

For security reasons, ROLE_APPLICATION_MANAGEMENT_ADMIN and ROLE_OPTION_MANAGEMENT_ADMIN have been
removed from the required roles which are defined in the manifest file of the Apama-ctrl microservice.
Any applications deployed with the Streaming Analytics application (for example, EPL apps) can no longer
perform security-sensitive operations such as application creation or modification of tenant options.

##### Removal of version 1 API of Analytics Builder Block SDK

The deprecated version 1 API of the Analytics Builder Block SDK for writing input and output blocks has been removed.
See also the announcement in the Streaming Analytics release notes for
[release 10.7.0](/release-10-7-0/streaming-analytics-10-7-0/#10_7_0).
Existing blocks that use the version 1 API must be migrated to use the version 2 API.
See [Migrating input and output blocks to the version 2 API](https://github.com/SoftwareAG/apama-analytics-builder-block-sdk/blob/rel/10.18.0.x/doc/150-MigrateInputOutputBlocks.md)
in the Analytics Builder Block SDK documentation on GitHub for more details.

##### Cumulocity IoT transport in Apama 10.15.2

In Apama 10.15.2, user status metrics with names containing labels in the form `{keyA=valueA,keyB=valueB}` are converted
to Prometheus metrics with those labels in the Prometheus labels.
The HTTP server transport now uses this syntax for chains that it creates dynamically.
For example, if you have `somename{key=value}` as part of the user status name,
this is now converted to a Prometheus label on the metric `somename`.
Previously, a user status with a name like this did not appear in Prometheus at all.

As a result, the HTTP server transport, which used to prefix all metrics on its chains for example
with `httpServer_instance_5_`, now prefixes them with `httpServer{chain=5}` instead.
This means, that the resulting metrics now look like this:

`sag_apama_correlator_user_httpServer_metricname{chain=5}`

instead of

`sag_apama_correlator_user_httpServer_instance_5_metricname`

##### Updated events in com.apama.cumulocity in Apama 10.15.3

In Apama 10.15.3, the following events of the `com.apama.cumulocity` package have been updated to improve consistency in error handling:

- `FindAlarmResponseAck`
- `FindEventResponseAck`
- `FindManagedObjectResponseAck`
- `FindMeasurementResponseAck`
- `FindOperationResponseAck`
- `FindTenantOptionsResponse`

In all cases of a server acknowledgement, the response is now sent to the correct response channel.
The above events now have the following additional members:

- `boolean error` - Set to true if the find request received either an error content type or an error response HTTP return code.
- `integer status` - The HTTP return code.
- `string errorDetails` - Details of the error.

You should now use the updated events listed above instead of the `Error` event since it is sent to the same channel as the updated events.
For more details, see the [API Reference for EPL (ApamaDoc)](https://documentation.softwareag.com/pam/10.15.3/en/webhelp/related/ApamaDoc/index.html).

<a name="data-hub"></a>
### Cumulocity IoT DataHub

#### Planned

##### Breaking change in the offloading mechanism - switch to new table format may introduce incompatibilities

In future versions of Cumulocity IoT DataHub the offloading mechanism may leverage the Apache Iceberg table format, which is an open format supported by Dremio. Due to this change, current offloading configurations may fail. For example, they may fail due to rarely used data types which are incompatible with the Apache Iceberg format. Further details and remediation actions will be provided closer to the respective release.

##### Breaking change in the offloading mechanism - removal of history column in alarms collection

In a future version of Cumulocity IoT core the history attribute will be removed from the alarms. Therefore, Cumulocity IoT DataHub does no longer include this column in newly defined offloadings. If alarms data has been already offloaded into the data lake, the Dremio table associated with that data lake table still includes the history column, but for new offloading runs the value will not be included anymore and thus will be null.

##### Deprecation of embedded PowerBI reports

In a future version of Cumulocity IoT DataHub the access to PowerBI reports from within the Cumulocity IoT DataHub UI may be removed. Comparable functionality can be integrated into the core UI based on a PowerBI extension.

##### Deprecation of HDFS support

In a future version of Cumulocity IoT DataHub the use of HDFS as data lake will be removed. Instead of HDFS, one of the other supported data lakes must be used.

#### Implemented

##### Deprecation of support for mixed types

As announced with [release 10.15](/release-10-15-0/announcements-10-15-0), Cumulocity IoT DataHub removes support for mixed data types. Cumulocity IoT DataHub will not be able to read data lake tables containing mixed data types, so corresponding data needs to be converted.


### Device Management

#### Planned

##### Deprecation of LWM2M device audit configuration

The [LWM2M device audit configuration](https://cumulocity.com/guides/10.18.0/protocol-integration/lwm2m/#audit-configuration) feature is deprecated. The feature is usable as is in release 10.18. However, it will be disabled by default in a future version.

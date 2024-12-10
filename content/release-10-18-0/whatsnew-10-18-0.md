---
weight: 10
title: What´s new
layout: bundle
---

Release 10.18.0 includes the following new features or major feature enhancements.

### Application enablement

#### Global time selector for dashboards

It is now possible to specify a global time context, which allows users to align all data on a dashboard to the same time range, for example, the last hour or last month. Widget developers can set the global time for the entire dashboard or particular widgets. Users can also share dashboards with a specific time context by including the time context parameters in a URL.

The global time context is currently available for the following widgets: Event list, Data points graph, and Data points table.

![Global time context](/images/release-10-18/global-time-context.png)

For details, see [Cockpit > Working with dashboards > Global time context](https://www.cumulocity.com/guides/10.18.0/users-guide/cockpit/#global-time-context) in the *User guide*.

#### Map feature migration and enhancements

The map feature has been migrated from AngularJS to Angular, bringing along several improvements and modifications.

Breaking change:
* Real-time tracking has been limited to a single device at a time.

New functionalities:

* A device can now be followed in real-time.
* Device icons can be customized.
* Zoom level adjustments have been introduced.
* Center point settings have been added to the map, allowing users to designate a specific point for the map to consistently center on. Users can also utilize their current location as the central point. The map will remain centered on this point when refreshing the page or navigating back from another tab.
* Clustering support has been implemented to handle a high number of devices.
* Devices are now loaded exclusively within the visible map area.
* Refresh polling capabilities have been incorporated for multiple devices, allowing users to adjust the polling interval as needed.

![Map widget improvements](/images/release-10-18/map-widget-improvements.png)

#### Improvements in the Packages/Extensions view

The **Packages** tab has been renamed to "Extensions" and added to the navigator as a new separate entry.

The new **Extensions** page has been improved with additional information about package types, offering better transparency about their origin and trustworthiness:
  * OFFICIAL: Packages coming from the official Cumulocity GitHub Enterprise.
  * COMMUNITY: Packages provided by connected partner repositories.
  * UNKNOWN: Packages with unknown origins, for example, uploaded to the management system with an unknown label.
  * CUSTOM: Custom packages uploaded to the tenant by a user.

![Package labels](/images/release-10-18/package-labels.png)

For more information, see [Administration > Managing applications > Extensions](https://www.cumulocity.com/guides/10.18.0/users-guide/administration/#extensions) in the *User guide*.

Moreover, when deploying a blueprint or installing a plugin, users are now informed about the license that must be approved before proceeding further.

![Accept license](/images/release-10-18/accept-license.png)

### Authentication

#### Authentication with OAuth2 access tokens from authorization servers

On top of standard SSO, Cumulocity IoT now also allows you to access the platform resources using access tokens from third-party authorization server directly as a Bearer token. The SSO configuration has been extended accordingly. For details refer to [Administration > Configuring single sign-on > Configuring authentication with OAuth2 access tokens from authorization servers](https://cumulocity.com/guides/10.18.0/users-guide/administration/#configuring-authentication-with-oauth2-access-tokens-from-authorization-servers) in the *User guide*.

#### Single sign-on integration enhancements

SSO users are now able to have inventory roles assigned. Dynamic access mapping has been extended with the ability to map inventory roles based on token claims. The configuration defined for standard access mapping is also applied when mapping inventory roles.

For details refer to [Administration > Configuring single sign-on](https://cumulocity.com/guides/10.18.0/users-guide/administration/##configuring-single-sign-on) in the *User guide*.


### Device Management

#### Fully customizable Home page

The Home page of the Device Management application now represents a fully customizable dashboard, which allows users to adapt it to their needs and to accommodate any KPI or metric. Based on the same underlying module as the Cockpit dashboard, users can now experience the same functionality including all available widgets as well as the same look and feel in the Device Management application.

![Customizable Home Page](/images/release-10-18/devmgmt-home-custom.png)

#### LWM2M external decoder event execution improvements

The forwarding of device data to LWM2M decoder microservices (LWM2M device protocol [custom actions](https://cumulocity.com/guides/10.18.0/protocol-integration/lwm2m/#custom-actions)) has been significantly improved. By using a different internal mechanism to parallelize decoder invocations, the received data is now passed to the microservices at a faster rate, which reduces the decoding delay.

#### Device deletion improvement for the LWM2M devices

Starting from version 10.18.0, a LWM2M device can be completely deleted from the [All devices](https://cumulocity.com/guides/10.18.0/users-guide/device-management/#viewing-devices) list. The additional execution of the LWM2M connector device operation called `cleanupClusterTenantObjectsForEndpoint` is no longer required in order to re-register the same device.
Deletion of the LWM2M device is done in the same way as any other device deletion, that is, via the delete icon in the respective row.

#### OPC UA thin-edge.io integration

The OPC UA device gateway can also be registered and operated via [thin-edge.io](https://thin-edge.io/). For more details about how to enable this, follow the instructions in [OPC UA > Thin Edge](https://cumulocity.com/guides/10.18.0/protocol-integration/opcua/#thin-edge) in the *Protocol integration guide*.

---
weight: 12
title: Important announcements
layout: bundle
---

### REST API changes

#### Feature preview:  Latest measurement values can be stored as part of a device managed object

{{< c8y-admon-important >}}
The feature is in Public Preview mode, that is, it is not enabled by default and maybe subject to change in future.
{{< /c8y-admon-important >}}

Starting from this release we introduce the support of automated persistence of measurement values under the `c8y_LatestMeasurements` fragment.

##### How to enable it

Use the tenant options to create a category named `measurement.series.latestvalue` with a PUT request to a [tenant options category](https://cumulocity.com/api/core/#operation/putCategoryOptionResource).
Example:
```
PUT /tenant/options/measurement.series.latestvalue
{
  "c8y_Humidity.H":"", // to enable single series c8y_Humidity.H
  "c8y_Temperature.*":"", // to able series under fragment c8y_Temperature
  // or "*":"" to enable all
}
```
where the key is a filter of measurement series that must be persistent and its value must always be an empty string (left for a future use case).

##### How it works

If a measurement is created with a series that matches the configuration the device managed object
is updated with the last series sent to the platform.
Example:

If you send
```
POST /measurement/measurements
{
  "source":"5413"
  "time":"2024-02-01T10:00:00Z"
  "c8y_Temperature":{
     "T": {
        "value": 15,
        "unit":"C"
     }
  }
  "c8y_Speed":{
    "S": {
      "value": 15,
      "unit":"m/s"
    }
  }
}
```
then,  considering the example configuration, only `c8y_Temperature.T` is stored as part of the device, while `c8y_Speed.S` is ignored.
This means, that the measurement is stored like before, only the state update is skipped.
To read the latest values on device level you must use the Inventory API.
To get a single device:
```
GET /inventory/managedObjects/5413?withLatestValues=true
{
   ...
   "c8y_LatestMeasurements":{
        "c8y_Temperature":{
           "T":{
             "value":15,
             "time":"2024-02-01T10:00:00Z",
             "unit":"C"
           }
        }
   }
}

```
To get a list of devices matching the expected criteria,
for example, get all devices which have a reported temperature higher than 10 degrees:

```
GET /inventory/managedObjects?withLatestValues=true&query=$filter=c8y_LatestMeasurements.c8y_Temperature.T.value+gt+10
{
  managedObjects: [
    {
        ...
        "c8y_LatestMeasurements":{
            "c8y_Temperature":{
                "T":{
                    "value":15,
                    "time":"2024-02-01T10:00:00Z",
                    "unit":"C"
                }
            }
        }
    }
  ]
}

```
##### Implications

This feature introduces an additional operation upon measurement creation.
This results in performance degradation, depending on the number of series to be
stored in each measurement, reaching from 5% for single series in each measurement to
more than 20% in case of 50 series per measurement.

##### Limitations

**Security**

The latest measurement values are part of the managed object and they follow the managed object inventory role permissions instead of respecting the inventory roles for measurements.

**Data model**

The latest measurements do not store the measurement type. This information
can be obtained using the Measurements API.

**Last value**

The value stored in the device managed object is the last value sent to the platform.
If the order of measurement delivery to the platform is different from the measurement creation time
then also that latest values will be affected.



#### Planned

#### Implemented

##### Breaking change in the Alarms, Events, Measurements APIs - required parameters will be introduced

As announced earlier, most recently with [release 10.17](/release-10-17-0/announcements-10-17-0), at least one query parameter limiting the affected data will be required to prevent accidental deletion of too many objects during a bulk delete operation.
This change affects the following APIs:

* `DELETE /alarm/alarms` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`, `createdFrom`, `createdTo`
* `DELETE /event/events` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`, `createdFrom`, `createdTo`
* `DELETE /measurements/measurement` requires at least one of the following parameters: `source`, `dateFrom`, `dateTo`



### SDK changes

#### Planned

#### Implemented

##### Removal of deprecated device-grid model classes, column implementations and services

As announced earlier, most recently with [release 10.17](/release-10-17-0/announcements-10-17-0), shared classes, components and services from the @c8y/ngx-components/device-grid are deprecated. Those deprecated items are removed with this release.

This change only affects you, if you or your development team use the Web SDK to extend Cumulocity IoT UI applications or to build your own web applications. If you use the device-grid functionalities, check the deprecation documentation and alter your code accordingly. Refer to the deprecations in the [WebSDK resources documentation for the device-grid service](http://resources.cumulocity.com/documentation/websdk/ngx-components/injectables/DeviceGridService.html). Other deprecations for reference are also marked in this documentation.

### Streaming Analytics

#### Planned

#### Implemented

##### Cumulocity IoT transport in Apama 10.15.4 - breaking change in REST APIs

Due to a change in Cumulocity IoT announced with [release 10.17](/release-10-17-0/announcements-10-17-0), Apama 10.15.4 now explicitly sets `withTotalPages` to `true` for most requests.

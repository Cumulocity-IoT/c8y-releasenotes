---
title: Predictive Analytics
layout : bundle
weight : 80

aliases:
  - /predictive-analytics/zementis-release-notes/#10_5_7
---

### Build 10.5.7.0.40 

* Enable ingestion of time series data: 
  - Given any time series data, users can generate ARIMA models.
  - This functionality will be backed by REST endpoints offering users the option to invoke them from any client.
* Enhanced support for time series:
  - Representation and computation of confidence intervals as an output for time series models.
  - Added support for Kalman Filters for the "exactLeastSquares" prediction method.
* Modification of charts for scheduled data processing:
  - The histogram is replaced with a line chart.
  - Timestamps pertaining to every measurement is part of the line chart.
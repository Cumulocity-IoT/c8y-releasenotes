---
title: Machine Learning
layout : bundle
weight : 80

aliases:
  - /predictive-analytics/zementis-release-notes/#10_5_0
---

### Build 10.5.0.0.40

* Support analysis of device data using scheduled jobs.
  * Schedule batch jobs for processing measurements from devices or device 
	groups against an available model.
  * The job scheduler can be used to trigger one-time or periodic jobs on 
	data captured from devices. 
  * The scheduler allows you to map device data to model inputs by providing 
	a mapping tool. Periodic executions of batch jobs can be useful when 
	aggregate information on model’s predictions is required for a desired 
	time period.
* Support for Object Detection using RetinaNet.
* Support for univariate seasonal ARIMA time series models with "conditionalLeastSquares" prediction method.
* Compliance with PMML 4.4 release features:
  * Support for Lag@aggregation=stddev
  * Support for Constant@missing
  * Support for Anomaly Detection release schema
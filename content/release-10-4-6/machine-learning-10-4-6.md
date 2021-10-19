---
title: Predictive Analytics
layout : bundle
weight : 80

aliases:
  - /predictive-analytics/zementis-release-notes/#10_4_6
---

### Build 10.4.6.0.8

* Embedded Zementis engine within a microservice package for Cumulocity IoT. 
  * Minimized footprint for the Zementis microservice along with improved performance.
  * Comprehensive REST API for model and resource management and data processing.
  * Intuitive web application which leverages the REST API of the Zementis microservice.
* Added support for following capabilities -
  * Upload/delete/download of PMML models and custom resources to Cumulocity IoT. 
	Users can also upload compressed PMML file (ZIP). The PMML file needs to be 
	the only entry in the ZIP file.
  * Enabled batch processing of data against available models on Cumulocity IoT. 
	Users can even upload compressed CSV/JSON/Image files (ZIP) for batch processing.
  * Visualize Model KPIs including memory metrics and prediction metrics. 
	These metrics help users to view the memory footprint of every model that 
	they have and also summarize the real-time predictions from each model.
  * Model activation/de-activation at runtime. De-activation evicts the model from 
	JVM heap, allowing the Zementis Microservice to manage more models than available heap.
* Support PMML 4.3 standard.
* Added compliance with upcoming PMML 4.4 release features:
  * Support for multiple model methods "weightedSum" and "weightedMedian"
  * Support for new MiningField@invalidValueReplacement attribute
  * Support for new Lag@aggregate attribute
  * Support for new built-in Functions: isValid, isNotValid, modulo(%), sin,
    asin, sinh, cos, acos, cosh, tan, atan, tanh, rint, hypot, ln1p, expm1
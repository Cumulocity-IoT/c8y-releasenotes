---
title: Machine Learning
layout : bundle
weight : 80

aliases:
  - /predictive-analytics/zementis-release-notes/#10_6_0
---

### Build 10.6.0.1.1 

* Support for ONNX models:
  - Dedicated microservice which allows deployment and management of ONNX models.
  - Updated UI to enable ONNX model management and inferencing.
* Support for Multi-Variate State Space Models:
  - Representation and computation of confidence intervals as an output for state space models.
  - Support for multi-valued output for multi-step forecasts and confidence intervals.
* Enable grouping of multiple models:
  - Models with homogenous input/output schema can be added to a logical group.
  - Inferencing can be done on a model group – one or more models at the same time.
  - Grouping also allows combining multiple versions of same model into a logical group.
* Performance improvements to the job scheduling feature by including pagination.

---
layout: post
title: Azure Durable Functions - Short Guide
subtitle:
tags: [azure, durable functions]
---
### Introduction 
People who just came across Azure Functions may don't even know that there is another, more powerful version of this service - **Durable Functions**. The main difference is the workload that we can pass to the function. According to the [documentation](https://docs.microsoft.com/pl-pl/azure/azure-functions/functions-scale), for the most common HTTP-triggered functions the maximum time of waiting for response to the request is 230 seconds, which can be not enough for more complex cases. Thanks to [asynchronous pattern](http://dontcodetired.com/blog/post/Understanding-Azure-Durable-Functions-Part-9-The-Asynchronous-HTTP-API-Pattern), **durable functions don't have such time limit**. The second difference (and very useful feature) is **Orchestrator**, which allows us to chain the set of multiple functions and execute them in desired order, which makes them very useful for multi-step data processing. The service cost is related to the workload, but it is considered very cheap.


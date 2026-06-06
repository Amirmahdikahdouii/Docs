---
title: "Distributed Tracing"
---
# Distributed Tracing

## Sources

| Title | Link |
| --- | --- |
| AWS - Distributed tracing | [link](https://aws.amazon.com/what-is/distributed-tracing/) |

## What is Distributed Tracing?

**Distributed tracing is observing data requests as they flow over distributed systems.**
Modern microservice architecture often has multiple small independent components, these components constantly communicate and exchange data using APIs to do complex works.
Using `distributed tracing` allow developers to trace a request and follow the flow, then `fix bugs`, `improve performance`  or troubleshoot errors.

## What are the benefits of Distributed Tracing?

- **Accelerate software troubleshooting**

Nowadays, modern application uses microservice architecture due to it's benefits rather than monolithic architecture, but troubleshooting in microservices are way more difficult than monolithic applications, because the cause of an issue might not be apparent. Also the complex communication between different software modules can make it difficult to diagnose issues.

Using distributed tracing, engineers can follow the data exchanges between different modules and follow the path that a request take to be complete, and then find the reason of an issue and fix the errors.

- **Improve developer collaborations**

Several developers are often involved in building a cloud application, with each responsible for one or several microservices.
The software development process slows down when a developer don't know how the system is working, and it takes time to find out.
Using distributed tracing developers can easily track and monitor systems behavior and understand the flow, and work faster.

- **Reduce time to market**

Distributed tracing allows organizations to understand users behavior and develop their needs faster.

## What are the different types of distributed tracing?

- **Code tracing**

`Code tracing` is a software process that inspects the flow of source codes in an application when performing a specific function.
It helps developers to understand the logic of code, when they trying to do different actions.

- **Program tracing**

`Program tracing` is a method wherein developers can visually trace addresses that used by running application for variables and instructions.
When a software application runs, it process each line of code that resides in a specific allocated memory space.
By using automated softwares, program tracing can help developers to find deep-rooted problems such as `memory overflow`, `performance issues` and `blocking operations`.

- **End-To-End tracing**

With End-To-End tracing developers can track the data transforming along the service request path.

## Distributed tracing components

- **Span**

When processing a request, an application might take several interactions.
These actions are represented as `span` in distributed tracing. Some spans could be `user authentication`, `enable storage access`, etc.
If a single request results in several actions, the initial or parent span may branch into several child spans.

- **Trace ID**

The distributed tracing systems assigns a unique ID to every request in order to track it.
Each span inherit the same `trace ID` from the original request it belongs to. Spans are also tagged using a unique `Span ID` that helps the tracing system consolidate the metadata, logs and metrics it collect.

- **Metric collection**

As each span passes through different microservices, it appends metrics that provide developers with deep and precise insights into software behavior.
You can collect `error rate`, `timestamps`, `response time`, and other metadata with the span.
After the trace completes an entire cycle, the distributed tracing tool consolidates all data collected.

> [!NOTE]
> An API call is evaluated with `response time`, `error status`, and break down of secondary functions fulfilled by multiple third-party services.
> The tracing tool turns the data into `visual forms`, highlighting key indicators and performance summary.
> This will help `SREs` to identify errors and performance issues and ensure compliance with `Service Level Agreements (SLAs)`.

## What are distributed tracing standards?

Distributed tracing standards provide a common framework and software tools for developers to implement tracing system easily.
These standards `monitor`, `visualize` and `analyze` service requests in modern application environments.

- **OpenTracing**

`OpenTracing` is an open source distributed tracing standard developed by **Cloud Native Computing Foundation** (**CNCF**).
OpenTracing focuses on enabling developers to generate traces with an instrumentation and detailed API.
This will allow developers to generate distributed traces from different parts of the code base.

- **OpenCensus**

`OpenCensus` consists of multi-language libraries capable of extracting software metrics and sending them to backend systems for analyze.
**Developers can manage how traces are generated using provided API**.
Unlike `OpenTracing`, developers work with OpenCensus from a single project repository instead of individual code bases and libraries.

- **OpenTelemetry**

**OpenTelemetry** unifies `OpenTracing` and `OpenCensus`. It combines the best features of both standards to provide a better framework.
`OpenTelemetry` provides extensive software development kits, APIs, libraries, and other instrumentation tools for implementing distributed tracing more effortlessly.

![Opentelemetry Image](/images/distributed-tracing-opentelemetry.png)

> [!IMPORTANT]
> **What is the difference between distributed tracing and logging?**
> Logging is a practice of recording specific events that occur when an application runs.
> Logging tools collect timestamp events such as `system errors`, `user interactions`, `communication status`, etc, to help development team to find anomalies and fix bugs.
> Generally, there are 2 types of logging:
> 1. Centralized logging collects all recorded activities and store them is a single location
> 2. Distributed logging store log files in different locations on the cloud
> Both `Logging` and `tracing` provide a set of statistics and metadata that helps development process.
> In contrast, distributed tracing provide a way to find out why an error occurred by correlating various telemetry data collected throughout a service request periods.
> Distributed tracing may use logging and other data collection methods for tracing a specific service request.


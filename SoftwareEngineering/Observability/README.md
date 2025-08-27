# Software Observability

For debugging, and monitoring an application and make it observer, three main concepts should be implemented in the code:

1. Logs
2. Traces
3. Metrics

## What is observability?

**Observability** lets you understand a system from the outside by letting you ask questions about that system without knowing its inner workings. 

> [!IMPORTANT]
> An application is properly instrumented when developers don’t need to add more instrumentation to troubleshoot an issue, because they have all of the information they need.
> These instrumentations are **logs**, **metrics** and **traces**.

## What is telemetry?

**Telemetry** refers to data emitted from a system and its behavior. The data can come in the form of traces, metrics, and logs.

### What is OpenTelemetry?

**Opentelemetry** is like a standard for exporting signals: (**logs**, **metrics** and **traces**) in an application. It's a framework that lets you export and persist data in a standard format.

## Reliability and metrics

**Reliability** is what you expect from service to responde to a user. A system could be up 100% of the time, but if, when a user clicks “Add to Cart” to add a black pair of shoes to their shopping cart, the system doesn’t always add black shoes, then the system could be unreliable.

**Metrics** on the other hand, are **aggregations over a period of time of numeric data** about your infrastructure or application.

**SLI**, or **Service Level Indicator**, represents a measurement of a service’s behavior.
A good SLI measures your service from the perspective of your users. An example SLI can be the speed at which a web page loads.

**SLO**, or **Service Level Objective**, represents the means by which reliability is communicated to an organization/other teams.
This is accomplished by attaching one or more SLIs to business value.

## Distributed tracing

**Distributed tracing** lets you `observe requests` as they propagate through complex, distributed systems.
**Distributed tracing** improves the visibility of your application or system’s health and lets you debug behavior that is difficult to reproduce locally.

Distributed tracing has three main components:

- Logs
- Spans
- Traces

### Logs

A log is a **timestamped message** emitted by services or other components.
Unlike *traces*, they aren’t necessarily associated with any particular user request or transaction.

> Logs have been heavily relied on in the past by both developers and operators to help them understand system behavior.

> [!IMPORTANT]
> Logs aren’t enough for tracking code execution, as they usually lack contextual information, such as where they were called from.
> They become far more useful when they are included as part of a span, or when they are correlated with a trace and a span.

### Spans

A **span** represents a unit of work or operation.
**Spans track specific operations that a request makes, painting a picture of what happened during the time in which that operation was executed.**

A span contains **name**, **time-related data**, **structured log messages**, and other metadata (that is, Attributes) to provide information about the operation it tracks.

#### Span attributes

Span attributes are metadata attached to a span.

| Key | Value |
| --- | --- |
| http.request.method  | "GET" |
| network.protocol.version  | "1.1" |
| url.path | "/webshop/articles/4" |
| url.query | "?s=1" |
| server.address | "example.com" |
| server.port | 8080 |
| url.scheme | "https" |
| http.route | "/webshop/articles/:article_id" |
| http.response.status_code | 200 |
| client.address | "192.0.2.4" |
| client.socket.address | "192.0.2.5" (the client goes through a proxy) |
| user_agent.original | "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:72.0) Gecko/20100101 Firefox/72.0" |

## Distributed Traces (AKA: traces)

A **distributed trace**, more commonly known as a **trace**, records the paths taken by requests (made by an application or end-user) as they propagate through multi-service architectures, like microservice and serverless applications.

**A trace is made of one or more spans.**
The first span represents the `root span`. Each **root span** represents a request from **start to finish**. The spans underneath the parent provide a more in-depth context of what occurs during a request (or what steps make up a request).

Without tracing, finding the root cause of performance problems in a distributed system **can be challenging**.

---

> [!NOTE]
> The above documentation was from reading [this document](https://opentelemetry.io/docs/concepts/observability-primer/).

---

## Context propagation

With **context propagation**, signals can be correlated with each other, regardless of where they are generated.

To understand context propagation, you need to understand two separate concepts: context and propagation.

### Context

**Context** is an object that contains the information for the sending and receiving service, or execution unit, to correlate one signal with another.

> [!NOTE]
> When Service A calls Service B, it includes a **trace ID** and a **span ID** as part of the context. Service B uses these values to create a **new span** that **belongs to the same trace**, setting the span from Service A as its **parent**.
> This makes it possible to track the full flow of a request across service boundaries.

### Propagation

**Propagation** is the mechanism that moves context between services and processes. It serializes or deserializes the context object and provides the relevant information to be propagated from one service to another.

Propagation is usually handled by instrumentation libraries and is transparent to the user.

---

> [!NOTE]
> The above documentation was from reading [this document](https://opentelemetry.io/docs/concepts/context-propagation/).

---

## Signals

The purpose of `OpenTelemetry` is to **collect**, **process**, and **export** `signals`.
**Signals** are system outputs that describe the underlying activity of the operating system and applications running on a platform.

### Traces

> The path of a request through your application.

Traces give us the big picture of what happens when a request is made to an application. Whether your application is a monolith with a single database or a sophisticated mesh of services, traces are essential to understanding the full “path” a request takes in your application.

In a trace example like this:

```json
{
  "name": "hello",
  "context": {
    "trace_id": "5b8aa5a2d2c872e8321cf37308d69df2",
    "span_id": "051581bf3cb55c13"
  },
  "parent_id": null,
  "start_time": "2022-04-29T18:52:58.114201Z",
  "end_time": "2022-04-29T18:52:58.114687Z",
  "attributes": {
    "http.route": "some_route1"
  },
  "events": [
    {
      "name": "Guten Tag!",
      "timestamp": "2022-04-29T18:52:58.114561Z",
      "attributes": {
        "event_attributes": 1
      }
    }
  ]
}
```

There is a `trace_id` and `span_id` in the `context` field, which will reprecent this trace as a unique object.
If a request comes into your application, the root function that is going to parse the request, will create a trace object, that is a `root trace`.

#### How we can specify the root trace?

The traces has a `parent_id` field, which will define a trace as a child one or a root one. If the value of this field be `null`, it means that you have a root trace, otherwise it's a child trace.

- **trace_id**:

For each request, for every trace that is generated during the responding to a request, will have a shared **trace_id** which will correlated them to a request and group them, which means that this group of traces are created during a single work or job.

- **span_id**:

`span_id` are unique in the tracing process, which will be separate each trace from one the other.
The `parent_id` will store the `span_id` of the parent trace.


### Tracer Provider

A `Tracer Provider` (sometimes called TracerProvider) is a **factory for Tracers**. In most applications, a Tracer Provider is initialized once and its **lifecycle matches the application’s lifecycle**.
Tracer Provider initialization also includes **Resource** and **Exporter** initialization.


### Tracer

A **Tracer** creates `spans` containing more information about what is happening for a given operation, such as a request in a service.
Tracers are created from Tracer Providers.

### Trace Exporters

**Trace Exporters** send traces to a **consumer**.
This consumer can be standard output for debugging and development-time, the OpenTelemetry Collector, or any open source or vendor backend of your choice.

### Context Propagation

**Context Propagation** is the core concept that enables `Distributed Tracing`.
With Context Propagation, Spans can be **correlated** with each other and assembled into a trace, regardless of where Spans are generated.

## Spans

A `span` represents a unit of work or operation. Spans are the building blocks of Traces.
In Open telemetry spans include these informations:

- Name
- Parent span ID (empty for root spans)
- Start and End Timestamps
- Span Context
- Attributes
- Span Events
- Span Links
- Span Status

> [!NOTE]
> Spans can be **nested**, as is implied by the presence of a **parent span ID**: child spans represent **sub-operations**. This allows spans to more accurately capture the work done in an application.

### Span Context

Span context is an immutable object on every span that contains the following:

- The `Trace ID` representing the trace that the span is a part of
- The span’s `Span ID`

### Span Attributes

Attributes are **key-value pairs** that contain `metadata` that you can use to **annotate** a Span to carry information about the operation it is tracking.

You can add attributes to spans **during** or **after** span **creation**.
**Prefer adding attributes at span creation** to make the attributes available to SDK sampling.
If you have to add a value after span creation, update the span with the value.

#### Span attributes rules

- Keys must be **non-null** string values
- Values must be a **non-null** string, boolean, floating point value, integer, or an array of these values

> [!IMPORTANT]
> There are many standard attributes, for different environemts.
> The list of attributes are available [here](https://opentelemetry.io/docs/specs/semconv/general/trace/).

### Span Events

A **Span Event** can be thought of as a **structured log message** (or annotation) on a `Span`.

#### When to use span events versus span attributes?

When you’re tracking an **operation** with a `span` and the operation completes, you might want to add data from the operation to your telemetry.

- If the timestamp in which the operation completes is meaningful or relevant, attach the data to a span event.
- If the timestamp isn’t meaningful, attach the data as span attributes.

### Span Links

Links exist so that you can **associate one span with one or more spans**, implying a causal relationship.

A **Span** may be linked to **zero or more other Spans** (defined by **SpanContext**) that are causally related.
Links can point to Spans inside a single Trace or across different Traces.
Links can be used to represent batched operations where a Span was initiated by multiple initiating Spans, each representing a single incoming item being processed in the batch.

### Span status

Each span has a status. The three possible values are:

- Unset
- Error
- Ok

The default value is `Unset`. A span status that is **Unset** means that the operation it tracked **successfully completed without an error**.

When a span status is `Error`, then that means some **error occurred in the operation it tracks**.
For example, this could be due to an `HTTP 500` error on a server handling a request.

When a span status is `Ok`, then that means the span was **explicitly marked as error-free by the developer of an application**.
> Although this is unintuitive, it’s not required to set a span status as `Ok` when a span is known to have completed without error, as this is covered by **Unset**. 

> [!IMPORTANT]
> **To reiterate**: `Unset` represents a span that **completed without an error**.
> `Ok` represents when a developer **explicitly marks a span as successful**.
> **In most cases, it is not necessary to explicitly mark a span as Ok.**

---

> [!NOTE]
> The above documentation was from reading [this document](https://opentelemetry.io/docs/concepts/signals/traces/).

---

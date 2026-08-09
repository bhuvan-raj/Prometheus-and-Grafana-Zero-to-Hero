# Prometheus Data Model

Prometheus stores monitoring data as **time series**.

Each time series is uniquely identified by:

1. A **metric name**
2. A set of **labels**

## Metric Name

The metric name describes the general characteristic being measured.

Examples:

```text
http_requests_total
node_cpu_seconds_total
```

## Labels

Labels provide dimensions that allow metrics to be filtered and aggregated.

Example:

```text
http_requests_total{method="post", handler="/users"}
```

The metric name and label set together identify a unique time series.

## Why Labels Matter

Labels make it possible to query metrics by dimensions such as:

- HTTP method
- Endpoint
- Status code
- Environment
- Service
- Instance

This multi-dimensional data model is one of Prometheus's core strengths.

## Conceptual Example

```text
http_requests_total
├── method="GET", endpoint="/users"
├── method="POST", endpoint="/users"
├── method="GET", endpoint="/orders"
└── method="POST", endpoint="/orders"
```

PromQL can then filter, aggregate, and analyze these time series.

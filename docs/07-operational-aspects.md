# Prometheus Operational Concepts

## Configuration

Prometheus is configured using YAML files.

Configuration can define:

- Scrape targets
- Alerting rules
- Recording rules
- Service discovery

## Recording Rules

Recording rules pre-compute frequently used or expensive PromQL expressions and save the result as a new time series.

This can:

- Improve query performance
- Reduce repeated computation
- Reduce load on the PromQL engine

Conceptually:

```text
Expensive PromQL expression
          ↓
    Recording Rule
          ↓
 Precomputed time series
```

## Alerting Rules

Alerting rules define conditions using PromQL expressions.

When a condition is met, Prometheus sends the resulting alert to Alertmanager.

```text
Metric
  ↓
PromQL condition
  ↓
Alerting Rule
  ↓
Alertmanager
  ↓
Notification
```

## Prometheus Operational Stack

```text
Targets
   ↓
Prometheus
 ├── TSDB
 ├── PromQL
 ├── Recording Rules
 └── Alerting Rules
          ↓
    Alertmanager
          ↓
   Notification systems
```

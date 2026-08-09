# Prometheus Pull vs Push Model

Prometheus primarily operates using a **pull model**.

## Pull Model

The Prometheus server actively scrapes metrics from monitoring targets.

```text
Target
  ↑
  │ HTTP /metrics
  │
Prometheus
```

The target exposes metrics, and Prometheus periodically retrieves them.

## Advantages of Pull

### Simplicity
Targets do not need to continuously push metrics to a central server.

### Reliability
Prometheus can determine that a target is unavailable when a scrape fails.

### Debugging
The `/metrics` endpoint can be accessed directly to inspect what Prometheus is scraping.

### Centralized Control
Prometheus controls which targets are monitored through its configuration and service discovery mechanisms.

## When Push is Used

Push is useful for **short-lived or ephemeral jobs** that may finish before Prometheus gets a chance to scrape them.

In such cases, Pushgateway can be used:

```text
Batch Job
   ↓ push
Pushgateway
   ↑ scrape
Prometheus
```

> Prometheus is fundamentally pull-based; Pushgateway is a specific solution for workloads that cannot be scraped directly.

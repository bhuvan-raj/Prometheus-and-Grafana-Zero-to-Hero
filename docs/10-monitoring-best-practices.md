# Application Monitoring Best Practices

## 1. Standardized Metric Naming

Follow Prometheus metric naming best practices.

Use:

- Snake case
- Base units
- Clear descriptions

Example:

```text
http_request_duration_seconds
```

## 2. Use Labels Effectively

Labels add useful context to metrics.

Examples:

```text
method
endpoint
status_code
environment
service_name
```

Good labels make filtering and aggregation in PromQL more powerful.

## 3. Configure Resource Requests and Limits

Kubernetes applications should define resource requests and limits.

This helps applications receive appropriate resources and prevents workloads from consuming excessive resources.

## 4. Configure Alerting

Use Alertmanager to send notifications when important conditions occur.

Examples:

- High error rate
- High latency
- Low available memory
- Other critical infrastructure conditions

## 5. Use Logs and Traces Alongside Metrics

Metrics are one part of observability.

### Logs
Logs provide detailed event information.

Examples of centralized logging solutions:

- Loki
- ELK stack

### Traces
Traces help understand request flow through distributed systems.

Examples:

- OpenTelemetry
- Jaeger
- Tempo

A practical observability model is:

```text
Metrics ──→ Prometheus ──→ Grafana
Logs ─────→ Loki ────────→ Grafana
Traces ───→ Tempo/Jaeger ─→ Grafana
```

## 6. Configure Health Checks

Kubernetes applications should use:

- Liveness probes
- Readiness probes

These help Kubernetes manage application lifecycle and availability.

## Monitoring Checklist

- [ ] Standardize metric names
- [ ] Use meaningful labels
- [ ] Set Kubernetes resource requests and limits
- [ ] Configure alerting
- [ ] Centralize logs
- [ ] Collect distributed traces
- [ ] Configure liveness probes
- [ ] Configure readiness probes

# Prometheus Service Discovery

In dynamic environments, manually configuring every scrape target is impractical.

Prometheus provides **service discovery** mechanisms to dynamically discover monitoring targets.

## Supported Discovery Sources

### Kubernetes
Prometheus can discover:

- Pods
- Services
- Endpoints
- Ingresses

### Consul
Prometheus can integrate with the Consul service catalog.

### Cloud Instances
Prometheus supports discovery mechanisms for cloud environments such as:

- EC2
- GCE
- Azure

### DNS
Targets can be discovered through DNS SRV records.

### File-Based Discovery
Prometheus can read target configurations from local files. This is useful for static targets or custom discovery scripts.

## Why Service Discovery Matters

Microservices and Kubernetes workloads frequently scale up and down.

Instead of manually changing Prometheus configuration every time a workload changes:

```text
New Pod
   ↓
Service Discovery
   ↓
Prometheus discovers target
   ↓
Prometheus scrapes metrics
```

This makes monitoring more suitable for dynamic infrastructure.

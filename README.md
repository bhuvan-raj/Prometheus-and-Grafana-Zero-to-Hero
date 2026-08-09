# Prometheus and Grafana

<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/pg.png" alt="Prometheus and Grafana" />

A practical **Prometheus and Grafana Zero to Hero** learning repository covering Prometheus architecture, exporters, PromQL concepts, alerting, Grafana dashboards, Kubernetes monitoring, and a hands-on deployment using `kube-prometheus-stack`.

## Table of Contents

### Prometheus
1. [What is Prometheus?](docs/01-what-is-prometheus.md)
2. [Prometheus Architecture](docs/02-prometheus-architecture.md)
3. [Prometheus Data Model](docs/03-prometheus-data-model.md)
4. [Pull vs Push Model](docs/04-pull-vs-push.md)
5. [Storage, HA and Long-Term Storage](docs/05-storage-ha-long-term-storage.md)
6. [Service Discovery](docs/06-service-discovery.md)
7. [Operational Aspects](docs/07-operational-aspects.md)

### Grafana
8. [What is Grafana?](docs/08-what-is-grafana.md)

### Hands-on
9. [Install Prometheus and Grafana on Kubernetes](docs/09-kubernetes-monitoring-lab.md)
10. [Application Monitoring Best Practices](docs/10-monitoring-best-practices.md)

## Learning Path

```text
Prometheus Basics
       ↓
Architecture
       ↓
Data Model
       ↓
Pull / Push
       ↓
Storage & HA
       ↓
Service Discovery
       ↓
Operational Concepts
       ↓
Grafana
       ↓
Kubernetes Monitoring Lab
       ↓
Monitoring Best Practices
```

## Repository Structure

```text
.
├── README.md
├── assets/
└── docs/
    ├── 01-what-is-prometheus.md
    ├── 02-prometheus-architecture.md
    ├── 03-prometheus-data-model.md
    ├── 04-pull-vs-push.md
    ├── 05-storage-ha-long-term-storage.md
    ├── 06-service-discovery.md
    ├── 07-operational-aspects.md
    ├── 08-what-is-grafana.md
    ├── 09-kubernetes-monitoring-lab.md
    └── 10-monitoring-best-practices.md
```

> The original single large README has been split into smaller topic-focused documents so each concept can be learned independently.

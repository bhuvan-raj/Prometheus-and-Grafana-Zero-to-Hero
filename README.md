<div align="center">

# 📊 Prometheus & Grafana — Zero to Hero

<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/pg.png" alt="Prometheus and Grafana" width="700" />

**A practical, hands-on learning repository for mastering monitoring and observability with Prometheus and Grafana on Kubernetes.**

![Status](https://img.shields.io/badge/status-active-brightgreen)
![Topics](https://img.shields.io/badge/topics-10-blue)
![Level](https://img.shields.io/badge/level-beginner%20to%20advanced-orange)
![Stack](https://img.shields.io/badge/stack-kube--prometheus--stack-red)

</div>

---

## 🧭 About This Repo

This repository breaks down Prometheus and Grafana into focused, self-contained guides — covering architecture, data modeling, service discovery, alerting, and a full Kubernetes deployment lab using `kube-prometheus-stack`.

> 💡 The original single large README has been split into smaller topic-focused documents so each concept can be learned independently.

---

## 📚 Table of Contents

### 🔥 Prometheus

| # | Topic | Description |
|---|-------|-------------|
| 1 | [Introduction to Monitoring](docs/00-Introduction-to-Monitoring.md) | Introduction to Monitoring and Observability |
| 2 | What is Prometheus?](docs/01-what-is-prometheus.md) | Introduction and core concepts |
| 3 | [Prometheus Architecture](docs/02-prometheus-architecture.md) | Components and how they fit together |
| 4 | [Prometheus Data Model](docs/03-prometheus-data-model.md) | Metrics, labels, and time series |
| 5 | [Pull vs Push Model](docs/04-pull-vs-push.md) | How Prometheus collects data |
| 6 | [Storage, HA and Long-Term Storage](docs/05-storage-ha-long-term-storage.md) | TSDB internals, high availability, remote storage |
| 7 | [Service Discovery](docs/06-service-discovery.md) | Dynamic target discovery mechanisms |
| 8 | [Operational Aspects](docs/07-operational-aspects.md) | Running Prometheus in production |

### 📈 Grafana

| # | Topic | Description |
|---|-------|-------------|
| 9 | [What is Grafana?](docs/08-what-is-grafana.md) | Visualization layer for your metrics |

### 🛠️ Hands-on

| # | Topic | Description |
|---|-------|-------------|
| 10 | [Install Prometheus and Grafana on Kubernetes](docs/09-kubernetes-monitoring-lab.md) | Full lab using `kube-prometheus-stack` |
| 11 | [Application Monitoring Best Practices](docs/10-monitoring-best-practices.md) | Real-world tips for reliable monitoring |
| 12 | [Installing Node Exporter on AWS EC2 Machine](docs/NodeExporter-Installation.md) | Installing NodeExporter and monitoring an aws ec2 server |

---

## 🗺️ Learning Path

```text
 1. Prometheus Basics
        ↓
 2. Architecture
        ↓
 3. Data Model
        ↓
 4. Pull / Push
        ↓
 5. Storage & HA
        ↓
 6. Service Discovery
        ↓
 7. Operational Concepts
        ↓
 8. Grafana
        ↓
 9. Kubernetes Monitoring Lab
        ↓
10. Monitoring Best Practices
```

---

## 📁 Repository Structure

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

---

## ✅ Who This Is For

- Engineers new to monitoring who want a structured, sequential path
- DevOps/SRE folks who need a quick, practical Kubernetes monitoring lab
- Anyone looking for a concise reference on PromQL, service discovery, and Grafana dashboards

---

<div align="center">

**⭐ If this helped you, consider starring the repo!**

</div>

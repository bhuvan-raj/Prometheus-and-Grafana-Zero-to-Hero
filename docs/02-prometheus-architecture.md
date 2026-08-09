# Prometheus Architecture

<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/prometheus.webp" alt="Prometheus Architecture" />

Prometheus is designed for **reliability and scalability**, particularly in dynamic environments such as microservices and cloud-native applications.

Its primary workflow is:

```text
Targets / Applications
        ↓
    Exporters
        ↓
Prometheus Server
   ├── Scraping
   ├── TSDB Storage
   ├── PromQL
   ├── HTTP API
   └── Service Discovery
        ↓
   Alerting Rules
        ↓
   Alertmanager
        ↓
 Notifications
```

## Core Components

### 1. Prometheus Server

The Prometheus server is the heart of the system.

It handles:

- **Scraping** — pulls metrics from instrumented targets over HTTP.
- **Storage** — stores scraped metrics in its local time-series database (TSDB).
- **PromQL Engine** — provides Prometheus Query Language for querying and aggregating metrics.
- **HTTP API** — exposes an API for querying and managing the server.
- **Service Discovery** — discovers monitoring targets dynamically.

### 2. Exporters

Exporters are lightweight agents that expose existing metrics in a Prometheus-compatible format.

Examples:

- **Node Exporter** — CPU, memory, disk I/O, and network metrics from Linux/Unix hosts.
- **cAdvisor** — container resource usage and performance metrics.
- **Kube-State-Metrics** — metrics based on Kubernetes object state.
- **Web Server Exporters** — HAProxy, Nginx, Apache, etc.
- **Cloud Service Exporters** — AWS, Azure, and Google cloud monitoring integrations.
- **Database Exporters** — MySQL, PostgreSQL, Redis, etc.
- **Application-Specific Exporters** — metrics for specific applications.

### 3. Pushgateway

Prometheus primarily uses a pull model. Pushgateway provides an intermediary for ephemeral or batch jobs that cannot be scraped directly.

```text
Short-lived Job
      ↓ push
Pushgateway
      ↑ scrape
Prometheus
```

### 4. Alertmanager

Alertmanager handles alerts sent by Prometheus.

Its key functions include:

- **Deduplication**
- **Grouping**
- **Routing**
- **Silences**

Notifications can be routed to systems such as email, Slack, PagerDuty, and other supported integrations.

### 5. Client Libraries

Applications can use Prometheus client libraries to expose application-specific metrics.

Prometheus client libraries are available for languages such as:

- Go
- Java
- Python
- Ruby
- Node.js

Applications can expose metric types such as counters, gauges, histograms, and summaries.

## End-to-End Flow

```text
Application / Infrastructure
          ↓
       /metrics
          ↓
     Prometheus
          ↓
       PromQL
      ↙      ↘
 Grafana   Alertmanager
```

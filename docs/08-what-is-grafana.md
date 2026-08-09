# What is Grafana?

<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/grafana.png" alt="Grafana" />

Grafana is an **open-source analytics and monitoring tool** designed for visualizing, analyzing, and alerting on data from multiple sources.

Prometheus collects and stores metrics; Grafana connects to data sources and provides dashboards and visualizations.

## Core Concepts

### Dashboards

Dashboards are the main user interface in Grafana.

A dashboard contains one or more panels arranged in a grid.

### Panels

Panels are the building blocks of dashboards.

They can display data as:

- Time series graphs
- Gauges
- Heatmaps
- Stat panels
- Tables

### Data Sources

Grafana supports many data sources.

Data-source plugins handle:

- Connection
- Authentication
- Data retrieval
- Source-specific queries

## Grafana Architecture

| Layer / Component | Description |
|---|---|
| Frontend | TypeScript and React-based UI |
| Backend | Go-based server |
| Plugins | Data source, visualization, and application integrations |
| LGTM Stack | Loki, Grafana, Tempo, and Mimir |
| Microservices | Modern Grafana can use distributed components while also running as a single binary |

## Key Features

- Centralized data access
- Powerful visualizations
- Real-time monitoring
- Data transformation
- Custom alerts
- Templating and variables
- Drilldowns and dashboard links
- User permissions

## Workflow

```text
Data Source
    ↓
Data Source Plugin
    ↓
Query / Transformation
    ↓
Dashboard
    ↓
Panels
    ↓
Visualization
    ↓
Alerts / Sharing
```

## Common Use Cases

- Infrastructure monitoring
- Application monitoring
- Business analytics
- IoT and custom metrics

## Prometheus + Grafana

```text
Applications / Infrastructure
          ↓
      Prometheus
          ↓
       Metrics
          ↓
       Grafana
          ↓
      Dashboards
```

### References

- [Grafana Fundamentals](https://grafana.com/docs/grafana/latest/fundamentals/)
- [Grafana Introduction](https://grafana.com/docs/grafana/latest/introduction/)
- [Grafana Tutorials](https://grafana.com/tutorials/)
- [Grafana Getting Started](https://grafana.com/docs/grafana/latest/getting-started/)

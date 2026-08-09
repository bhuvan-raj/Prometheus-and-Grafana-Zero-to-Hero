# Prometheus Storage, HA and Long-Term Storage

## Local Storage

Prometheus uses a local, on-disk **time-series database (TSDB)**.

### Efficient Storage
The TSDB is optimized for time-series data and efficient writes.

### Block-Based Storage
Data is stored in immutable blocks. New data is initially written to the current head block.

### Compaction
Older blocks are compacted to reduce disk I/O and improve query performance.

### Retention
Retention settings determine how long local data is kept.

## Limitations of a Single Prometheus Server

A single Prometheus server has practical limits for:

- Very large-scale metric ingestion
- Indefinite long-term retention
- Global querying across multiple Prometheus instances
- High availability

Solutions such as federation and remote storage can address these requirements.

## Federation

Federation allows one Prometheus server to scrape selected metrics from another Prometheus server.

Use cases include:

- Aggregating metrics from multiple Prometheus instances
- Hierarchical monitoring
- Centralized monitoring across multiple clusters

```text
Prometheus Cluster A ─┐
Prometheus Cluster B ─┼→ Global Prometheus
Prometheus Cluster C ─┘
```

## Remote Storage

Prometheus can use remote write/read integrations to work with external storage systems.

This enables:

- Long-term storage
- Global query views
- High availability through external systems

## Thanos

Thanos extends Prometheus for long-term storage, high availability, and a global query view.

### Thanos Sidecar
Runs alongside Prometheus and uploads TSDB blocks to object storage such as S3, GCS, or Azure Blob Storage.

### Thanos Store Gateway
Connects to object storage and exposes historical data to Thanos Query.

### Thanos Query
Provides a PromQL-compatible endpoint across connected Prometheus instances and historical object-storage data.

### Thanos Compactor
Compacts older blocks in object storage and supports deduplication.

### Thanos Ruler
Evaluates recording and alerting rules against the Thanos Query layer.

### Persistence
Object storage provides cost-effective long-term persistence.

### High Availability
Multiple Prometheus replicas can run with Thanos Sidecars, while Thanos Query handles deduplication.

## Cortex

Cortex is designed for horizontally scalable, multi-tenant, long-term Prometheus metrics storage.

It uses a distributed architecture containing components such as:

- Distributors
- Ingester
- Storage
- Querier

It can use backends such as object storage, Cassandra, and DynamoDB.

## Mimir

Mimir is a Grafana Labs project inspired by Cortex and focused on scalable, highly available, multi-tenant long-term storage for Prometheus metrics.

It primarily uses object storage such as S3, GCS, or Azure Blob Storage.

## Comparison

| Solution | Main Purpose |
|---|---|
| Federation | Aggregate selected metrics between Prometheus servers |
| Thanos | HA, long-term storage, global query |
| Cortex | Distributed, scalable, multi-tenant metrics |
| Mimir | Large-scale, HA, multi-tenant Prometheus storage |

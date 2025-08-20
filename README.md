# Prometheus and Grafana
<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/pg.png" alt="Banner" />


Prometheus is an open-source monitoring system that collects and stores time-series data from various applications and infrastructure components.
Grafana is an open-source analytics and interactive visualization web application. It doesn't collect data itself but integrates with various data sources, including Prometheus, to create customizable dashboards with charts, graphs, and other visualizations.





## Prometheus Monitoring Tool: In-Depth Teaching Note
<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/dp.jpg" alt="Banner" />


Prometheus is an **open-source monitoring and alerting toolkit** designed specifically for reliability and scalability in dynamic, cloud-native environments such as Kubernetes. Originally developed by SoundCloud in 2012 and later contributed to the CNCF, Prometheus has become the go-to solution for metrics collection and monitoring in modern IT systems.[1]

***
## ARCHITECTURE OF PROMETHEUS
<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/prometheus.webp" alt="Banner" />

The architecture of Prometheus is designed for **reliability** and **scalability**, making it a powerful monitoring solution for dynamic environments like microservices and cloud-native applications. It primarily functions by **scraping metrics** from configured targets at specified intervals, storing them, and then allowing for querying, analysis, and alerting based on those metrics.

### 1. Core Components

The Prometheus ecosystem consists of several core components that work together to provide a comprehensive monitoring solution:

 - a. Prometheus Server
This is the heart of the system. The Prometheus server is a single binary that handles several crucial tasks:

* **Scraping**: It pulls metrics from instrumented targets (e.g., application instances, databases, servers) over HTTP. Targets expose metrics in a specific text-based exposition format.
* **Storage**: It stores all scraped metrics locally on disk in a custom, highly optimized time-series database (TSDB). This storage is append-only for new samples, making writes very efficient. The data is organized in blocks, with older blocks eventually being compacted and retained according to configured retention policies.
* **PromQL Engine**: It provides **PromQL (Prometheus Query Language)**, a powerful and flexible query language for selecting and aggregating time-series data. This allows users to generate ad-hoc graphs, tables, and alerts.
* **HTTP API**: It exposes an HTTP API for querying data and managing the server.
* **Service Discovery**: While not a separate component, the server incorporates various service discovery mechanisms (e.g., Kubernetes, Consul, EC2, DNS) to dynamically discover targets to scrape, eliminating the need for manual configuration in dynamic environments.

- b. Exporters
Exporters are lightweight agents that expose existing metrics from third-party systems in a Prometheus-compatible format. Since Prometheus itself doesn't directly integrate with every system, exporters act as translators.


Examples include:

* **Node Exporter**: Collects system-level metrics (CPU, memory, disk I/O, network) from Linux/Unix hosts.
* **cAdvisor**: Exposes container resource usage and performance metrics.
* **Database Exporters**: For MySQL, PostgreSQL, Redis, etc., exposing database-specific metrics.
* **Application-Specific Exporters**: Many applications (e.g., HAProxy, Nginx) have their own exporters.

- c. Pushgateway
While Prometheus primarily uses a pull model, the **Pushgateway** allows for ephemeral or batch jobs that cannot be scraped directly to expose their metrics to Prometheus. Jobs push their metrics to the Pushgateway, and then the Prometheus server scrapes the Pushgateway. This is particularly useful for short-lived services or batch jobs that may not exist long enough to be scraped directly by Prometheus.

- d. Alertmanager
The **Alertmanager** handles alerts sent by the Prometheus server. Its key functionalities include:

* **Deduplication**: Suppresses duplicate alerts.
* **Grouping**: Groups similar alerts into a single notification.
* **Routing**: Routes alerts to various notification channels (e.g., email, Slack, PagerDuty, VictorOps) based on routing trees defined in its configuration.
* **Inhibition**: Silences alerts based on other active alerts (e.g., if a critical alert is firing, less critical alerts for the same issue can be suppressed).
* **Silences**: Allows users to temporarily silence alerts for planned maintenance or known issues.

- e. Client Libraries
For applications to expose metrics in a format Prometheus can understand, they need to be **instrumented**. Prometheus provides **client libraries** for various programming languages (Go, Java, Python, Ruby, Node.js, etc.). These libraries allow developers to define and expose application-specific metrics (counters, gauges, histograms, summaries) directly within their code.

***

### 2. Data Model

Prometheus stores all data as **time series**. Each time series is uniquely identified by a **metric name** and a set of **key-value pairs called labels**.

* **Metric Name**: Describes the general characteristic of the measured system (e.g., `http_requests_total`, `node_cpu_seconds_total`).
* **Labels**: Provide a rich dimensionality to the metrics, allowing for powerful filtering and aggregation. For example, `http_requests_total{method="post", handler="/users"}` uniquely identifies the total number of HTTP POST requests to the `/users` handler.


This multi-dimensional data model is a core strength of Prometheus, enabling complex queries and flexible monitoring.

***

### 3. Pull vs. Push Model

Prometheus primarily operates on a **pull model**, where the Prometheus server actively scrapes metrics from targets.

* **Advantages of Pull**:
    * **Simplicity**: No need for agents to push data, reducing complexity on the target side.
    * **Reliability**: Prometheus knows when a target is down because it fails to scrape, rather than waiting for an agent to report.
    * **Debugging**: Easier to debug, as you can directly access the `/metrics` endpoint on the target to see what Prometheus is scraping.
    * **Centralized Control**: Configuration of what to monitor lives in Prometheus, not on the targets.

* **When Push is Used**:
    * **Short-lived jobs**: For batch jobs or ephemeral services that don't exist long enough to be scraped directly, the Pushgateway acts as an intermediary.

***

### 4. Storage

Prometheus uses a **local, on-disk time-series database (TSDB)**.

* **Efficient Storage**: Designed for the specific needs of time-series data, offering high compression and efficient writes.
* **Block-based**: Data is stored in immutable blocks. As new data comes in, it's written to the current head block. Periodically, the head block is closed, and new blocks are created.
* **Compaction**: Older blocks are compacted together to reduce disk I/O and improve query performance for historical data.
* **Retention**: Configurable retention policies allow users to specify how long data should be kept.
* **Scalability for a Single Node**: While highly efficient, a single Prometheus server has practical limits on the amount of data it can store and query. For very large-scale, long-term storage or high availability, a **federation** setup or integrating with **remote storage** solutions like Thanos or Cortex is often used.

***

### 5. High Availability and Long-Term Storage

Out of the box, a single Prometheus server is not highly available, and its local storage is not designed for indefinite long-term retention or global views across multiple Prometheus instances. To address these, the community has developed solutions:

### a. Federation
**Federation** allows one Prometheus server to scrape selected metrics from another Prometheus server. This can be used to:

* **Aggregate metrics**: Gather metrics from multiple independent Prometheus instances into a central one.
* **Hierarchical monitoring**: Create a tiered monitoring setup (e.g., per-cluster Prometheus instances federating into a global one).

### b. Remote Storage Integrations
Prometheus can be configured to write (and read, in some cases) its time-series data to and from remote storage systems that implement the Prometheus remote write/read API. This enables:

* **Long-term storage**: Store data beyond the local disk limits.
* **Global query view**: Query data from multiple Prometheus instances from a single endpoint.
* **High availability**: Leverage the HA capabilities of the remote storage system.

Popular remote storage solutions include:

* **Thanos**: Provides a global query view, high availability, and long-term storage for Prometheus instances. It extends Prometheus by adding components like Sidecar, Query, Store, Compactor, and Ruler.
* **Cortex**: A highly scalable, multi-tenant, and long-term storage solution for Prometheus metrics, designed for cloud-native environments.

***

### 6. Service Discovery

In dynamic environments, manually configuring scrape targets is impractical. Prometheus addresses this with robust **service discovery mechanisms**. It can automatically discover targets from various sources:

* **Kubernetes**: Discover pods, services, endpoints, and ingresses.
* **Consul**: Integrate with Consul's service catalog.
* **EC2/GCE/Azure**: Discover instances in cloud environments.
* **DNS**: Resolve targets via DNS SRV records.
* **File-based**: Read target configurations from local files, useful for static targets or integrations with custom discovery scripts.

This dynamic target discovery is crucial for monitoring microservices where instances frequently scale up and down.

***

### 7. Operational Aspects

* **Configuration**: Prometheus is configured using YAML files, defining scrape targets, alerting rules, and recording rules.
* **Recording Rules**: Allow pre-computation of frequently needed or computationally expensive expressions and saving the results as new time series. This improves query performance and reduces the load on the PromQL engine.
* **Alerting Rules**: Define conditions based on PromQL expressions that, when met, trigger alerts that are sent to the Alertmanager.

***

## Conclusion

The architecture of Prometheus, with its robust scraping capabilities, powerful PromQL, flexible data model, and ecosystem of exporters and the Alertmanager, makes it a highly effective and popular choice for monitoring modern, dynamic infrastructures. Its design prioritizes operational simplicity for small to medium deployments while offering clear paths to scalability and high availability for larger, more complex environments through solutions like Thanos and Cortex.




### Why Prometheus?

#### **Cloud-Native by Design**
Prometheus is tailored for microservices and container-based architectures. Its design aligns perfectly with Kubernetes and other scaling platforms, ensuring adaptivity and resilience.

#### **Operational Simplicity**
Service discovery and the pull model mean that even in highly dynamic environments, monitoring targets are managed with minimal manual intervention.

#### **Powerful Data Model**
Rich label-based metrics allow nuanced, high-resolution insights into system performance and behavior.

#### **Strong Querying Capabilities**
PromQL offers deep analytics and flexible queries to uncover trends, correlations, and anomalies.

#### **Robust Alerting**
Integration with Alertmanager ensures effective, manageable, and context-rich alerting.

#### **Vibrant Ecosystem**
A broad, active open-source community fuels continuous development, extensive documentation, plenty of exporters, and third-party integrations.

***

### Summary Table

| Aspect                | Details                                                      |
|-----------------------|-------------------------------------------------------------|
| Data Model            | Time-series metrics with multi-dimensional labels            |
| Query Language        | PromQL—functional, flexible, analytical                     |
| Architecture          | Standalone server, exporters, aggregations, alerting stack  |
| Monitoring Targets    | Applications, hardware, databases, containers, networks     |
| Service Discovery     | Kubernetes, Consul, DNS, EC2, file-based                    |
| Visualization         | Built-in UI, Grafana integration                            |
| Alerting              | Prometheus alert rules + Alertmanager for notifications     |
| Ecosystem             | Extensive exporters, integrations, active community         |

***

checkout official docs for more info: 
[1] https://prometheus.io/docs/introduction/overview/



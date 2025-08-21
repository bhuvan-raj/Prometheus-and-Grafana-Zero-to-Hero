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

### a. Prometheus Server
This is the heart of the system. The Prometheus server is a single binary that handles several crucial tasks:

* **Scraping**: It pulls metrics from instrumented targets (e.g., application instances, databases, servers) over HTTP. Targets expose metrics in a specific text-based exposition format.
* **Storage**: It stores all scraped metrics locally on disk in a custom, highly optimized time-series database (TSDB). This storage is append-only for new samples, making writes very efficient. The data is organized in blocks, with older blocks eventually being compacted and retained according to configured retention policies.
* **PromQL Engine**: It provides **PromQL (Prometheus Query Language)**, a powerful and flexible query language for selecting and aggregating time-series data. This allows users to generate ad-hoc graphs, tables, and alerts.
* **HTTP API**: It exposes an HTTP API for querying data and managing the server.
* **Service Discovery**: While not a separate component, the server incorporates various service discovery mechanisms (e.g., Kubernetes, Consul, EC2, DNS) to dynamically discover targets to scrape, eliminating the need for manual configuration in dynamic environments.

### b. Exporters
Exporters are lightweight agents that expose existing metrics from third-party systems in a Prometheus-compatible format. Since Prometheus itself doesn't directly integrate with every system, exporters act as translators.


Examples include:

* **Node Exporter**: Collects system-level metrics (CPU, memory, disk I/O, network) from Linux/Unix hosts.
* **cAdvisor**: Exposes container resource usage and performance metrics.
* **Kube-State-Metrics**: Listens to the Kubernetes API server and generates metrics based on the state of Kubernetes objects (like Deployments, Pods, Services, ReplicaSets, DaemonSets, Jobs, etc.), providing insights into your cluster's orchestration and health.
* **Web Server Exporters**: Such as HAProxy Exporter, Nginx Exporter, and Apache Exporter, which provide metrics on HTTP requests, connections, and server health.
* **Cloud Service Exporters**: For various cloud providers, like AWS CloudWatch Exporter, Azure Monitor Exporter, or Google Stackdriver Exporter, to pull metrics from cloud-native monitoring services into Prometheus.
* **Database Exporters**: For MySQL, PostgreSQL, Redis, etc., exposing database-specific metrics.
* **Application-Specific Exporters**: Many applications (e.g., HAProxy, Nginx) have their own exporters.

### c. Pushgateway
While Prometheus primarily uses a pull model, the **Pushgateway** allows for ephemeral or batch jobs that cannot be scraped directly to expose their metrics to Prometheus. Jobs push their metrics to the Pushgateway, and then the Prometheus server scrapes the Pushgateway. This is particularly useful for short-lived services or batch jobs that may not exist long enough to be scraped directly by Prometheus.

### d. Alertmanager
The **Alertmanager** handles alerts sent by the Prometheus server. Its key functionalities include:

* **Deduplication**: Suppresses duplicate alerts.
* **Grouping**: Groups similar alerts into a single notification.
* **Routing**: Routes alerts to various notification channels (e.g., email, Slack, PagerDuty, VictorOps) based on routing trees defined in its configuration.
* **Silences**: Allows users to temporarily silence alerts for planned maintenance or known issues.

### e. Client Libraries
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

### Popular External Storage Solutions
Here are the most common and robust solutions for external persistence, especially in Kubernetes environments:

- 1. Thanos
Thanos is an open-source project that extends Prometheus for long-term storage, high availability, and a global query view. It's an excellent choice for Kubernetes.

Key Components:

Thanos Sidecar: Runs alongside each Prometheus server pod. It uploads (or "pushes") data blocks from Prometheus's local TSDB to object storage (e.g., S3, GCS, Azure Blob Storage) and can also serve data directly from Prometheus's local block storage to the Thanos Query component.

Thanos Store Gateway: Connects to the object storage to expose historical data to the Thanos Query component.

Thanos Query: Provides a single PromQL API endpoint that federates queries across all connected Prometheus servers (via Sidecars) and historical data in object storage (via Store Gateways).

Thanos Compactor: Compacts older blocks in object storage for efficient long-term storage and deduplication.

Thanos Ruler: Can evaluate Prometheus recording and alerting rules against data from the Thanos Query layer, providing global rule evaluation.

Persistence Mechanism: Data is persisted in cost-effective object storage.

High Availability: Achieved by running multiple Prometheus replicas with Thanos Sidecars, and Thanos Query handles deduplication.

- 2. Cortex
Cortex is another robust open-source solution designed for horizontally scalable, multi-tenant, and long-term storage of Prometheus metrics. It's often favored by larger organizations or those building monitoring-as-a-service platforms.

Architecture: Cortex is a distributed system comprising many microservices (e.g., ingesters, distributors, storages, queriers).

Persistence Mechanism: It stores data in various backend databases, including object storage (S3, GCS), Cassandra, or DynamoDB.

Multi-Tenancy: Designed from the ground up to handle metrics from multiple isolated tenants.

Scalability: Built for massive scale, able to ingest and query billions of samples.

High Availability: Inherently highly available due to its distributed nature.

- 3. Mimir
Mimir is a newer project from Grafana Labs, heavily inspired by Cortex but focused on being simpler to operate and offering better performance. It provides massive scalability and long-term storage for Prometheus metrics.

Features: Scalability, high availability, multi-tenancy, and excellent query performance.

Persistence Mechanism: Primarily leverages object storage (S3, GCS, Azure Blob Storage) for long-term persistence.

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




# What is Grafana?

<img src="https://github.com/bhuvan-raj/Prometheus-and-Grafana/blob/main/assets/grafana.png" alt="Banner" />


Grafana is a **powerful open-source analytics and monitoring tool** designed for visualizing, analyzing, and alerting on data from multiple sources. Primarily used for infrastructure and application monitoring, Grafana transforms complex datasets into interactive, visually-rich dashboards that support rapid operational insights and troubleshooting.

### Core Concepts

#### 1. Dashboards and Panels

- **Dashboards**: The main user interface in Grafana, dashboards are made up of one or more panels arranged in a grid. They serve as **centralized hubs** for real-time data visualization and monitoring. 
- **Panels**: The building blocks of dashboards, each displaying data from one or more sources in various formats such as time series graphs, gauges, heatmaps, stat panels, and tables. Panels can be deeply customized through queries, visualization settings, and dynamic field/override options.[5][4]

#### 2. Data Sources and Plugins

- Grafana supports a vast range of **data sources** 
- Each data source is integrated via a **plugin** that handles connection, authentication, and data retrieval, translating Grafana queries into source-specific queries and back into unified data frames for visualization.
- **Plugin architecture** enables extensibility, supporting custom data sources and panel types.[4][5]

***

### Grafana Architecture

| Layer/Component      | Description                                                                                  |
|----------------------|---------------------------------------------------------------------------------------------|
| Frontend (UI)        | Built with TypeScript and React; allows intuitive dashboard creation and management[6].      |
| Backend (Server)     | Written in Go; communicates with data sources using plugins and APIs[6][4].                 |
| Plugins              | Connectors for data sources, panel visualizations, and apps. Extensible for custom needs[5].|
| LGTM Stack           | Refers to integration with Loki (logs), Grafana, Tempo (traces), and Mimir (metrics)[4].   |
| Microservices        | Modern Grafana is designed as distributed microservices, yet still runs as a single binary[4].|

***

### Key Features

- **Centralized Data Access:** Aggregate metrics, logs, and traces from multiple sources in unified dashboards.
- **Powerful Visualizations:** Multiple chart types (time series, heatmaps, gauges, tables, etc.) for deep insights.
- **Real-Time Monitoring:** Dashboards auto-refresh for live system visibility.
- **Data Transformation:** Advanced query and transformation features for shaping data before visualization.
- **Custom Alerts:** Define alert rules on panels to trigger notifications based on thresholds or anomalies.
- **Templating:** Use variables and dynamic panels for flexible, reusable dashboards.
- **Drilldown & Links:** Link between dashboards for both overview and drilldown analyses.[2][7][5]
- **User Permissions:** Manage user roles, folder structures, and dashboard access for team collaboration.

***

### Workflow: From Data Source to Visualization

1. **Data Source Plugin:** Grafana connects to external data sources via plugins, establishing communication and extracting data using custom queries.
2. **Data Querying & Transformation:** In Grafana’s query editor, users can write queries, filter, and manipulate datasets from the source.
3. **Dashboard Composition:** Users lay out dashboards by adding and configuring panels. Each panel executes its configured query, transforms data, and displays the result.
4. **Custom Visualizations:** Panels are customized using thresholds, color mapping, and field overrides to highlight actionable insights or anomalies.
5. **Alerting:** Users set alert rules on queries/panels to receive notifications for critical events or outages.
6. **Sharing & Collaboration:** Dashboards can be shared, exported, imported, or integrated with external systems.

***

### Common Use Cases

- **Infrastructure Monitoring:** Track CPU, memory, disk I/O, and network performance across servers and clusters.
- **Application Monitoring:** Monitor response times, throughput, error rates, and user sessions.
- **Business Analytics:** Display sales metrics, customer engagement, or any key business indicators.
- **IoT & Custom Metrics:** Visualize environmental, industrial, or custom time-series data from sensors and devices.

***



### Summary

Grafana delivers **real-time, interactive, and highly customizable monitoring** and analytics by connecting to disparate data sources, supporting robust visualizations, full alerting, and extensive integration features—all via an open-source, plugin-extensible platform.

- https://grafana.com/docs/grafana/latest/fundamentals/
- https://www.skedler.com/blog/everything-you-need-to-know-about-grafana/
- https://grafana.com/docs/grafana/latest/introduction/
- https://www.igmguru.com/blog/what-is-grafana
- https://kodekloud.com/blog/how-grafana-works/
- https://www.kozhuhds.com/blog/an-easy-look-at-grafana-architecture/
- https://grafana.com/blog/2022/06/06/grafana-dashboards-a-complete-guide-to-all-the-different-types-you-can-build/
- https://grafana.com/tutorials/grafana-fundamentals/
- https://grafana.com/tutorials/
- https://grafana.com/docs/grafana/latest/getting-started/
- https://www.youtube.com/watch?v=lILY8eSspEo
- https://www.youtube.com/watch?v=oPumWaoNw5s
***

checkout official docs for more info: 
- https://prometheus.io/docs/introduction/overview/

# Hands on lab to install Prometheus and Grafana in a Kubernetes Cluster

-----

## Create Your Deployment and Deploy to Kubernetes cluster

**Example `my-app-deployment.yaml`:**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-flask-app
  labels:
    app: my-flask-app
spec:
  replicas: 2
  selector:
    matchLabels:
      app: my-flask-app
  template:
    metadata:
      labels:
        app: my-flask-app
      # Important: Annotations for Prometheus auto-discovery
      annotations:
        prometheus.io/scrape: "true" # Tell Prometheus to scrape this pod
        prometheus.io/port: "5000"   # The port where metrics are exposed
    spec:
      containers:
      - name: my-flask-app-container
        image: bhuvanraj123/monitor-app 
        ports:
        - containerPort: 5000
---
apiVersion: v1
kind: Service
metadata:
  name: my-flask-app-service
  labels:
    app: my-flask-app
spec:
  selector:
    app: my-flask-app
  ports:
    - protocol: TCP
      port: 80 # Service port
      targetPort: 5000 # Target port in the pod
```

Apply this with `kubectl apply -f my-app-deployment.yaml`.

-----

## Set Up Prometheus and Grafana (using `kube-prometheus-stack`)

As covered in the previous interaction, the `kube-prometheus-stack` Helm chart is the recommended way to deploy Prometheus, Grafana, and related components.

1.  **Add Helm Repositories:**
    ```bash
    helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
    helm repo update
    ```
2.  **Install `kube-prometheus-stack`:**
    ```bash
    kubectl create namespace monitoring
    helm install prometheus-stack prometheus-community/kube-prometheus-stack --namespace monitoring
    ```

Prometheus, deployed by the Prometheus Operator within this stack, will automatically discover and scrape metrics from your application pods if they have the `prometheus.io/scrape: "true"` and `prometheus.io/port: "<your-metrics-port>"` annotations. This is a powerful feature of the Prometheus Operator.

-----

##  Access and Visualize in Grafana

1.  **Port-forward Grafana:**
    ```bash
    kubectl port-forward service/prometheus-stack-grafana 3000:80 -n monitoring
    ```
    Access Grafana at `http://localhost:3000`. Get the password as described before:
    ```bash
    kubectl get secret --namespace monitoring prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
    ```
2.  **Explore Data Sources:** Inside Grafana, you'll find that Prometheus is already configured as a data source by the Helm chart.
3.  **Create Dashboards:**
      * Click on the **Dashboards** icon on the left navigation bar.
      * You can import existing dashboards (e.g., for Kubernetes cluster overview, Node Exporter, kube-state-metrics) or create new ones.
      * To visualize your custom application metrics:
          * Create a new dashboard (`+` -\> `Dashboard`).
          * Add a new panel (`Add new panel`).
          * In the **Query** tab, select your Prometheus data source.
          * Enter your PromQL query (e.g., `rate(http_requests_total{app="my-flask-app"}[5m])` for requests per second, or `histogram_quantile(0.99, sum by (le, method, endpoint) (rate(http_request_duration_seconds_bucket{app="my-flask-app"}[5m])))` for 99th percentile latency).
          * Choose appropriate visualizations (Graph, Stat, Gauge, Table, etc.).

-----

## Best Practices for Application Monitoring

  * **Standardized Naming:** Follow Prometheus metric naming best practices (snake\_case, base units, clear descriptions).
  * **Labels:** Use **labels** effectively to add context to your metrics (e.g., `method`, `endpoint`, `status_code`, `environment`, `service_name`). This allows for powerful filtering and aggregation in PromQL.
  * **Resource Requests and Limits:** Always set **resource requests and limits** in your Kubernetes deployments to ensure your applications get the resources they need and don't consume too much, preventing noisy neighbor issues.
  * **Alerting:** Configure **Alertmanager** (included in `kube-prometheus-stack`) to send notifications when critical metrics cross thresholds (e.g., high error rates, long latencies, low available memory).
  * **Logs and Traces:** While metrics provide aggregated insights, remember the other pillars of observability:
      * **Logs:** For detailed event data (use a centralized logging solution like Loki or ELK stack).
      * **Traces:** For understanding the flow of requests through distributed systems (e.g., using OpenTelemetry and Jaeger/Tempo). The `kube-prometheus-stack` can integrate with these.
  * **Health Checks:** Implement Kubernetes **liveness and readiness probes** in your application deployments to help Kubernetes manage your application's lifecycle effectively.

















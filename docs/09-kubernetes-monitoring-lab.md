# Kubernetes Monitoring Lab

This lab installs Prometheus and Grafana in a Kubernetes cluster using the `kube-prometheus-stack` Helm chart.

## Architecture

```text
Kubernetes Cluster
│
├── Application Pod
│     └── /metrics
│
├── Prometheus
│     └── Scrapes application metrics
│
├── Alertmanager
│     └── Handles alerts
│
└── Grafana
      └── Visualizes Prometheus metrics
```

## 1. Create the Application

Example `my-app-deployment.yaml`:

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
      annotations:
        prometheus.io/scrape: "true"
        prometheus.io/port: "5000"
    spec:
      containers:
      - name: my-flask-app-container
        image: nginx
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
      port: 80
      targetPort: 5000
```

Apply it:

```bash
kubectl apply -f my-app-deployment.yaml
```

> Note: The example above preserves the original lab. For a real application, the container should actually expose Prometheus metrics on the configured port.

## 2. Add the Prometheus Community Helm Repository

```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
```

## 3. Create the Monitoring Namespace

```bash
kubectl create namespace monitoring
```

## 4. Install kube-prometheus-stack

```bash
helm install prometheus-stack   prometheus-community/kube-prometheus-stack   --namespace monitoring
```

The stack provides Prometheus, Grafana, Alertmanager, and related monitoring components.

## 5. Access Grafana

Port-forward the Grafana service:

```bash
kubectl port-forward service/prometheus-stack-grafana 3000:80 -n monitoring
```

Open:

```text
http://localhost:3000
```

### Get the Admin Password

The default username is:

```text
admin
```

Retrieve the password:

```bash
kubectl get secret   --namespace monitoring   prometheus-stack-grafana   -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

## 6. Access Prometheus

Port-forward Prometheus:

```bash
kubectl port-forward svc/prometheus-operated 9090:9090 -n monitoring
```

Prometheus can then be accessed locally through port `9090`.

## 7. Create Grafana Dashboards

Inside Grafana:

1. Open **Dashboards**.
2. Import an existing Kubernetes dashboard or create a new one.
3. Add a panel.
4. Select Prometheus as the data source.
5. Enter a PromQL query.
6. Select a visualization such as Graph, Stat, Gauge, or Table.

Example query:

```promql
rate(http_requests_total{app="my-flask-app"}[5m])
```

Example 99th percentile latency query:

```promql
histogram_quantile(
  0.99,
  sum by (le, method, endpoint) (
    rate(http_request_duration_seconds_bucket{app="my-flask-app"}[5m])
  )
)
```

## 8. Optional: Add Grafana's Helm Repository

If installing Grafana separately:

```bash
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

> When using `kube-prometheus-stack`, Grafana is already included in the stack, so a separate Grafana installation is generally unnecessary for this lab.

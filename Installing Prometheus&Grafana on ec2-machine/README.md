This lab manual provides a step-by-step guide to setting up a monitoring environment using **Prometheus** and **Grafana** on two **AWS EC2 instances**: one for the monitoring tools and one as the target to be monitored.

-----

## Prerequisites

1.  **AWS Account**: Access to the AWS Management Console.
2.  **Key Pair**: An SSH key pair for connecting to the EC2 instances.
3.  **Basic Linux/CLI Knowledge**.
4.  **Two EC2 Instances (e.g., Ubuntu or Amazon Linux 2):**
      * **Monitoring Server:** To install Prometheus and Grafana.
      * **Target Server:** To install Node Exporter (the metric collector).

## Step 1: EC2 Instance Setup and Security Groups

1.  **Launch EC2 Instances**: Launch two Linux EC2 instances (e.g., t2.micro, Amazon Linux 2 or Ubuntu).

      * Name one **`Monitoring-Server`**.
      * Name the other **`Target-Server`**.
      * Ensure both use the same SSH key pair.

2.  **Configure Security Groups (Crucial)**: Create or modify a Security Group (SG) for both instances to allow the necessary traffic.

    | Instance Name | Port | Protocol | Source | Purpose |
    | :--- | :--- | :--- | :--- | :--- |
    | **Monitoring-Server** | 22 | TCP | Your IP | SSH access |
    | | 9090 | TCP | Your IP | Prometheus Web UI |
    | | 3000 | TCP | Your IP | Grafana Web UI |
    | | 9100 | TCP | *Target-Server SG* or *Private IP* | Scrape Node Exporter |
    | **Target-Server** | 22 | TCP | Your IP | SSH access |
    | | 9100 | TCP | *Monitoring-Server SG* or *Private IP* | Expose Node Exporter metrics |

      * *Best Practice:* For ports 9100, restrict the source to the *Private IP* or the Security Group of the **Monitoring-Server** to ensure the target is only scraped by your Prometheus instance. For 9090 and 3000, restrict to **Your Public IP** instead of `0.0.0.0/0`.

## Step 2: Install Node Exporter on the Target Server

**Node Exporter** is an official Prometheus exporter that exposes host-level metrics (CPU, memory, disk, network) on an HTTP endpoint (default port 9100).

1.  **SSH into the `Target-Server`**:

    ```bash
    ssh -i your-key.pem ec2-user@<Target-Server-Public-IP>
    ```

2.  **Download and Extract Node Exporter**:

      * Find the latest version on the [Prometheus downloads page](https://www.google.com/search?q=https://prometheus.io/download/%23node_exporter) and replace the version number below.

    <!-- end list -->

    ```bash
    wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
    tar xvfz node_exporter-1.7.0.linux-amd64.tar.gz
    sudo mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/local/bin/
    rm -rf node_exporter*
    ```

3.  **Create a dedicated user (optional but recommended)**:

    ```bash
    sudo useradd -rs /bin/false node_exporter
    ```

4.  **Create a systemd service file**:

    ```bash
    sudo nano /etc/systemd/system/node_exporter.service
    ```

      * Paste the following content:
        ```ini
        [Unit]
        Description=Node Exporter
        Wants=network-online.target
        After=network-online.target

        [Service]
        User=node_exporter
        Group=node_exporter
        Type=simple
        ExecStart=/usr/local/bin/node_exporter

        [Install]
        WantedBy=multi-user.target
        ```

5.  **Start and Enable Node Exporter**:

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start node_exporter
    sudo systemctl enable node_exporter
    sudo systemctl status node_exporter
    ```

6.  **Verify**: Access `http://<Target-Server-Public-IP>:9100/metrics` from your web browser. You should see a page with a lot of metrics data.

-----

## Step 3: Install Prometheus on the Monitoring Server

1.  **SSH into the `Monitoring-Server`**:

    ```bash
    ssh -i your-key.pem ec2-user@<Monitoring-Server-Public-IP>
    ```

2.  **Download and Extract Prometheus**:

      * Find the latest version on the [Prometheus downloads page](https://www.google.com/search?q=https://prometheus.io/download/%23prometheus).

    <!-- end list -->

    ```bash
    wget https://github.com/prometheus/prometheus/releases/download/v2.49.1/prometheus-2.49.1.linux-amd64.tar.gz
    tar xvfz prometheus-2.49.1.linux-amd64.tar.gz
    sudo mv prometheus-2.49.1.linux-amd64/prometheus /usr/local/bin/
    sudo mv prometheus-2.49.1.linux-amd64/promtool /usr/local/bin/
    sudo mv prometheus-2.49.1.linux-amd64/consoles /etc/prometheus
    sudo mv prometheus-2.49.1.linux-amd64/console_libraries /etc/prometheus
    rm -rf prometheus*
    ```

3.  **Create directories and user**:

    ```bash
    sudo useradd --no-create-home --shell /bin/false prometheus
    sudo mkdir -p /etc/prometheus /var/lib/prometheus
    sudo chown prometheus:prometheus /etc/prometheus
    sudo chown prometheus:prometheus /var/lib/prometheus
    ```

4.  **Create the Prometheus configuration file (`prometheus.yml`)**:

    ```bash
    sudo nano /etc/prometheus/prometheus.yml
    ```

      * Paste the following configuration, replacing `<TARGET_SERVER_PRIVATE_IP>` with the **Private IP address** of your `Target-Server`.

        ```yaml
        global:
          scrape_interval: 15s

        scrape_configs:
          - job_name: 'prometheus'
            static_configs:
              - targets: ['localhost:9090']

          - job_name: 'ec2_target'
            static_configs:
              - targets: ['<TARGET_SERVER_PRIVATE_IP>:9100']
        ```

5.  **Create a systemd service file**:

    ```bash
    sudo nano /etc/systemd/system/prometheus.service
    ```

      * Paste the following content:
        ```ini
        [Unit]
        Description=Prometheus
        Wants=network-online.target
        After=network-online.target

        [Service]
        User=prometheus
        Group=prometheus
        Type=simple
        ExecStart=/usr/local/bin/prometheus \
            --config.file /etc/prometheus/prometheus.yml \
            --storage.tsdb.path /var/lib/prometheus/ \
            --web.console.templates=/etc/prometheus/consoles \
            --web.console.libraries=/etc/prometheus/console_libraries

        [Install]
        WantedBy=multi-user.target
        ```

6.  **Start and Enable Prometheus**:

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start prometheus
    sudo systemctl enable prometheus
    sudo systemctl status prometheus
    ```

7.  **Verify**: Access `http://<Monitoring-Server-Public-IP>:9090` in your browser. Go to **Status \> Targets**. You should see both `prometheus` and `ec2_target` (Node Exporter) targets listed with a state of **UP**.

-----

## Step 4: Install and Configure Grafana

1.  **Install Grafana (on `Monitoring-Server`)**:

      * *For Ubuntu/Debian:*
        ```bash
        sudo apt-get install -y apt-transport-https software-properties-common wget
        sudo mkdir -p /etc/apt/keyrings/
        wget -q -O - https://apt.grafana.com/gpg.key | gpg --dearmor | sudo tee /etc/apt/keyrings/grafana.gpg > /dev/null
        echo "deb [signed-by=/etc/apt/keyrings/grafana.gpg] https://apt.grafana.com stable main" | sudo tee /etc/apt/sources.list.d/grafana.list
        sudo apt-get update
        sudo apt-get install grafana
        ```
      * *For Amazon Linux 2 / RHEL (using yum/dnf):* (Commands will differ slightly—refer to official Grafana documentation)

2.  **Start and Enable Grafana**:

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl start grafana-server
    sudo systemctl enable grafana-server
    sudo systemctl status grafana-server
    ```

3.  **Access Grafana**: Open your browser to `http://<Monitoring-Server-Public-IP>:3000`.

      * **Default Login:** **User**: `admin`, **Password**: `admin`. You will be prompted to change the password.

4.  **Add Prometheus Data Source**:

      * In the Grafana UI, navigate to **Connections** (or Configuration -\> Data Sources).
      * Click **Add data source**.
      * Select **Prometheus**.
      * For the **URL** field, enter `http://localhost:9090` (since Grafana and Prometheus are on the same machine).
      * Click **Save & test**. You should see "Data source is working."

5.  **Import a Node Exporter Dashboard**:

      * Navigate to **Dashboards** -\> **New** -\> **Import**.
      * Enter the dashboard ID **1860** (Node Exporter Full) in the "Import via grafana.com" field.
      * Select your Prometheus data source from the dropdown.
      * Click **Import**.

You should now see the metrics from your **Target-Server** visualized in the Grafana dashboard\!

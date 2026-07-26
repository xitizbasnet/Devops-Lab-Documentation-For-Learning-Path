# Lab 23: Monitoring with Prometheus and Data Visualization (Grafana)

# Overview

In this lab, you will implement a complete monitoring solution using **Prometheus** and **Grafana**.

You will install Prometheus for metrics collection, configure Node Exporter on managed servers, connect Grafana with Prometheus as a data source, and create dashboards to visualize infrastructure performance.

This lab introduces real-world monitoring practices used in DevOps and production environments.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install and configure Prometheus on an EC2 monitoring server.
- Collect server metrics using Node Exporter.
- Configure Prometheus scrape targets.
- Install and configure Grafana.
- Connect Grafana with Prometheus.
- Import dashboards for infrastructure monitoring.
- Monitor CPU, memory, disk, and network metrics.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed previous AWS and Linux labs.
- Ubuntu 22.04 EC2 instances available.
- SSH access configured.
- Managed nodes running for metric collection.
- Basic understanding of Linux services.

---

# Monitoring Architecture

The monitoring stack created in this lab:

```text
                     Users

                       |
                       |
                   Grafana
                 Port: 3000

                       |
                       |
                 Prometheus
                 Port: 9090

                       |
        --------------------------------
        |                              |
        v                              v

 Node Exporter                  Node Exporter
   node1                          node2

 Port: 9100                    Port: 9100

        |
        |
 System Metrics
 CPU | Memory | Disk | Network
```

---

# Components Overview

| Component | Purpose |
|-----------|---------|
| Prometheus | Collects and stores time-series metrics. |
| Node Exporter | Exposes Linux system metrics. |
| Grafana | Creates dashboards and visualizations. |
| Data Source | Connection between Grafana and Prometheus. |

---

# Lab Steps

# Step 1: Launch EC2 for Monitoring Stack

Launch a new EC2 instance.

## Instance Configuration

| Setting | Value |
|---------|-------|
| Name | `monitoring-server` |
| AMI | Ubuntu 22.04 |
| Instance Type | `t2.micro` |

---

## Security Group Ports

Allow the following ports:

| Port | Service |
|------|---------|
| 22 | SSH |
| 9090 | Prometheus |
| 3000 | Grafana |
| 9100 | Node Exporter |

---

Connect to the monitoring server:

```bash
ssh -i devops-key.pem ubuntu@<MONITORING_IP>
```

---

# Step 2: Install Prometheus

Download Prometheus:

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.48.0/prometheus-2.48.0.linux-amd64.tar.gz
```

Extract the package:

```bash
tar -xvf prometheus-2.48.0.linux-amd64.tar.gz
```

Move Prometheus files:

```bash
sudo mv prometheus-2.48.0.linux-amd64 /opt/prometheus
```

Create a Prometheus user:

```bash
sudo useradd --no-create-home prometheus
```

Assign ownership:

```bash
sudo chown -R prometheus:prometheus /opt/prometheus
```

---

# Step 3: Configure Prometheus

Open the Prometheus configuration file:

```bash
nano /opt/prometheus/prometheus.yml
```

Add the following configuration:

```yaml
global:

  scrape_interval: 15s


scrape_configs:


- job_name: 'prometheus'

  static_configs:

  - targets: ['localhost:9090']


- job_name: 'node-exporter'

  static_configs:

  - targets:
      - '<NODE1_IP>:9100'
      - '<NODE2_IP>:9100'
```

---

# Prometheus Configuration Explanation

| Configuration | Description |
|---------------|-------------|
| `scrape_interval` | Defines how frequently metrics are collected. |
| `job_name` | Identifies a monitoring target group. |
| `targets` | Defines servers where metrics are collected. |

---

# Step 4: Run Prometheus as a Service

Create a systemd service file:

```bash
sudo nano /etc/systemd/system/prometheus.service
```

Add the following content:

```ini
[Unit]

Description=Prometheus


[Service]

User=prometheus

ExecStart=/opt/prometheus/prometheus \
--config.file=/opt/prometheus/prometheus.yml \
--storage.tsdb.path=/opt/prometheus/data

Restart=always


[Install]

WantedBy=multi-user.target
```

---

Reload system services:

```bash
sudo systemctl daemon-reload
```

Enable Prometheus:

```bash
sudo systemctl enable prometheus
```

Start Prometheus:

```bash
sudo systemctl start prometheus
```

Check service status:

```bash
sudo systemctl status prometheus
```

---

## Test Prometheus

Open:

```text
http://<MONITORING_IP>:9090
```

Prometheus web interface should load successfully.

---

# Step 5: Install Node Exporter on Managed Nodes

Run the following commands on:

- node1
- node2

Download Node Exporter:

```bash
wget https://github.com/prometheus/node_exporter/releases/download/v1.7.0/node_exporter-1.7.0.linux-amd64.tar.gz
```

Extract the package:

```bash
tar -xvf node_exporter-1.7.0.linux-amd64.tar.gz
```

Move the binary:

```bash
sudo mv node_exporter-1.7.0.linux-amd64/node_exporter /usr/local/bin/
```

Start Node Exporter:

```bash
nohup node_exporter &
```

---

## Verify Node Exporter

Run:

```bash
curl http://localhost:9100/metrics
```

Expected output:

```text
# HELP node_cpu_seconds_total
# HELP node_memory_MemAvailable_bytes
```

---

# Step 6: Install Grafana

Install required packages:

```bash
sudo apt install -y apt-transport-https software-properties-common
```

Add Grafana GPG key:

```bash
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
```

Add Grafana repository:

```bash
echo "deb https://packages.grafana.com/oss/deb stable main" \
| sudo tee /etc/apt/sources.list.d/grafana.list
```

Update packages:

```bash
sudo apt update
```

Install Grafana:

```bash
sudo apt install grafana -y
```

Enable Grafana service:

```bash
sudo systemctl enable grafana-server
```

Start Grafana:

```bash
sudo systemctl start grafana-server
```

Check status:

```bash
sudo systemctl status grafana-server
```

---

# Access Grafana

Open:

```text
http://<MONITORING_IP>:3000
```

Default login:

```text
Username: admin

Password: admin
```

---

# Step 7: Configure Grafana Dashboard

## Step 1: Login to Grafana

Open:

```text
http://<MONITORING_IP>:3000
```

Login using:

```text
Username: admin

Password: admin
```

---

## Step 2: Add Prometheus Data Source

Navigate:

```text
Connections
    ↓
Data Sources
    ↓
Add Data Source
```

Select:

```text
Prometheus
```

Configure URL:

```text
http://localhost:9090
```

Click:

```text
Save & Test
```

---

## Step 3: Import Dashboard

Navigate:

```text
Dashboards
    ↓
Import
```

Enter dashboard ID:

```text
1860
```

Select:

```text
Prometheus datasource
```

Click:

```text
Import
```

---

# Available Dashboard Metrics

After importing the dashboard, you can monitor:

| Metric | Description |
|--------|-------------|
| CPU Usage | Processor utilization. |
| Memory Usage | RAM consumption. |
| Disk Usage | Storage utilization. |
| Network Traffic | Network activity. |
| System Load | Server performance status. |

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Launch a monitoring EC2 server.
- ✅ Install Prometheus.
- ✅ Configure Prometheus scrape targets.
- ✅ Install Node Exporter on managed nodes.
- ✅ Collect Linux server metrics.
- ✅ Install Grafana.
- ✅ Connect Grafana with Prometheus.
- ✅ Import monitoring dashboards.

---

# Summary

In this lab, you learned how to:

- Build a complete monitoring stack using Prometheus and Grafana.
- Collect infrastructure metrics using Node Exporter.
- Configure Prometheus monitoring targets.
- Visualize server performance using Grafana dashboards.

The monitoring setup created in this lab provides the foundation for production-level observability, alerting, and infrastructure health management.

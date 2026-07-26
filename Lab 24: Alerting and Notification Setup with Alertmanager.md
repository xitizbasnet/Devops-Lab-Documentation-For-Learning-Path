# Lab 24: Alerting and Notification Setup with Alertmanager

# Overview

In this lab, you will configure an alerting system using **Prometheus Alertmanager**.

You will extend the monitoring stack created in the previous lab by adding automated alert detection and notification capabilities.

The lab covers:

- Installing Alertmanager.
- Configuring notification receivers.
- Creating Prometheus alert rules.
- Connecting Prometheus with Alertmanager.
- Testing alerts using CPU stress conditions.
- Configuring Grafana alerting as an alternative solution.

Alerting helps DevOps teams identify infrastructure problems quickly and respond before services are impacted.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install and configure Prometheus Alertmanager.
- Create Prometheus alert rules.
- Configure Slack notification integration.
- Connect Prometheus with Alertmanager.
- Trigger and validate infrastructure alerts.
- Configure Grafana-based alert rules.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 23: Monitoring with Prometheus and Data Visualization (Grafana)**.
- Prometheus running on the monitoring server.
- Node Exporter installed on managed nodes.
- Grafana installed and connected with Prometheus.
- Access to a Slack webhook or email notification service.

---

# Alerting Architecture

```text
                 Infrastructure Metrics

                         |
                         |
                  Node Exporter

                         |
                         |
                    Prometheus

                         |
              Alert Rules Evaluation

                         |
                         |
                 Alertmanager

                         |
        --------------------------------
        |                              |
        v                              v

      Slack                         Email

   Notifications                Notifications
```

---

# Components Overview

| Component | Purpose |
|-----------|---------|
| Prometheus | Collects metrics and evaluates alert rules. |
| Alertmanager | Processes alerts and sends notifications. |
| Alert Rules | Defines conditions that trigger alerts. |
| Receiver | Defines notification destination. |
| Grafana Alerting | Alternative alert management system. |

---

# Lab Steps

# Step 1: Install Alertmanager

Download Alertmanager:

```bash
wget https://github.com/prometheus/alertmanager/releases/download/v0.26.0/alertmanager-0.26.0.linux-amd64.tar.gz
```

Extract the package:

```bash
tar -xvf alertmanager-0.26.0.linux-amd64.tar.gz
```

Move Alertmanager files:

```bash
sudo mv alertmanager-0.26.0.linux-amd64 /opt/alertmanager
```

Assign ownership:

```bash
sudo chown -R prometheus:prometheus /opt/alertmanager
```

---

# Step 2: Configure Alertmanager

Create the Alertmanager configuration file:

```bash
nano /opt/alertmanager/alertmanager.yml
```

Add the following configuration:

```yaml
global:

  resolve_timeout: 5m


route:

  group_by:
    - alertname

  group_wait: 30s

  receiver: 'slack-notifications'


receivers:

- name: 'slack-notifications'

  slack_configs:

  - api_url: 'https://hooks.slack.com/services/YOUR/SLACK/WEBHOOK'

    channel: '#alerts'

    text: 'Alert: {{ .CommonAnnotations.description }}'
```

---

# Alertmanager Configuration Explanation

| Configuration | Description |
|---------------|-------------|
| `resolve_timeout` | Time before resolved alerts are cleared. |
| `group_by` | Groups similar alerts together. |
| `receiver` | Defines where alerts are sent. |
| `slack_configs` | Configures Slack notifications. |

---

# Step 3: Run Alertmanager as a Service

Create the systemd service file:

```bash
sudo nano /etc/systemd/system/alertmanager.service
```

Add:

```ini
[Unit]

Description=Alertmanager


[Service]

User=prometheus

ExecStart=/opt/alertmanager/alertmanager \
--config.file=/opt/alertmanager/alertmanager.yml \
--storage.path=/opt/alertmanager/data

Restart=always


[Install]

WantedBy=multi-user.target
```

---

Reload system services:

```bash
sudo systemctl daemon-reload
```

Enable Alertmanager:

```bash
sudo systemctl enable alertmanager
```

Start Alertmanager:

```bash
sudo systemctl start alertmanager
```

Check service status:

```bash
sudo systemctl status alertmanager
```

---

# Step 4: Create Prometheus Alerting Rules

Create the alert rules directory:

```bash
sudo mkdir -p /opt/prometheus/rules
```

Create alert rules file:

```bash
nano /opt/prometheus/rules/alerts.yml
```

Add the following content:

```yaml
groups:


- name: HighCPU

  rules:


  - alert: HighCPUUsage

    expr: 100 - (avg by(instance)(rate(node_cpu_seconds_total{mode='idle'}[5m])) * 100) > 80

    for: 2m

    labels:

      severity: warning

    annotations:

      summary: 'High CPU on {{ $labels.instance }}'

      description: 'CPU > 80% for 2 minutes'



- name: InstanceDown

  rules:


  - alert: InstanceDown

    expr: up == 0

    for: 1m

    labels:

      severity: critical

    annotations:

      summary: 'Instance {{ $labels.instance }} is DOWN'
```

---

# Alert Rules Explanation

## High CPU Alert

Condition:

```text
CPU usage > 80%
```

Duration:

```text
2 minutes
```

Severity:

```text
warning
```

---

## Instance Down Alert

Condition:

```text
up == 0
```

Duration:

```text
1 minute
```

Severity:

```text
critical
```

---

# Step 5: Link Alert Rules to Prometheus

Edit Prometheus configuration:

```bash
nano /opt/prometheus/prometheus.yml
```

Add:

```yaml
rule_files:

- '/opt/prometheus/rules/*.yml'


alerting:

  alertmanagers:

  - static_configs:

    - targets:

      - 'localhost:9093'
```

---

Restart Prometheus:

```bash
sudo systemctl restart prometheus
```

---

# Verify Alerts

Open:

```text
http://<MONITORING_IP>:9090/alerts
```

The Prometheus Alerts page should display configured rules.

---

# Step 6: Test Alerting by Triggering High CPU

Connect to a managed node:

```bash
ssh -i devops-key.pem ubuntu@<NODE_IP>
```

Install stress tool if required:

```bash
sudo apt install stress -y
```

Generate CPU load:

```bash
stress --cpu 4 --timeout 300 &
```

Alternative method:

```bash
yes > /dev/null &
```

---

# Monitor Alert Status

Wait approximately:

```text
2 minutes
```

Check:

```text
Prometheus Alerts Page
```

Expected:

```text
HighCPUUsage Alert: FIRING
```

Verify notification:

```text
Slack #alerts channel
```

---

# Stop CPU Test

Stop the background process:

```bash
kill %1
```

CPU usage should return to normal and the alert should resolve automatically.

---

# Step 7: Grafana Alerting (Alternative)

Grafana also provides built-in alerting capabilities.

Navigate:

```text
Grafana
    ↓
Alerting
    ↓
Alert Rules
    ↓
New Rule
```

---

## Create Alert Condition

Configure:

```text
Condition:
avg(node_cpu) > 80
```

Duration:

```text
5 minutes
```

---

## Configure Notification

Select:

```text
Contact Point
```

Options:

- Email
- Slack

---

## Configure Notification Policy

Link:

```text
Alert Rule → Contact Point
```

Save the configuration.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install Alertmanager.
- ✅ Configure notification receivers.
- ✅ Create Prometheus alert rules.
- ✅ Connect Prometheus with Alertmanager.
- ✅ Trigger CPU-based alerts.
- ✅ Receive Slack notifications.
- ✅ Configure Grafana alert rules.

---

# Summary

In this lab, you learned how to:

- Extend Prometheus monitoring with Alertmanager.
- Create infrastructure alert rules.
- Configure automated notifications.
- Test monitoring alerts using real CPU load.
- Use Grafana as an alternative alerting platform.

The alerting workflow created in this lab completes the monitoring and observability foundation required for production DevOps environments.

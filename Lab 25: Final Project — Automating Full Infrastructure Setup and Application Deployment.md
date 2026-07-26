# Lab 25: Final Project — Automating Full Infrastructure Setup and Application Deployment

# Overview

This final project combines all DevOps tools and practices covered throughout Labs 1–24 into a complete end-to-end automation workflow.

You will provision cloud infrastructure, configure servers, deploy applications through CI/CD pipelines, and implement monitoring and alerting for a production-style DevOps environment.

This project integrates:

- Infrastructure as Code (Terraform)
- Configuration Management (Ansible)
- Source Control (GitHub)
- CI/CD Automation (Jenkins)
- Containerization (Docker)
- Container Orchestration (Kubernetes / EKS)
- Monitoring (Prometheus + Grafana)
- Alerting (Alertmanager + Slack)

---

# Learning Objectives

After completing this final project, you will be able to:

- Provision complete AWS infrastructure using Terraform.
- Configure servers automatically using Ansible.
- Build and deploy containerized applications using Jenkins CI/CD.
- Deploy applications into an Amazon EKS Kubernetes cluster.
- Configure monitoring dashboards using Prometheus and Grafana.
- Implement alert notifications using Alertmanager.
- Perform complete infrastructure cleanup using Terraform.

---

# Architecture Overview

## DevOps Automation Architecture

```text
                         Developer

                            |
                            v

                         GitHub

                            |
                            v

                        Jenkins CI/CD

                            |
        -----------------------------------------
        |                  |                    |
        v                  v                    v

   Build/Test        Docker Build        Docker Push

                            |
                            v

                     DockerHub / ECR

                            |
                            v

                       Amazon EKS

                            |
                            v

                 Containerized Application


----------------------------------------------------

Infrastructure Layer

Terraform
    |
    |
    +-- VPC
    +-- Subnets
    +-- EC2
    +-- EKS


Configuration Layer

Ansible
    |
    |
    +-- Docker Installation
    +-- Nginx Configuration
    +-- Monitoring Agents


Monitoring Layer

Prometheus
        |
        |
     Grafana


Alerting Layer

Alertmanager
        |
        |
       Slack
```

---

# Technology Stack

| Layer | Technology | Purpose |
|-------|------------|---------|
| Infrastructure | Terraform | VPC, Subnets, EC2, EKS provisioning |
| Configuration | Ansible | OS configuration, Docker, Nginx, monitoring agents |
| Source Control | GitHub | Application source code, Jenkinsfile, Kubernetes manifests |
| CI/CD | Jenkins | Build, Test, Docker Push, Deployment automation |
| Container Registry | DockerHub / ECR | Store Docker images |
| Orchestration | Amazon EKS | Run production containers |
| Monitoring | Prometheus + Grafana | Metrics collection and visualization |
| Alerting | Alertmanager + Slack | Notification management |

---

# Prerequisites

Before starting this final project, ensure the following labs are completed:

- Lab 17 — Jenkins CI/CD Pipeline with EKS
- Lab 20 — Automating EKS Cluster Provisioning with Terraform
- Lab 22 — Ansible Playbooks
- Lab 23 — Prometheus and Grafana Monitoring
- Lab 24 — Alertmanager Notifications

Required access:

- AWS Account
- GitHub Repository
- DockerHub Account
- Jenkins Server
- Kubernetes Cluster Access

---

# Step 1 — Provision Infrastructure with Terraform

Use the EKS Terraform configuration created in Lab 20.

Navigate to the Terraform project:

```bash
cd lab20-eks
```

Initialize Terraform:

```bash
terraform init
```

Provision AWS infrastructure:

```bash
terraform apply -auto-approve
```

---

## Configure Kubernetes Access

Update kubeconfig:

```bash
aws eks update-kubeconfig \
--name devops-eks \
--region ap-south-1
```

Verify cluster status:

```bash
kubectl get nodes
```

Expected result:

```text
NAME                           STATUS
worker-node                    Ready
```

The EKS cluster is now ready for application deployment.

---

# Step 2 — Configure Servers with Ansible

Use Ansible to configure EC2 servers automatically.

## Install Docker on Servers

Run:

```bash
ansible-playbook -i inventory.ini install-docker.yml
```

---

## Install Monitoring Agents

Run:

```bash
ansible-playbook -i inventory.ini monitoring-setup.yml
```

---

# Ansible Docker Installation Playbook

File:

```text
install-docker.yml
```

Content:

```yaml
---

- hosts: all

  become: yes

  roles:

  - geerlingguy.docker
```

---

# Step 3 — Set Up Jenkins CI/CD Pipeline

Ensure the Jenkins server has the following configurations:

## Jenkins Requirements

### 1. Docker Installed

Configured from:

```text
Lab 12 — Setting Up Jenkins
```

---

### 2. Kubernetes Access

Configured from:

```text
Lab 17 — Jenkins and EKS Deployment
```

---

### 3. DockerHub Credentials

Configured in:

```text
Jenkins Dashboard
    ↓
Manage Jenkins
    ↓
Credentials
```

---

### 4. GitHub Webhook

Configured for automatic pipeline triggering.

---

# Final Jenkins Pipeline Workflow

The final Jenkins pipeline contains these stages:

```text
Checkout
    |
    v
Build
    |
    v
Test
    |
    v
Docker Build
    |
    v
Push Image
    |
    v
Deploy to EKS
    |
    v
Send Notification
```

---

# Step 4 — Deploy Application

Push application changes to GitHub:

```bash
git add .

git commit -m "Final project deployment"

git push origin main
```

---

# Jenkins Automatic Deployment Flow

After the Git push, Jenkins performs:

## Stage 1 — Clone Repository

Jenkins downloads the latest application source code.

---

## Stage 2 — Build Docker Image

Creates a container image from the application.

---

## Stage 3 — Push Image

Uploads the image to:

```text
DockerHub / Amazon ECR
```

---

## Stage 4 — Deploy to Kubernetes

Updates the EKS deployment.

---

## Stage 5 — Send Notification

Sends deployment status through Slack.

---

# Verify Kubernetes Deployment

Check running pods:

```bash
kubectl get pods
```

Check services:

```bash
kubectl get services
```

Retrieve application endpoint:

```text
EXTERNAL-IP
```

---

# Step 5 — Verify Monitoring Dashboard

## Open Grafana Dashboard

Access:

```text
http://<MONITORING_IP>:3000
```

Verify:

- EKS node metrics.
- CPU utilization.
- Memory usage.
- Disk usage.
- Network metrics.

---

## Open Prometheus

Access:

```text
http://<MONITORING_IP>:9090
```

Run query:

```promql
up{job='node-exporter'}
```

Expected result:

```text
All monitoring targets should be green.
```

---

# Step 6 — Test End-to-End Alert

Generate CPU load on a node:

```bash
yes > /dev/null &
```

---

Wait:

```text
2 minutes
```

Expected result:

```text
Slack Alert Triggered
```

---

Stop the CPU test:

```bash
kill %1
```

Expected result:

```text
Alert resolved notification received.
```

---

# Step 7 — Documentation and Cleanup

## Document Architecture

Create project documentation containing:

- Architecture diagram.
- Infrastructure design.
- Deployment workflow.
- Monitoring setup.
- Troubleshooting steps.

---

## Repository Documentation

Include:

```text
README.md
Terraform Files
Ansible Playbooks
Jenkinsfile
Dockerfile
Kubernetes YAML Files
Monitoring Configuration
```

Push all configurations to GitHub.

---

# Infrastructure Cleanup

⚠️ Important: AWS resources created during this project may generate charges.

Destroy all Terraform-managed resources:

```bash
terraform destroy -auto-approve
```

---

# Verify Cleanup

Check AWS Console and confirm:

- No running EC2 instances.
- No active EKS clusters.
- No unused load balancers.
- No remaining cloud resources.

---

# Final Project Completion Checklist

| Task | Status |
|------|--------|
| Terraform infrastructure provisioning | ✅ |
| AWS EKS cluster creation | ✅ |
| Ansible server configuration | ✅ |
| Docker installation automation | ✅ |
| Jenkins CI/CD pipeline | ✅ |
| Docker image build and publishing | ✅ |
| Kubernetes deployment | ✅ |
| Prometheus monitoring | ✅ |
| Grafana dashboards | ✅ |
| Alertmanager notifications | ✅ |
| Infrastructure cleanup | ✅ |

---

# Congratulations 🎉

You have completed all **25 DevOps Labs**.

Throughout this hands-on journey, you have gained practical experience with:

- Linux Administration
- Git Version Control
- Build Automation
- AWS Cloud Infrastructure
- Docker Containerization
- Jenkins CI/CD
- Kubernetes and Amazon EKS
- Terraform Infrastructure as Code
- Ansible Configuration Management
- Prometheus Monitoring
- Grafana Visualization
- Alertmanager Notifications

You now have hands-on exposure to a complete modern DevOps toolkit used in real-world engineering environments.

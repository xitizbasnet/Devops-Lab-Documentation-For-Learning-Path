# Quick Reference — Essential DevOps Commands

This quick reference guide contains frequently used commands for daily DevOps operations across Linux, Git, Docker, Kubernetes, Terraform, and Ansible.

---

# Linux Commands 🐧

## SSH Connection

Connect to a remote Linux server using SSH:

```bash
ssh -i key.pem ubuntu@<IP>
```

---

## Fix SSH Key Permissions

Secure your private key file:

```bash
chmod 400 key.pem
```

---

## Check Service Status

View the status of a Linux service:

```bash
sudo systemctl status <service>
```

---

## Check System Resources

Monitor disk usage, memory, and running processes:

```bash
df -h && free -m && top
```

### Command Description

| Command | Purpose |
|---------|---------|
| `df -h` | Check disk usage |
| `free -m` | Check memory usage |
| `top` | Monitor running processes |

---

# Git Commands 🌿

## Basic Git Workflow

Initialize, clone, stage, and commit code:

```bash
git init
git clone
git add
git commit
```

---

## Create a New Branch

Create and switch to a new feature branch:

```bash
git checkout -b feature/name
```

---

## Branch Integration Commands

Merge, rebase, or temporarily save changes:

```bash
git merge
git rebase
git stash
```

---

## View Git History

Display commit history in graph format:

```bash
git log --oneline --graph
```

---

# Docker Commands 🐳

## Build Docker Image

Create a Docker image from a Dockerfile:

```bash
docker build -t name:tag .
```

---

## Run Docker Container

Start a container with port mapping:

```bash
docker run -d -p 80:80 nginx
```

---

## Start Docker Compose Stack

Run multiple containers using Docker Compose:

```bash
docker-compose up -d
```

---

## Push Image to Registry

Upload Docker image to a container registry:

```bash
docker push user/image:tag
```

---

# Kubernetes Commands ☸️

## Deploy Kubernetes Resources

Apply Kubernetes YAML manifests:

```bash
kubectl apply -f file.yaml
```

---

## List Kubernetes Resources

View pods, services, and deployments:

```bash
kubectl get pods/services/deploy
```

---

## Scale Application Deployment

Increase or decrease application replicas:

```bash
kubectl scale deploy app --replicas=5
```

---

## Rollback Deployment

Undo the latest deployment update:

```bash
kubectl rollout undo deploy/app
```

---

# Terraform Commands 🏗️

## Terraform Core Workflow

Initialize, review, and apply infrastructure changes:

```bash
terraform init
terraform plan
terraform apply
```

---

## Destroy Infrastructure

Remove Terraform-managed resources:

```bash
terraform destroy -auto-approve
```

---

## View Terraform State

List managed resources:

```bash
terraform state list
```

---

## View Terraform Outputs

Display output values:

```bash
terraform output
```

---

# Ansible Commands ⚙️

## Test Host Connectivity

Verify Ansible connection to managed nodes:

```bash
ansible all -m ping
```

---

## Run Ansible Playbook

Execute an automation playbook:

```bash
ansible-playbook playbook.yml
```

---

## Install Ansible Role

Download and install an Ansible Galaxy role:

```bash
ansible-galaxy install role
```

---

## Gather System Facts

Collect information from managed nodes:

```bash
ansible all -m setup
```

---

# DevOps Command Summary

| Tool | Common Operations |
|------|-------------------|
| Linux | SSH, permissions, services, system monitoring |
| Git | Version control, branching, merging |
| Docker | Build, run, compose, push images |
| Kubernetes | Deploy, scale, rollback workloads |
| Terraform | Provision and destroy infrastructure |
| Ansible | Configure and automate servers |

---

## Tip 💡

Keep this reference available during DevOps activities. These commands cover the most frequently used operations in cloud infrastructure management, CI/CD workflows, container orchestration, and automation tasks.

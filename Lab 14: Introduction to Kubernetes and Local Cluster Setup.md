# Lab 14: Introduction to Kubernetes and Local Cluster Setup

# Overview

In this lab, you will set up a local Kubernetes cluster on an AWS EC2 instance using Minikube. You will install Kubernetes command-line tools, start a Kubernetes cluster using Docker as the runtime, and explore core Kubernetes resources.

This lab introduces fundamental Kubernetes concepts including Pods, Deployments, Services, Namespaces, and basic `kubectl` operations.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install and configure `kubectl`.
- Install and configure Minikube on an EC2 instance.
- Start a local Kubernetes cluster.
- Understand Kubernetes core components:
  - Pods
  - Deployments
  - Services
  - Namespaces
- Execute basic Kubernetes management commands using `kubectl`.
- Access the Minikube dashboard.

---

# Prerequisites

Before starting this lab, ensure that you have:

- An Ubuntu EC2 instance.
- SSH access configured.
- Docker installed on the EC2 instance.
- Basic understanding of Linux commands.
- Sudo privileges on the EC2 server.

---

# Kubernetes Architecture Overview

Kubernetes manages containerized applications through several core resources:

| Resource | Description |
|----------|-------------|
| Pod | The smallest deployable Kubernetes unit that runs one or more containers. |
| Deployment | Manages application replicas and ensures the desired number of Pods are running. |
| Service | Provides stable network access to Pods. |
| Namespace | Provides logical separation of Kubernetes resources. |
| kubectl | Command-line tool used to communicate with the Kubernetes cluster. |

---

# Lab Steps

## Step 1: Install kubectl

`kubectl` is the command-line tool used to interact with Kubernetes clusters.

Download the latest stable version:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s \
https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

Install `kubectl`.

```bash
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Verify the installation.

```bash
kubectl version --client
```

Expected output:

```text
Client Version: v1.x.x
```

---

# Step 2: Install Minikube

Minikube creates a local Kubernetes cluster suitable for learning and testing.

Download Minikube:

```bash
curl -LO \
https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
```

Install Minikube.

```bash
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```

Verify the installation.

```bash
minikube version
```

---

# Step 3: Install Docker Driver

Minikube requires a container runtime. In this lab, Docker is used as the driver.

Install Docker if it is not already installed.

```bash
sudo apt install docker.io -y
```

Add the Ubuntu user to the Docker group.

```bash
sudo usermod -aG docker ubuntu
```

Reload group permissions.

```bash
newgrp docker
```

Verify Docker access.

```bash
docker --version
```

---

# Step 4: Start the Minikube Cluster

Start Kubernetes using the Docker driver.

```bash
minikube start --driver=docker
```

Check Minikube status.

```bash
minikube status
```

View Kubernetes cluster information.

```bash
kubectl cluster-info
```

Check available cluster nodes.

```bash
kubectl get nodes
```

Expected output:

```text
NAME       STATUS   ROLES           AGE
minikube   Ready    control-plane   xx
```

---

# Step 5: Explore Kubernetes Resources

## View All Pods

Display Pods from all namespaces.

```bash
kubectl get pods -A
```

---

## View Namespaces

List Kubernetes namespaces.

```bash
kubectl get namespaces
```

---

## View Services

Display all Kubernetes services.

```bash
kubectl get services -A
```

---

## View Deployments

Display all deployments.

```bash
kubectl get deployments -A
```

---

# Step 6: Essential kubectl Commands

## Create or Update Resources

Apply Kubernetes configuration files.

```bash
kubectl apply -f <file.yaml>
```

---

## Delete Resources

Remove Kubernetes resources.

```bash
kubectl delete -f <file.yaml>
```

---

## View Pod Details

Display detailed information about a Pod.

```bash
kubectl describe pod <name>
```

---

## View Pod Logs

Display application logs.

```bash
kubectl logs <pod-name>
```

---

## Access a Pod Shell

Open an interactive shell inside a running Pod.

```bash
kubectl exec -it <pod> -- bash
```

---

## View All Resources

Display all Kubernetes resources.

```bash
kubectl get all
```

---

## View Kubernetes Contexts

List configured Kubernetes clusters.

```bash
kubectl config get-contexts
```

---

# Step 7: Enable Minikube Dashboard

Start the Kubernetes dashboard.

```bash
minikube dashboard &
```

The dashboard provides a visual interface to monitor:

- Cluster information
- Pods
- Deployments
- Services
- Resource status

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install `kubectl`.
- ✅ Install Minikube.
- ✅ Configure Docker as the Minikube driver.
- ✅ Start a local Kubernetes cluster.
- ✅ Verify Kubernetes node status.
- ✅ Explore Pods, Services, Deployments, and Namespaces.
- ✅ Execute basic `kubectl` commands.
- ✅ Access the Minikube dashboard.

---

# Summary

In this lab, you learned how to:

- Set up a Kubernetes learning environment using Minikube.
- Install Kubernetes command-line tools.
- Understand basic Kubernetes resources.
- Manage Kubernetes objects using `kubectl`.
- Monitor cluster resources using the Minikube dashboard.

The Kubernetes concepts introduced in this lab will be used in upcoming labs for application deployment, managed Kubernetes clusters, and CI/CD integration.

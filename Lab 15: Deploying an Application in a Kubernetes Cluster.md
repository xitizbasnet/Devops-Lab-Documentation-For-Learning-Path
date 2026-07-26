# Lab 15: Deploying an Application in a Kubernetes Cluster

# Overview

In this lab, you will deploy a containerized application to a Kubernetes cluster using Kubernetes YAML manifests. You will create a Deployment and Service, expose the application externally, scale application replicas, perform rolling updates, and manage application rollbacks.

This lab introduces the Kubernetes application deployment workflow used in production environments.

---

# Learning Objectives

After completing this lab, you will be able to:

- Create Kubernetes YAML manifests.
- Deploy containerized applications using Kubernetes Deployments.
- Configure Kubernetes Services.
- Expose applications using:
  - NodePort
  - LoadBalancer
- Scale Kubernetes deployments.
- Perform rolling updates.
- Roll back deployments when required.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 14: Introduction to Kubernetes and Local Cluster Setup**.
- A running Kubernetes cluster using Minikube.
- A Docker image available in Docker Hub.
- Basic understanding of Kubernetes Pods and Deployments.

---

# Kubernetes Deployment Architecture

The application deployment flow:

```text
              User
                |
                |
          Kubernetes Service
                |
                |
          Kubernetes Pods
        +-------+-------+
        |       |       |
      Pod-1   Pod-2   Pod-3
        |
        |
 Flask Application Container
```

Components:

| Component | Purpose |
|-----------|---------|
| Namespace | Provides logical isolation for resources. |
| Deployment | Manages application Pods and replicas. |
| Pod | Runs the application container. |
| Service | Provides network access to Pods. |

---

# Lab Steps

## Step 1: Create a Namespace

Create a dedicated namespace for the application.

```bash
kubectl create namespace devops-lab
```

Verify the namespace.

```bash
kubectl get namespaces
```

Expected output:

```text
devops-lab
```

---

# Step 2: Create Deployment YAML

Create the deployment configuration file.

```bash
nano flask-deployment.yaml
```

Add the following content:

```yaml
apiVersion: apps/v1
kind: Deployment

metadata:
  name: flask-app
  namespace: devops-lab

spec:
  replicas: 3

  selector:
    matchLabels:
      app: flask-app

  template:
    metadata:
      labels:
        app: flask-app

    spec:
      containers:
      - name: flask
        image: <dockerhub-user>/flask-devops:1.0
        ports:
        - containerPort: 5000
```

---

# Deployment YAML Explanation

| Field | Description |
|-------|-------------|
| `kind: Deployment` | Creates a Kubernetes Deployment resource. |
| `replicas` | Defines the number of application Pods. |
| `selector` | Identifies Pods managed by the Deployment. |
| `image` | Docker image used for the application. |
| `containerPort` | Port exposed inside the container. |

---

# Step 3: Create Service YAML

Create the service configuration file.

```bash
nano flask-service.yaml
```

Add the following content:

```yaml
apiVersion: v1
kind: Service

metadata:
  name: flask-service
  namespace: devops-lab

spec:
  selector:
    app: flask-app

  ports:
  - port: 80
    targetPort: 5000

  type: NodePort
```

---

# Service YAML Explanation

| Field | Description |
|-------|-------------|
| `selector` | Connects the Service to matching Pods. |
| `port` | Service access port. |
| `targetPort` | Application container port. |
| `type` | Defines external access method. |

---

# Step 4: Deploy Application to Kubernetes

Apply the Deployment manifest.

```bash
kubectl apply -f flask-deployment.yaml
```

Apply the Service manifest.

```bash
kubectl apply -f flask-service.yaml
```

Verify Pods.

```bash
kubectl get pods -n devops-lab
```

Verify Service.

```bash
kubectl get services -n devops-lab
```

Expected result:

```text
NAME             TYPE       PORT
flask-service    NodePort   80
```

---

# Step 5: Access the Application

Get the application URL from Minikube.

```bash
minikube service flask-service -n devops-lab --url
```

The command returns a URL similar to:

```text
http://<minikube-ip>:<node-port>
```

Test the application:

```bash
curl http://<APPLICATION_URL>
```

You can also open the URL in a browser.

---

# Step 6: Scale the Deployment

Increase the number of application replicas.

```bash
kubectl scale deployment flask-app --replicas=5 -n devops-lab
```

Verify the Pods.

```bash
kubectl get pods -n devops-lab
```

Expected result:

```text
5 Flask application Pods running
```

Scaling allows Kubernetes to distribute application workload across multiple Pods.

---

# Step 7: Perform a Rolling Update

A rolling update replaces application Pods gradually without downtime.

Update the Docker image version.

```bash
kubectl set image deployment/flask-app \
flask=<user>/flask-devops:2.0 \
-n devops-lab
```

Monitor the rollout.

```bash
kubectl rollout status deployment/flask-app -n devops-lab
```

View deployment history.

```bash
kubectl rollout history deployment/flask-app -n devops-lab
```

---

# Step 8: Rollback Deployment if Required

If the new deployment version has issues, rollback to the previous version.

Execute rollback:

```bash
kubectl rollout undo deployment/flask-app -n devops-lab
```

Verify rollback status.

```bash
kubectl rollout status deployment/flask-app -n devops-lab
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Create a Kubernetes namespace.
- ✅ Write a Deployment YAML manifest.
- ✅ Write a Service YAML manifest.
- ✅ Deploy an application to Kubernetes.
- ✅ Verify running Pods and Services.
- ✅ Access an application using NodePort.
- ✅ Scale application replicas.
- ✅ Perform rolling updates.
- ✅ Roll back application deployments.

---

# Summary

In this lab, you learned how to:

- Deploy containerized applications using Kubernetes.
- Create Kubernetes Deployment and Service resources.
- Manage application replicas.
- Expose applications externally.
- Perform zero-downtime rolling updates.
- Roll back deployments when required.

The deployment concepts covered in this lab are fundamental for managing applications in Kubernetes production environments.

# Lab 17: Deploying a CI, CD Pipeline with EKS and DockerHub

# Overview

In this lab, you will integrate Jenkins with an Amazon EKS Kubernetes cluster to create a complete CI/CD workflow.

The pipeline will automatically build a Docker image, push it to DockerHub, and deploy the updated application to the EKS cluster.

This workflow represents a production-style DevOps deployment process where code changes automatically trigger application delivery.

---

# Learning Objectives

After completing this lab, you will be able to:

- Integrate Jenkins with an Amazon EKS cluster.
- Configure Kubernetes access from Jenkins.
- Automate the complete CI/CD workflow:
  - Build Docker image
  - Push image to DockerHub
  - Deploy application to EKS
- Configure `kubectl` for Jenkins.
- Implement an automated Kubernetes deployment pipeline.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed:
  - Lab 12: Setting Up Jenkins for Build Automation
  - Lab 13: Creating a Pipeline Job in Jenkins
  - Lab 16: Setting Up and Running a Managed EKS Cluster
- A running Jenkins server.
- A running EKS cluster.
- DockerHub credentials configured in Jenkins.
- Application Kubernetes manifests available.

---

# CI/CD Workflow Architecture

The complete deployment workflow:

```text
Developer
    |
    |
 Git Push
    |
    v
 GitHub Repository
    |
    |
 GitHub Webhook
    |
    v
 Jenkins Pipeline
    |
    +--------------------+
    |                    |
    v                    v
Build Docker Image   Push Image
                         |
                         v
                   DockerHub Registry
                         |
                         v
                    Deploy to EKS
                         |
                         v
                Kubernetes Application
```

---

# Pipeline Stages

| Stage | Description |
|-------|-------------|
| Checkout | Downloads application source code from GitHub. |
| Build Image | Creates a Docker image from application code. |
| Push Image | Uploads the image to DockerHub. |
| Deploy | Updates the application running in EKS. |

---

# Lab Steps

## Step 1: Install kubectl on Jenkins Server

Connect to the Jenkins server.

```bash
ssh ubuntu@<JENKINS_IP>
```

Download Kubernetes CLI.

```bash
curl -LO https://dl.k8s.io/release/$(curl -Ls \
https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl
```

Install `kubectl`.

```bash
sudo install kubectl /usr/local/bin/kubectl
```

Verify installation.

```bash
kubectl version --client
```

---

# Step 2: Configure kubeconfig on Jenkins

Jenkins requires Kubernetes credentials to communicate with the EKS cluster.

Create Kubernetes configuration directory for Jenkins.

```bash
sudo mkdir -p /var/lib/jenkins/.kube
```

Copy the Kubernetes configuration file from the EC2 instance where `eksctl` was executed.

```bash
scp -i devops-key.pem ubuntu@<EC2_IP>:~/.kube/config /tmp/kubeconfig
```

Copy the kubeconfig file to Jenkins home directory.

```bash
sudo cp /tmp/kubeconfig /var/lib/jenkins/.kube/config
```

Assign ownership to Jenkins.

```bash
sudo chown jenkins:jenkins /var/lib/jenkins/.kube/config
```

---

# Verify Kubernetes Access from Jenkins

Switch to the Jenkins user.

```bash
sudo su -s /bin/bash jenkins
```

Test Kubernetes connectivity.

```bash
kubectl get nodes
```

Or execute directly:

```bash
sudo su -s /bin/bash jenkins -c \
'kubectl get nodes'
```

Expected output:

```text
NAME                                      STATUS
ip-xxx-xxx-xxx.compute.internal           Ready
```

---

# Step 3: Update Jenkinsfile for EKS Deployment

Update the `Jenkinsfile` in your repository with EKS deployment configuration.

```groovy
pipeline {

    agent any

    environment {
        IMAGE = '<dockerhub-user>/flask-devops'
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/<user>/my-devops-app.git'
            }
        }

        stage('Build & Push Image') {

            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-creds',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {

                    sh 'docker build -t $IMAGE:$BUILD_NUMBER .'

                    sh '''
                    echo $PASS | docker login \
                    -u $USER \
                    --password-stdin
                    '''

                    sh 'docker push $IMAGE:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to EKS') {

            steps {

                sh '''
                kubectl set image deployment/flask-app \
                flask=$IMAGE:$BUILD_NUMBER
                '''

                sh '''
                kubectl rollout status \
                deployment/flask-app
                '''
            }
        }
    }
}
```

---

# Jenkinsfile Stage Explanation

| Stage | Purpose |
|-------|---------|
| Checkout | Retrieves latest application source code. |
| Build & Push Image | Builds the Docker image and uploads it to DockerHub. |
| Deploy to EKS | Updates the Kubernetes Deployment with the new image version. |

---

# Step 4: Test the Complete Pipeline

Make a change to the application code.

Example:

```bash
echo "# Updated" >> app.py
```

Stage the changes.

```bash
git add .
```

Commit the changes.

```bash
git commit -m "Trigger CI/CD"
```

Push changes to GitHub.

```bash
git push origin main
```

---

# Expected CI/CD Execution

After the Git push:

```text
GitHub Push
     |
     v
Webhook Trigger
     |
     v
Jenkins Pipeline Starts
     |
     v
Docker Image Build
     |
     v
DockerHub Image Update
     |
     v
EKS Deployment Update
```

Monitor:

- Jenkins build console output.
- DockerHub image updates.
- Kubernetes deployment status.

---

# Verify EKS Deployment

Check running Pods:

```bash
kubectl get pods
```

Check deployment status:

```bash
kubectl get deployment
```

View rollout status:

```bash
kubectl rollout status deployment/flask-app
```

Verify the updated image:

```bash
kubectl describe deployment flask-app
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install `kubectl` on Jenkins.
- ✅ Configure Jenkins Kubernetes access.
- ✅ Connect Jenkins with EKS.
- ✅ Update Jenkins pipeline for Kubernetes deployment.
- ✅ Build Docker images automatically.
- ✅ Push images to DockerHub.
- ✅ Deploy updates automatically to EKS.
- ✅ Trigger deployments using GitHub webhook.

---

# Summary

In this lab, you learned how to:

- Integrate Jenkins with Amazon EKS.
- Configure Kubernetes authentication for CI/CD.
- Automate Docker image creation and publishing.
- Deploy applications automatically to Kubernetes.
- Build a production-style CI/CD workflow.

This completes the application delivery pipeline:

```text
Code Commit
     ↓
Jenkins Build
     ↓
Docker Image
     ↓
DockerHub Registry
     ↓
Amazon EKS Deployment
```

The pipeline created in this lab provides the foundation for advanced Infrastructure as Code and cloud automation workflows.

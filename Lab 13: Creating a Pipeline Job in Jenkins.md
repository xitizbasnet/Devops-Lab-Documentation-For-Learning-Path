# Lab 13: Creating a Pipeline Job in Jenkins

# Overview

In this lab, you will create a Jenkins Pipeline job using a `Jenkinsfile`. You will define automated CI/CD stages including source code checkout, Docker image creation, Docker Hub publishing, and application deployment.

You will also configure Jenkins credentials securely and set up a GitHub webhook to automatically trigger the pipeline whenever new code changes are pushed.

---

# Learning Objectives

After completing this lab, you will be able to:

- Create a Jenkins Pipeline job using a `Jenkinsfile`.
- Define CI/CD pipeline stages:
  - Checkout
  - Build
  - Docker Build
  - Push
  - Deploy
- Configure Docker access on Jenkins.
- Store Docker Hub credentials securely in Jenkins.
- Create and execute Jenkins Pipeline jobs.
- Configure GitHub webhooks for automated pipeline triggers.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 12: Setting Up Jenkins for Build Automation**.
- A running Jenkins server.
- Docker installed on the Jenkins server.
- A GitHub repository containing application source code.
- A Docker Hub account.

---

# Jenkins Pipeline Workflow

The pipeline workflow used in this lab:

```text
Developer Push Code
        |
        v
    GitHub Repository
        |
        v
   GitHub Webhook
        |
        v
 Jenkins Pipeline
        |
        +----------------+
        |                |
        v                v
    Checkout        Build Docker Image
                         |
                         v
                  Push to DockerHub
                         |
                         v
                    Deploy App
```

---

# Lab Steps

## Step 1: Install Docker on Jenkins Server

Install Docker.

```bash
sudo apt install docker.io -y
```

Add the Jenkins user to the Docker group.

```bash
sudo usermod -aG docker jenkins
```

Restart Jenkins to apply the permission changes.

```bash
sudo systemctl restart jenkins
```

Verify Docker access from Jenkins.

```bash
docker --version
```

---

# Step 2: Create Jenkinsfile in Your Repository

In your GitHub repository, create a file named:

```text
Jenkinsfile
```

Add the following pipeline configuration:

```groovy
pipeline {

    agent any

    environment {
        DOCKERHUB_CREDS = credentials('dockerhub-creds')
        IMAGE = '<your-dockerhub-username>/flask-devops'
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/<user>/my-devops-app.git',
                branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE:$BUILD_NUMBER .'
            }
        }

        stage('Push to DockerHub') {
            steps {

                sh '''
                echo $DOCKERHUB_CREDS_PSW | docker login \
                -u $DOCKERHUB_CREDS_USR \
                --password-stdin
                '''

                sh 'docker push $IMAGE:$BUILD_NUMBER'
            }
        }

        stage('Deploy') {
            steps {

                sh 'docker stop flask-app || true'

                sh 'docker rm flask-app || true'

                sh '''
                docker run -d \
                -p 5000:5000 \
                --name flask-app \
                $IMAGE:$BUILD_NUMBER
                '''
            }
        }
    }
}
```

---

# Pipeline Stage Explanation

| Stage | Purpose |
|-------|---------|
| Checkout | Downloads source code from GitHub. |
| Build Docker Image | Creates a Docker image from application code. |
| Push to DockerHub | Publishes the Docker image to Docker Hub. |
| Deploy | Runs the latest Docker container. |

---

# Step 3: Add DockerHub Credentials to Jenkins

Navigate to:

```text
Jenkins Dashboard → Manage Jenkins → Credentials
```

Select:

```text
System → Global credentials → Add Credentials
```

Configure the credential:

| Field | Value |
|-------|-------|
| Kind | Username with password |
| Username | `<dockerhub-username>` |
| Password | `<dockerhub-password>` |
| ID | `dockerhub-creds` |

Click:

```text
Create
```

> **Note**
>
> Jenkins credentials allow sensitive information such as passwords and tokens to be stored securely instead of being written directly in pipeline files.

---

# Step 4: Create a Jenkins Pipeline Job

Open Jenkins Dashboard.

Select:

```text
New Item
```

Configure the job:

| Setting | Value |
|---------|-------|
| Name | `flask-devops-pipeline` |
| Type | Pipeline |

Click:

```text
OK
```

---

## Configure Pipeline from SCM

In the Pipeline section:

| Setting | Value |
|---------|-------|
| Definition | Pipeline script from SCM |
| SCM | Git |
| Repository URL | `https://github.com/<user>/my-devops-app.git` |
| Branch | `*/main` |
| Script Path | `Jenkinsfile` |

Click:

```text
Save
```

---

# Step 5: Run the Pipeline

Open the Jenkins job.

Select:

```text
Build Now
```

Click the generated build number.

Open:

```text
Console Output
```

Monitor the pipeline execution.

Expected stages:

```text
Checkout
      ↓
Build Docker Image
      ↓
Push to DockerHub
      ↓
Deploy
```

---

# Step 6: Configure GitHub Webhook

A webhook allows GitHub to automatically notify Jenkins when code changes are pushed.

---

## Configure Webhook in GitHub

Open your GitHub repository.

Navigate to:

```text
Settings → Webhooks → Add webhook
```

Configure:

| Setting | Value |
|---------|-------|
| Payload URL | `http://<JENKINS_IP>:8080/github-webhook/` |
| Content Type | `application/json` |
| Events | Just the push event |

Click:

```text
Add webhook
```

---

## Enable Webhook Trigger in Jenkins

Open the Jenkins Pipeline job.

Navigate to:

```text
Configure
```

Under:

```text
Build Triggers
```

Enable:

```text
GitHub hook trigger for GITScm polling
```

Click:

```text
Save
```

---

# Test Automatic Deployment

Make a code change in your GitHub repository.

Commit and push the changes:

```bash
git add .
git commit -m "Trigger Jenkins pipeline"
git push origin main
```

GitHub will send the webhook notification to Jenkins.

The Jenkins pipeline will automatically start.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install Docker on Jenkins server.
- ✅ Configure Jenkins Docker permissions.
- ✅ Create a Jenkinsfile.
- ✅ Define CI/CD pipeline stages.
- ✅ Configure Docker Hub credentials.
- ✅ Create a Jenkins Pipeline job.
- ✅ Execute a pipeline manually.
- ✅ Configure GitHub webhook integration.
- ✅ Trigger Jenkins automatically after a Git push.

---

# Summary

In this lab, you learned how to:

- Create Jenkins declarative pipelines.
- Automate Docker image creation and deployment.
- Securely manage Jenkins credentials.
- Integrate Jenkins with GitHub.
- Configure webhook-based CI/CD automation.

The Jenkins pipeline created in this lab represents a complete CI/CD workflow that will be extended further with Kubernetes deployments in upcoming labs.

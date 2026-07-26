# Lab 10: Creating and Deploying a Docker Image

# Overview

In this lab, you will learn how to create a custom Docker image for an application, build the image using a Dockerfile, test it locally, publish it to Docker Hub, and deploy it on another server.

This workflow represents a common container deployment process used in modern DevOps and CI/CD environments.

---

# Learning Objectives

After completing this lab, you will be able to:

- Create application files for containerization.
- Write a Dockerfile for a Python application.
- Build a custom Docker image.
- Run and test a Docker container locally.
- Push Docker images to Docker Hub.
- Pull and run Docker images on another server.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 9: Introduction to Docker and Containerization**.
- Docker installed and configured on your EC2 instance.
- A Docker Hub account.
- Basic understanding of Docker commands.

---

# Docker Image Workflow

The workflow followed in this lab:

```text
Application Code
        |
        v
    Dockerfile
        |
        v
 Docker Image Build
        |
        v
 Docker Container
        |
        v
 Docker Hub Registry
        |
        v
 Deployment on Any Server
```

---

# Lab Steps

## Step 1: Create Application Files

Create a project directory.

```bash
mkdir docker-app && cd docker-app
```

Create the Python Flask application.

```bash
echo "from flask import Flask
app = Flask(__name__)
@app.route('/')
def hello(): return '<h1>Hello from Docker!</h1>'
if __name__ == '__main__': app.run(host='0.0.0.0', port=5000)" > app.py
```

Create the dependency file.

```bash
echo "flask" > requirements.txt
```

Verify the project files.

```bash
ls -la
```

Expected files:

```text
docker-app/
├── app.py
└── requirements.txt
```

---

## Step 2: Write the Dockerfile

Create a Dockerfile.

```bash
nano Dockerfile
```

Add the following content:

```dockerfile
FROM python:3.11-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install -r requirements.txt

COPY . .

EXPOSE 5000

CMD ["python", "app.py"]
```

### Dockerfile Explanation

| Instruction | Description |
|-------------|-------------|
| `FROM` | Defines the base image. |
| `WORKDIR` | Sets the working directory inside the container. |
| `COPY` | Copies application files into the container. |
| `RUN` | Executes commands during image creation. |
| `EXPOSE` | Documents the application port. |
| `CMD` | Defines the default command when the container starts. |

---

## Step 3: Build the Docker Image

Build the Docker image.

```bash
docker build -t flask-devops:1.0 .
```

Verify that the image was created.

```bash
docker images
```

Expected output:

```text
flask-devops   1.0
```

---

## Step 4: Test the Docker Image Locally

Run the Docker container.

```bash
docker run -d -p 5000:5000 --name flask-app flask-devops:1.0
```

Verify that the container is running.

```bash
docker ps
```

Test the application.

```bash
curl http://localhost:5000
```

Expected output:

```html
<h1>Hello from Docker!</h1>
```

To access the application from a browser:

1. Open the EC2 Security Group.
2. Allow inbound traffic on port:

```text
5000
```

3. Open:

```text
http://<EC2_PUBLIC_IP>:5000
```

---

## Step 5: Login to Docker Hub

Create a Docker Hub account if you do not already have one.

Docker Hub:

```text
hub.docker.com
```

Login from the terminal.

```bash
docker login
```

Enter:

- Docker Hub username
- Docker Hub password

---

## Step 6: Tag and Push the Docker Image

Tag the local image with your Docker Hub repository name.

```bash
docker tag flask-devops:1.0 <dockerhub-username>/flask-devops:1.0
```

Push the image to Docker Hub.

```bash
docker push <dockerhub-username>/flask-devops:1.0
```

Verify the image on Docker Hub.

```text
https://hub.docker.com
```

---

## Step 7: Pull and Run the Image on Another Server

On a fresh EC2 instance or another Docker-enabled server, pull the image.

```bash
docker pull <dockerhub-username>/flask-devops:1.0
```

Run the container.

```bash
docker run -d -p 5000:5000 <dockerhub-username>/flask-devops:1.0
```

Verify the running container.

```bash
docker ps
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Create application files for a Docker project.
- ✅ Write a Dockerfile.
- ✅ Build a custom Docker image.
- ✅ Run and test the container locally.
- ✅ Configure EC2 networking for application access.
- ✅ Login to Docker Hub.
- ✅ Tag and push an image to Docker Hub.
- ✅ Pull and run the image on another server.

---

# Summary

In this lab, you learned how to:

- Containerize a Python application using Docker.
- Create a Dockerfile with application dependencies and runtime instructions.
- Build a custom Docker image.
- Run and test Docker containers.
- Publish images to Docker Hub.
- Deploy the same container image on another server.

This container image workflow is a core concept used in CI/CD pipelines, Kubernetes deployments, and cloud-native application delivery.

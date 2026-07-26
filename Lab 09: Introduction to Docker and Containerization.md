# Lab 9: Introduction to Docker and Containerization

# Overview

In this lab, you will learn the fundamentals of Docker and containerization. You will install Docker on an Ubuntu EC2 instance, understand Docker architecture, run containers, manage images, and inspect container logs and networking information.

Docker enables developers and DevOps engineers to package applications and their dependencies into lightweight, portable containers that can run consistently across different environments.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install Docker on an Ubuntu EC2 instance.
- Understand Docker architecture, including:
  - Images
  - Containers
  - Registries
- Pull and run Docker containers.
- Manage Docker images and containers.
- Inspect container logs and networking details.

---

# Prerequisites

Before starting this lab, ensure that you have:

- An Ubuntu EC2 instance running on AWS.
- SSH access configured.
- Sudo privileges on the EC2 instance.
- Basic Linux command-line knowledge.

---

# Docker Architecture Overview

Docker consists of several key components:

| Component | Description |
|-----------|-------------|
| Docker Image | A read-only template containing application code, dependencies, and configuration. |
| Docker Container | A running instance of a Docker image. |
| Docker Registry | A repository used to store and distribute Docker images, such as Docker Hub. |
| Docker Engine | The runtime responsible for creating and managing containers. |

---

# Lab Steps

## Step 1: Install Docker on Ubuntu EC2

Update the package repository.

```bash
sudo apt update
```

Install required packages.

```bash
sudo apt install -y ca-certificates curl gnupg
```

Create the Docker keyring directory.

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Add the Docker GPG key.

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
| sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Add the Docker repository.

```bash
echo "deb [arch=$(dpkg --print-architecture)
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo $VERSION_CODENAME) stable" \
| sudo tee /etc/apt/sources.list.d/docker.list
```

Update package information.

```bash
sudo apt update
```

Install Docker Engine.

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

Verify the Docker installation.

```bash
docker --version
```

---

## Step 2: Add User to Docker Group

Add the Ubuntu user to the Docker group.

```bash
sudo usermod -aG docker ubuntu
```

Reload the group configuration without logging out.

```bash
newgrp docker
```

Verify Docker access.

```bash
docker run hello-world
```

Expected result:

```text
Hello from Docker!
```

This confirms that Docker is installed and running successfully.

---

## Step 3: Explore Docker Commands

### List Local Docker Images

```bash
docker images
```

### View Running Containers

```bash
docker ps
```

### View All Containers

```bash
docker ps -a
```

### Download an Image from Docker Hub

```bash
docker pull nginx
```

Verify the downloaded image.

```bash
docker images
```

---

## Step 4: Run a Docker Container

Run an Nginx container.

```bash
docker run -d -p 80:80 --name my-nginx nginx
```

Command options:

| Option | Description |
|--------|-------------|
| `-d` | Runs the container in detached mode (background). |
| `-p 80:80` | Maps the host port to the container port. |
| `--name` | Assigns a custom container name. |

Verify that the container is running.

```bash
docker ps
```

Test the Nginx service.

```bash
curl http://localhost:80
```

---

## Step 5: Inspect and Manage Containers

View container logs.

```bash
docker logs my-nginx
```

Inspect container details.

```bash
docker inspect my-nginx
```

Access the container shell.

```bash
docker exec -it my-nginx bash
```

Inside the container, view the Nginx directory.

```bash
ls /etc/nginx
```

Exit the container shell.

```bash
exit
```

---

## Step 6: Stop, Start, and Remove Containers

Stop the running container.

```bash
docker stop my-nginx
```

Start the container again.

```bash
docker start my-nginx
```

Stop the container.

```bash
docker stop my-nginx
```

Remove the container.

```bash
docker rm my-nginx
```

Remove the Docker image.

```bash
docker rmi nginx
```

---

# Important

Ensure that the EC2 Security Group allows inbound traffic on port:

```text
80
```

This is required to access the Nginx container from a web browser.

Security Group configuration:

| Type | Port | Source |
|------|------|--------|
| HTTP | 80 | Anywhere or required IP range |

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install Docker on Ubuntu EC2.
- ✅ Configure Docker user permissions.
- ✅ Pull images from Docker Hub.
- ✅ Run Docker containers.
- ✅ View running and stopped containers.
- ✅ Inspect container logs and configuration.
- ✅ Access a container shell.
- ✅ Stop, start, and remove containers.
- ✅ Remove Docker images.

---

# Summary

In this lab, you learned how to:

- Install and configure Docker on an AWS EC2 instance.
- Understand Docker images, containers, and registries.
- Download images from Docker Hub.
- Run and manage Docker containers.
- Inspect container logs and configuration.
- Perform basic Docker lifecycle operations.

Docker containerization concepts learned in this lab will be used in future CI/CD and Kubernetes labs.

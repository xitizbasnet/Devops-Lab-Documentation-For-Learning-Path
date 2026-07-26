# Lab 11: Running Multi-Container Applications with Docker Compose

# Overview

In this lab, you will learn how to run multi-container applications using Docker Compose. You will create a Flask web application connected to a MongoDB database, define application services using a `docker-compose.yml` file, and manage containers, volumes, and networking.

Docker Compose simplifies the process of defining and running applications that require multiple interconnected containers.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install Docker Compose on an EC2 instance.
- Create a `docker-compose.yml` file for multi-service applications.
- Run Flask and MongoDB services together using Docker Compose.
- Configure persistent storage using volumes.
- Manage container networking with Docker Compose.
- Control the lifecycle of multi-container applications.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 9: Introduction to Docker and Containerization**.
- Completed **Lab 10: Creating and Deploying a Docker Image**.
- Docker installed on your EC2 instance.
- Basic understanding of Docker containers and images.

---

# Docker Compose Architecture

The application architecture used in this lab:

```text
             Client
               |
               |
          Flask Container
               |
               |
        Docker Network
               |
               |
         MongoDB Container
               |
               |
        Persistent Volume
```

Components:

| Component | Purpose |
|-----------|---------|
| Flask | Web application service |
| MongoDB | Database service |
| Docker Compose | Container orchestration |
| Volume | Persistent database storage |
| Network | Communication between containers |

---

# Lab Steps

## Step 1: Install Docker Compose

Download Docker Compose.

```bash
sudo curl -L \
"https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" \
-o /usr/local/bin/docker-compose
```

Grant execute permissions.

```bash
sudo chmod +x /usr/local/bin/docker-compose
```

Verify the installation.

```bash
docker-compose --version
```

---

## Step 2: Create Project Directory

Create the application directory.

```bash
mkdir compose-app && cd compose-app
```

Create a directory for the Flask application.

```bash
mkdir app
```

Project structure:

```text
compose-app/
├── app/
└── docker-compose.yml
```

---

## Step 3: Create the Flask Application

Create the Flask application file.

```bash
nano app/app.py
```

Add the following content:

```python
from flask import Flask
from pymongo import MongoClient

app = Flask(__name__)

client = MongoClient('mongodb://mongo:27017/')

db = client['devopsdb']

@app.route('/')
def index():
    db.hits.insert_one({'hit': 1})
    return f'Hits: {db.hits.count_documents({})}'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### Application Explanation

| Component | Description |
|-----------|-------------|
| Flask | Provides the web application interface. |
| MongoClient | Connects Flask to MongoDB. |
| MongoDB | Stores application hit count data. |
| Port 5000 | Flask application port. |

---

## Step 4: Create Dockerfile for the Application

Create the Dockerfile.

```bash
nano app/Dockerfile
```

Add the following content:

```dockerfile
FROM python:3.11-slim

WORKDIR /app

RUN pip install flask pymongo

COPY app.py .

EXPOSE 5000

CMD ["python", "app.py"]
```

### Dockerfile Explanation

| Instruction | Description |
|-------------|-------------|
| `FROM` | Uses Python 3.11 slim as the base image. |
| `WORKDIR` | Sets the working directory. |
| `RUN` | Installs Flask and MongoDB client libraries. |
| `COPY` | Copies the application file. |
| `EXPOSE` | Documents the application port. |
| `CMD` | Starts the Flask application. |

---

## Step 5: Create the Docker Compose Configuration

Create the Compose file.

```bash
nano docker-compose.yml
```

Add the following content:

```yaml
version: '3.8'

services:

  web:
    build: ./app
    ports:
      - '5000:5000'
    depends_on:
      - mongo
    networks:
      - app-net

  mongo:
    image: mongo:6
    volumes:
      - mongo-data:/data/db
    networks:
      - app-net

volumes:
  mongo-data:

networks:
  app-net:
```

---

## Docker Compose Configuration Explanation

### Services

| Service | Description |
|---------|-------------|
| `web` | Flask application container. |
| `mongo` | MongoDB database container. |

### Ports

```yaml
ports:
  - '5000:5000'
```

Maps:

```text
Host Port → Container Port
5000      → 5000
```

### Volumes

```yaml
volumes:
  - mongo-data:/data/db
```

Stores MongoDB data outside the container lifecycle.

### Networks

```yaml
networks:
  - app-net
```

Allows containers to communicate using service names.

---

# Step 6: Run the Application Using Docker Compose

Start all services in detached mode.

```bash
docker-compose up -d
```

Verify running services.

```bash
docker-compose ps
```

View Flask service logs.

```bash
docker-compose logs web
```

Test the application.

```bash
curl http://localhost:5000
```

Expected output:

```text
Hits: 1
```

Each request increases the stored hit count in MongoDB.

---

# Step 7: Docker Compose Lifecycle Commands

## Stop Services

```bash
docker-compose stop
```

Stops running containers without removing them.

---

## Start Services

```bash
docker-compose start
```

Starts previously stopped containers.

---

## Stop and Remove Containers

```bash
docker-compose down
```

Stops and removes containers created by Compose.

---

## Remove Containers and Volumes

```bash
docker-compose down -v
```

Removes:

- Containers
- Networks
- Volumes

> **Warning**
>
> Using `docker-compose down -v` permanently removes stored volume data.

---

## Rebuild Images

```bash
docker-compose build
```

Rebuilds application images after changes.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install Docker Compose.
- ✅ Create a multi-container application structure.
- ✅ Build a Flask application container.
- ✅ Configure MongoDB as a database service.
- ✅ Create a Docker Compose configuration.
- ✅ Run multiple containers together.
- ✅ Configure Docker volumes for persistent storage.
- ✅ Configure container networking.
- ✅ Manage application lifecycle using Compose commands.

---

# Summary

In this lab, you learned how to:

- Install and configure Docker Compose.
- Build a multi-container application environment.
- Connect a Flask application with MongoDB.
- Define services, networks, and volumes using `docker-compose.yml`.
- Run and manage multiple containers as a single application stack.
- Control container lifecycle using Docker Compose commands.

Docker Compose concepts learned in this lab are foundational for deploying complex applications and will be used in Kubernetes-based deployments.

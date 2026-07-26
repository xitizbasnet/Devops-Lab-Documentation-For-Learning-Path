# Lab 12: Setting Up Jenkins for Build Automation

# Overview

In this lab, you will install and configure Jenkins on an AWS EC2 instance. You will complete the initial Jenkins setup wizard, install essential plugins, and configure required build tools such as JDK, Maven, and Git.

Jenkins is an open-source automation server widely used in CI/CD pipelines to automate software building, testing, and deployment processes.

---

# Learning Objectives

After completing this lab, you will be able to:

- Launch an EC2 instance for Jenkins.
- Install and configure Jenkins on Ubuntu.
- Complete the Jenkins initial setup wizard.
- Install essential Jenkins plugins.
- Configure build tools including:
  - JDK
  - Maven
  - Git

---

# Prerequisites

Before starting this lab, ensure that you have:

- An AWS account with EC2 access.
- SSH access configured with the `devops-key.pem` key pair.
- Basic Linux administration knowledge.
- Understanding of Java and Maven build tools.

---

# Jenkins Architecture Overview

A basic Jenkins environment consists of:

| Component | Description |
|-----------|-------------|
| Jenkins Server | Central automation server that runs jobs and pipelines. |
| Plugins | Extensions that add functionality to Jenkins. |
| Build Tools | Tools such as JDK, Maven, and Git used during builds. |
| Agents | Machines that execute Jenkins tasks. |

---

# Lab Steps

## Step 1: Launch an EC2 Instance for Jenkins

Launch a new EC2 instance.

Navigate to:

```text
AWS Console → EC2 → Launch Instance
```

Configure the instance:

| Configuration | Value |
|--------------|-------|
| Name | `jenkins-server` |
| AMI | Ubuntu 22.04 LTS |
| Instance Type | `t2.micro` (or `t2.medium` for better performance) |
| Key Pair | `devops-key` |

Configure the Security Group:

| Type | Port | Purpose |
|------|------|---------|
| SSH | 22 | Server administration |
| Custom TCP | 8080 | Jenkins Web Interface |

Launch the instance.

---

## Step 2: SSH into Jenkins Server and Install Java

Connect to the EC2 instance.

```bash
ssh -i devops-key.pem ubuntu@<JENKINS_IP>
```

Update the package repository.

```bash
sudo apt update
```

Install Java Development Kit.

```bash
sudo apt install openjdk-17-jdk -y
```

Verify Java installation.

```bash
java -version
```

---

# Step 3: Install Jenkins

Download the Jenkins repository key.

```bash
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
```

Add the Jenkins repository.

```bash
echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
https://pkg.jenkins.io/debian-stable binary/" \
| sudo tee /etc/apt/sources.list.d/jenkins.list
```

Update package information.

```bash
sudo apt update
```

Install Jenkins.

```bash
sudo apt install jenkins -y
```

---

# Step 4: Start and Enable Jenkins

Enable Jenkins to start automatically after reboot.

```bash
sudo systemctl enable jenkins
```

Start the Jenkins service.

```bash
sudo systemctl start jenkins
```

Check Jenkins service status.

```bash
sudo systemctl status jenkins
```

---

# Step 5: Retrieve Initial Jenkins Administrator Password

Retrieve the initial administrator password.

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Copy the displayed password.

This password is required during the initial Jenkins setup wizard.

---

# Step 6: Complete Jenkins Setup Wizard

Open a browser and navigate to:

```text
http://<JENKINS_IP>:8080
```

Complete the setup process:

1. Paste the administrator password from the previous step.
2. Select:

```text
Install suggested plugins
```

3. Wait approximately **3–5 minutes** for plugin installation.

Create the administrator user:

| Setting | Value |
|---------|-------|
| Username | `admin` |
| Password | `Admin@123` |
| Full Name | `Vinod Muleva` |
| Email | `your@email.com` |

4. Click:

```text
Save and Continue
```

5. Click:

```text
Start using Jenkins
```

---

# Step 7: Install Additional Jenkins Plugins

Navigate to:

```text
Jenkins Dashboard → Manage Jenkins → Plugins
```

Select:

```text
Available Plugins
```

Search and install the following plugins:

| Plugin | Purpose |
|--------|---------|
| Docker Pipeline | Docker build and deployment support |
| Amazon ECR | AWS container registry integration |
| GitHub Integration | GitHub repository integration |
| Maven Integration | Maven build support |

Click:

```text
Install without restart
```

---

# Step 8: Configure Tools in Jenkins

Navigate to:

```text
Manage Jenkins → Tools
```

---

## Configure JDK

Add a JDK installation.

| Setting | Value |
|---------|-------|
| Name | `JDK-17` |
| JAVA_HOME | `/usr/lib/jvm/java-17-openjdk-amd64` |

---

## Configure Maven

Add a Maven installation.

| Setting | Value |
|---------|-------|
| Name | `Maven-3.9` |
| Install automatically | Enabled |

---

## Configure Git

Git is usually detected automatically by Jenkins.

Verify the Git installation configuration.

---

Click:

```text
Save
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Launch an EC2 instance for Jenkins.
- ✅ Install Java 17.
- ✅ Install Jenkins on Ubuntu.
- ✅ Start and enable the Jenkins service.
- ✅ Access the Jenkins Web UI.
- ✅ Complete the Jenkins setup wizard.
- ✅ Install required Jenkins plugins.
- ✅ Configure JDK, Maven, and Git tools.

---

# Summary

In this lab, you learned how to:

- Deploy Jenkins on an AWS EC2 instance.
- Configure Jenkins for CI/CD automation.
- Install and manage Jenkins plugins.
- Configure essential build tools.
- Prepare Jenkins for creating automated build pipelines.

The Jenkins server configured in this lab will be used in upcoming CI/CD pipeline exercises.

# Lab 8: Setting Up and Running Nexus on a Cloud Server

# Overview

In this lab, you will deploy Sonatype Nexus Repository Manager on an AWS EC2 instance. You will configure Nexus as an artifact repository, create hosted Maven and npm repositories, and publish application artifacts using Maven.

Nexus Repository Manager is commonly used in CI/CD pipelines to store, manage, and distribute build artifacts such as JAR files, packages, and dependencies.

---

# Learning Objectives

After completing this lab, you will be able to:

- Launch a dedicated EC2 instance for Nexus Repository Manager.
- Install and configure Nexus OSS.
- Run Nexus as a Linux system service.
- Access and configure the Nexus Web UI.
- Create hosted Maven and npm repositories.
- Publish artifacts to Nexus using Maven.

---

# Prerequisites

Before starting this lab, ensure that you have:

- An AWS account with EC2 access.
- SSH access configured with the `devops-key.pem` key pair.
- Basic Linux administration knowledge.
- Completed previous AWS, Linux, Git, and Build Tool labs.

---

# Lab Steps

## Step 1: Launch an EC2 Instance for Nexus

Launch a dedicated EC2 instance for Nexus Repository Manager.

Navigate to:

```text
AWS Console → EC2 → Launch Instance
```

Configure the instance with the following settings:

| Configuration | Value |
|--------------|-------|
| Name | `nexus-server` |
| AMI | Amazon Linux 2 or Ubuntu 22.04 |
| Instance Type | `t2.medium` |
| Storage | 20 GB |
| Key Pair | `devops-key` |

> **Important**
>
> Nexus requires sufficient memory resources. A minimum of **4 GB RAM** is recommended for stable operation.

Configure the Security Group:

| Type | Port | Purpose |
|------|------|---------|
| SSH | 22 | Server administration |
| Custom TCP | 8081 | Nexus Web Interface |

Launch the instance.

---

## Step 2: SSH into the Nexus Server and Install Java

Connect to the EC2 instance.

```bash
ssh -i devops-key.pem ubuntu@<NEXUS_IP>
```

Update the package repository.

```bash
sudo apt update
```

Install Java 8.

```bash
sudo apt install openjdk-8-jdk -y
```

Verify the Java installation.

```bash
java -version
```

---

## Step 3: Download and Install Nexus Repository Manager

Navigate to the `/opt` directory.

```bash
cd /opt
```

Download the latest Nexus package.

```bash
sudo wget https://download.sonatype.com/nexus/3/latest-unix.tar.gz
```

Extract the Nexus archive.

```bash
sudo tar -xvf latest-unix.tar.gz
```

Rename the extracted Nexus directory.

```bash
sudo mv nexus-3* nexus
```

Move the Nexus data directory.

```bash
sudo mv sonatype-work /opt/sonatype-work
```

The installation structure should look like:

```text
/opt/
├── nexus
└── sonatype-work
```

---

## Step 4: Create a Nexus User

Create a dedicated Linux user for running Nexus.

```bash
sudo adduser nexus
```

Change ownership of Nexus directories.

```bash
sudo chown -R nexus:nexus /opt/nexus
```

```bash
sudo chown -R nexus:nexus /opt/sonatype-work
```

> **Note**
>
> Running Nexus with a dedicated user improves security by avoiding execution with root privileges.

---

## Step 5: Configure Nexus to Run as a Service

Open the Nexus runtime configuration file.

```bash
sudo nano /opt/nexus/bin/nexus.rc
```

Add the following configuration:

```bash
run_as_user="nexus"
```

Create a systemd service file.

```bash
sudo nano /etc/systemd/system/nexus.service
```

Add the following content.

---

## Step 6: Nexus Service File Configuration

```ini
[Unit]
Description=Nexus Service

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort

[Install]
WantedBy=multi-user.target
```

Save and close the file.

---

## Step 7: Start the Nexus Service

Reload systemd configurations.

```bash
sudo systemctl daemon-reload
```

Enable Nexus to start automatically after reboot.

```bash
sudo systemctl enable nexus
```

Start the Nexus service.

```bash
sudo systemctl start nexus
```

Check the service status.

```bash
sudo systemctl status nexus
```

Wait approximately **2–3 minutes** for Nexus to complete startup.

---

## Step 8: Access the Nexus Web Interface

Open a browser and navigate to:

```text
http://<NEXUS_IP>:8081
```

Use the default administrator credentials.

| Setting | Value |
|---------|-------|
| Username | `admin` |
| Password | Retrieve from admin password file |

Retrieve the initial password:

```bash
cat /opt/sonatype-work/nexus3/admin.password
```

> **Important**
>
> Change the default admin password after the first login.

---

## Step 9: Create a Hosted Maven Repository

In the Nexus Web UI:

```text
Settings (gear icon) → Repositories → Create Repository
```

Configure the repository:

| Setting | Value |
|---------|-------|
| Recipe | `maven2 (hosted)` |
| Name | `maven-releases` |
| Version Policy | Release |

Create the repository.

---

## Step 10: Configure Maven to Publish Artifacts to Nexus

In your Maven project, edit the `pom.xml` file.

Add the following configuration:

```xml
<distributionManagement>
    <repository>
        <id>nexus</id>
        <url>http://<NEXUS_IP>:8081/repository/maven-releases/</url>
    </repository>
</distributionManagement>
```

Replace:

```text
<NEXUS_IP>
```

with the public IP address of your Nexus server.

---

## Configure Nexus Credentials for Maven

Edit the Maven settings file:

```bash
nano ~/.m2/settings.xml
```

Add your Nexus credentials.

After configuration, publish the artifact:

```bash
mvn deploy
```

The Maven artifact will be uploaded to the Nexus hosted repository.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Launch an EC2 instance for Nexus.
- ✅ Install Java on the server.
- ✅ Download and configure Nexus Repository Manager.
- ✅ Create a dedicated Nexus service user.
- ✅ Configure Nexus as a systemd service.
- ✅ Access the Nexus Web UI.
- ✅ Retrieve the initial admin password.
- ✅ Create a hosted Maven repository.
- ✅ Configure Maven deployment to Nexus.
- ✅ Publish artifacts using Maven.

---

# Summary

In this lab, you learned how to:

- Deploy Nexus Repository Manager on AWS EC2.
- Configure Nexus as an artifact repository server.
- Run Nexus as a Linux service.
- Manage repository configurations through the Nexus UI.
- Create hosted Maven repositories.
- Publish build artifacts using Maven.

Nexus will be used in future CI/CD workflows as a centralized artifact management solution.

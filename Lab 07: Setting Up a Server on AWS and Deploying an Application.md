# Lab 7: Setting Up a Server on AWS and Deploying an Application

# Overview

In this lab, you will create an application server on AWS EC2, configure it as a deployment environment, install a web server using Nginx, deploy a Java JAR application, and expose the application through a public IP address or Elastic IP.

This lab introduces the basic workflow of deploying applications to cloud infrastructure, including server provisioning, application deployment, and reverse proxy configuration.

---

# Learning Objectives

After completing this lab, you will be able to:

- Launch an EC2 instance for application deployment.
- Install and configure a web server using Nginx.
- Deploy a Java JAR application on an EC2 instance.
- Configure Nginx as a reverse proxy.
- Access an application using a public IP address or Elastic IP.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed previous AWS, Linux, Git, and Build Tool labs.
- An AWS account with EC2 access.
- A generated SSH key pair (`devops-key.pem`) from Lab 1.
- A Java JAR artifact created from Lab 6.

---

# Lab Steps

## Step 1: Launch a New EC2 Instance for the Application Server

Navigate to:

```text
AWS Console → EC2 → Launch Instance
```

Configure the instance using the following settings:

| Configuration | Value |
|--------------|-------|
| Name | `app-server` |
| AMI | Ubuntu 22.04 LTS (Free Tier) |
| Instance Type | `t2.micro` |
| Key Pair | `devops-key` (reuse from Lab 1) |

Configure the Security Group with the following inbound rules:

| Type | Port | Purpose |
|------|------|---------|
| SSH | 22 | Remote server access |
| HTTP | 80 | Web traffic |
| Custom TCP | 8080 | Application access |

---

## Step 2: SSH into the Application Server

Connect to the EC2 instance using SSH.

```bash
ssh -i devops-key.pem ubuntu@<APP_SERVER_PUBLIC_IP>
```

Replace:

```text
<APP_SERVER_PUBLIC_IP>
```

with the public IP address of your EC2 instance.

---

## Step 3: Install Java Runtime Environment

Update the package repository.

```bash
sudo apt update
```

Install Java Runtime Environment.

```bash
sudo apt install openjdk-17-jre -y
```

Verify the Java installation.

```bash
java -version
```

---

## Step 4: Transfer the JAR Artifact to the Server

Copy the JAR file from your local machine or Lab EC2 instance to the application server.

```bash
scp -i devops-key.pem my-app/target/my-app-1.0-SNAPSHOT.jar \
ubuntu@<APP_SERVER_IP>:/home/ubuntu/
```

Verify that the JAR file exists on the server.

```bash
ls -la
```

---

## Step 5: Run the Application

Start the Java application.

```bash
java -jar my-app-1.0-SNAPSHOT.jar
```

To run the application in the background, use:

```bash
nohup java -jar my-app-1.0-SNAPSHOT.jar &
```

Display the background process ID.

```bash
echo $!
```

> **Note**
>
> Running the application with `nohup` allows the process to continue running even after the SSH session is closed.

---

## Step 6: Install Nginx as a Reverse Proxy

Install Nginx.

```bash
sudo apt install nginx -y
```

Start the Nginx service.

```bash
sudo systemctl start nginx
```

Enable Nginx to start automatically after reboot.

```bash
sudo systemctl enable nginx
```

---

## Configure Nginx Reverse Proxy

Open the default Nginx configuration file.

```bash
sudo nano /etc/nginx/sites-available/default
```

Add the following configuration inside the `server` block:

```nginx
location / {
    proxy_pass http://localhost:8080;
}
```

Save the file and restart Nginx.

```bash
sudo systemctl restart nginx
```

---

## Step 7: Test the Deployment

Open a browser and access:

```text
http://<APP_SERVER_PUBLIC_IP>
```

Alternatively, test using `curl`.

Test locally on the server:

```bash
curl http://localhost:8080
```

Test using the public IP:

```bash
curl http://<PUBLIC_IP>
```

---

## Step 8: Assign an Elastic IP (Optional but Recommended)

An Elastic IP provides a static public IP address that remains associated with your AWS account.

Navigate to:

```text
AWS Console → EC2 → Elastic IPs → Allocate
```

Assign the Elastic IP:

```text
Select the new IP → Actions → Associate Elastic IP
```

Choose your application server instance and complete the association.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Launch an EC2 application server.
- ✅ Connect to the server using SSH.
- ✅ Install Java Runtime Environment.
- ✅ Transfer a JAR artifact to the server.
- ✅ Run a Java application on EC2.
- ✅ Install and configure Nginx.
- ✅ Configure Nginx as a reverse proxy.
- ✅ Access the deployed application using a public IP.
- ✅ Associate an Elastic IP with the application server.

---

# Summary

In this lab, you learned how to:

- Provision an AWS EC2 instance for application hosting.
- Configure a Linux server for application deployment.
- Deploy a Java JAR application.
- Run applications as background services.
- Configure Nginx as a reverse proxy.
- Expose cloud-hosted applications through public networking.
- Use Elastic IP addresses for stable application access.

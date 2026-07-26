# Lab 16: Setting Up and Running a Managed EKS Cluster

# Overview

In this lab, you will create and manage a production-style Kubernetes environment using Amazon Elastic Kubernetes Service (EKS).

You will install AWS CLI and `eksctl`, configure AWS credentials, create a managed EKS cluster, configure worker node groups, deploy an application, and verify the Kubernetes workload running on AWS.

Amazon EKS provides a fully managed Kubernetes control plane while allowing you to run containerized applications using AWS infrastructure.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install AWS CLI on an EC2 instance.
- Configure AWS CLI using IAM credentials.
- Install and use `eksctl` for EKS management.
- Create a managed EKS cluster.
- Configure managed node groups.
- Deploy applications to EKS.
- Verify Kubernetes resources on AWS EKS.
- Delete EKS resources to prevent unnecessary charges.

---

# Prerequisites

Before starting this lab, ensure that you have:

- An AWS account with permission to create EKS resources.
- An Ubuntu EC2 instance for running administrative tools.
- AWS IAM user credentials.
- Basic Kubernetes knowledge from previous labs.
- Completed:
  - Lab 14: Introduction to Kubernetes and Local Cluster Setup
  - Lab 15: Deploying an Application in a Kubernetes Cluster

---

# EKS Architecture Overview

The EKS architecture used in this lab:

```text
                 AWS Account
                      |
                      |
              Amazon EKS Control Plane
                      |
        --------------------------------
        |                              |
        |                              |
 Managed Node Group              Kubernetes API
        |
        |
   EC2 Worker Nodes
        |
        |
 Kubernetes Pods
        |
        |
 Application Containers
```

---

# Components Used

| Component | Description |
|-----------|-------------|
| Amazon EKS | Managed Kubernetes service provided by AWS. |
| eksctl | Command-line utility for creating and managing EKS clusters. |
| AWS CLI | Command-line tool for managing AWS services. |
| IAM User | Provides permissions required for AWS operations. |
| Node Group | Collection of worker nodes running Kubernetes workloads. |

---

# Lab Steps

## Step 1: Install AWS CLI

Download AWS CLI version 2.

```bash
curl 'https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip' \
-o 'awscliv2.zip'
```

Install unzip package.

```bash
sudo apt install unzip -y
```

Extract AWS CLI package.

```bash
unzip awscliv2.zip
```

Install AWS CLI.

```bash
sudo ./aws/install
```

Verify installation.

```bash
aws --version
```

Expected output:

```text
aws-cli/2.x.x
```

---

# Step 2: Configure AWS CLI with IAM User

Configure AWS CLI credentials.

```bash
aws configure
```

Enter the following values:

```text
AWS Access Key ID: <your-access-key>

AWS Secret Access Key: <your-secret-key>

Default region name: ap-south-1

Default output format: json
```

---

# Create IAM User (If Required)

To create an IAM user:

Navigate to:

```text
AWS Console → IAM → Users → Create User
```

Configure permissions:

```text
Permissions:
AdministratorAccess
```

Create an access key:

```text
Create Access Key → Download CSV
```

Use the generated credentials with:

```bash
aws configure
```

---

# Step 3: Install eksctl

`eksctl` is a command-line tool used to create and manage Amazon EKS clusters.

Set the architecture.

```bash
ARCH=amd64
```

Set the platform.

```bash
PLATFORM=$(uname -s)_$ARCH
```

Download `eksctl`.

```bash
curl -sLO \
"https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_${PLATFORM}.tar.gz"
```

Extract the package.

```bash
tar -xzf eksctl_${PLATFORM}.tar.gz
```

Move `eksctl` to the system path.

```bash
sudo mv eksctl /usr/local/bin
```

Verify installation.

```bash
eksctl version
```

---

# Step 4: Create an EKS Cluster

Create a managed EKS cluster.

```bash
eksctl create cluster \
--name devops-cluster \
--region ap-south-1 \
--nodegroup-name standard-nodes \
--node-type t3.medium \
--nodes 2 \
--nodes-min 1 \
--nodes-max 3 \
--managed
```

Cluster creation may take approximately:

```text
15–20 minutes
```

The command automatically creates:

- EKS control plane
- Managed node group
- IAM roles
- Networking configuration
- Kubernetes configuration

---

# Step 5: Verify the EKS Cluster

Verify Kubernetes worker nodes.

```bash
kubectl get nodes
```

Expected output:

```text
NAME                                      STATUS
ip-xxx-xxx-xxx-xxx.compute.internal       Ready
```

---

View Kubernetes namespaces.

```bash
kubectl get namespaces
```

---

Verify the EKS cluster using `eksctl`.

```bash
eksctl get cluster
```

---

List EKS clusters using AWS CLI.

```bash
aws eks list-clusters --region ap-south-1
```

---

# Step 6: Deploy Application to EKS

Deploy the application Deployment manifest.

```bash
kubectl apply -f flask-deployment.yaml
```

Deploy the Service manifest.

```bash
kubectl apply -f flask-service.yaml
```

Verify running Pods.

```bash
kubectl get pods
```

Verify Services.

```bash
kubectl get services
```

For LoadBalancer services, note the:

```text
EXTERNAL-IP
```

This address can be used to access the deployed application.

---

# Step 7: Delete EKS Cluster

EKS resources generate AWS charges. Delete the cluster after completing the lab.

Delete the EKS cluster:

```bash
eksctl delete cluster \
--name devops-cluster \
--region ap-south-1
```

Wait for the deletion process to complete.

---

# Important Cost Notice

> **Important**
>
> EKS clusters are not included in the AWS Free Tier.
>
> Worker nodes such as `t3.medium` instances incur charges. The approximate cost for a `t3.medium` instance is **$0.052/hour**.
>
> Always delete the EKS cluster after completing the lab to avoid unexpected billing.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install AWS CLI.
- ✅ Configure AWS CLI with IAM credentials.
- ✅ Install `eksctl`.
- ✅ Create a managed EKS cluster.
- ✅ Configure managed worker nodes.
- ✅ Connect `kubectl` to EKS.
- ✅ Deploy an application to EKS.
- ✅ Verify Pods and Services.
- ✅ Delete the EKS cluster after completion.

---

# Summary

In this lab, you learned how to:

- Create a managed Kubernetes cluster using Amazon EKS.
- Use AWS CLI and `eksctl` for cluster management.
- Configure IAM permissions for Kubernetes administration.
- Deploy applications on AWS-managed Kubernetes infrastructure.
- Manage EKS resources and control AWS costs.

The EKS cluster created in this lab provides the foundation for advanced Kubernetes CI/CD workflows and production-grade application deployments.

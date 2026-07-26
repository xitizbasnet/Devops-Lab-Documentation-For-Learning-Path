# Lab 20: Automating EKS Cluster Provisioning with Terraform

# Overview

In this lab, you will automate the provisioning of an Amazon Elastic Kubernetes Service (EKS) cluster using Terraform.

You will use official Terraform AWS modules to create the complete Kubernetes infrastructure stack, including:

- VPC networking
- Public and private subnets
- NAT Gateway
- EKS control plane
- Managed worker node groups

After provisioning the cluster, you will configure `kubectl` access, deploy an application, and cleanly remove all infrastructure using Terraform.

This lab demonstrates how Infrastructure as Code (IaC) can automate production-grade Kubernetes environments.

---

# Learning Objectives

After completing this lab, you will be able to:

- Use Terraform modules for AWS infrastructure automation.
- Provision an EKS cluster using Terraform.
- Automatically create VPC and subnet infrastructure.
- Configure managed EKS node groups.
- Connect `kubectl` to a Terraform-managed EKS cluster.
- Deploy applications on EKS.
- Destroy cloud infrastructure safely using Terraform.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed:
  - Lab 18: Introduction to Terraform and Setting Up a Project
  - Lab 19: Creating and Managing Resources with Terraform
- Terraform installed.
- AWS CLI configured.
- IAM permissions to create EKS resources.
- Basic Kubernetes knowledge.
- Understanding of Terraform modules.

---

# Terraform EKS Architecture

The infrastructure created in this lab:

```text
                     AWS Cloud

                         |
                         |
                    Terraform
                         |
        ---------------------------------
        |                               |
        v                               v
       VPC                         Amazon EKS
        |                               |
        |                         Control Plane
        |
  ----------------
  |              |
Public Subnets  Private Subnets
                      |
                      |
              Managed Node Group
                      |
                      |
             Kubernetes Worker Nodes
                      |
                      |
              Application Pods
```

---

# Resources Created

| Resource | Description |
|----------|-------------|
| VPC | Provides isolated AWS networking. |
| Public Subnets | Hosts internet-facing resources. |
| Private Subnets | Hosts Kubernetes worker nodes. |
| NAT Gateway | Allows private resources to access the internet. |
| EKS Cluster | Managed Kubernetes control plane. |
| Node Group | EC2 worker nodes running Kubernetes workloads. |

---

# Lab Steps

## Step 1: Create Project Structure

Create a Terraform project directory.

```bash
mkdir lab20-eks && cd lab20-eks
```

Create Terraform configuration files.

```bash
touch main.tf variables.tf outputs.tf
```

Project structure:

```text
lab20-eks/
│
├── main.tf
├── variables.tf
└── outputs.tf
```

---

# Step 2: Configure EKS Terraform Module

Open the Terraform configuration file.

```bash
nano main.tf
```

Add the following content:

```hcl
module "vpc" {

  source = "terraform-aws-modules/vpc/aws"

  version = "5.0.0"


  name = "eks-vpc"

  cidr = "10.0.0.0/16"


  azs = [
    "ap-south-1a",
    "ap-south-1b"
  ]


  private_subnets = [
    "10.0.1.0/24",
    "10.0.2.0/24"
  ]


  public_subnets = [
    "10.0.101.0/24",
    "10.0.102.0/24"
  ]


  enable_nat_gateway = true

}



module "eks" {

  source = "terraform-aws-modules/eks/aws"

  version = "19.0.0"


  cluster_name = "devops-eks"

  cluster_version = "1.28"


  vpc_id = module.vpc.vpc_id


  subnet_ids = module.vpc.private_subnets


  eks_managed_node_groups = {

    nodes = {

      min_size = 1

      max_size = 3

      desired_size = 2


      instance_types = [
        "t3.medium"
      ]

    }

  }

}
```

---

# Terraform Module Explanation

## VPC Module

The VPC module creates:

- AWS VPC.
- Availability Zone configuration.
- Public subnets.
- Private subnets.
- NAT Gateway.

---

## EKS Module

The EKS module creates:

- Kubernetes control plane.
- Managed worker nodes.
- Node scaling configuration.
- AWS IAM integration.

---

# Step 3: Initialize Terraform

Initialize Terraform modules and providers.

```bash
terraform init
```

Terraform downloads:

- AWS provider.
- VPC module.
- EKS module.

---

# Step 4: Review Infrastructure Plan

Generate the execution plan.

```bash
terraform plan
```

Review the resources that Terraform will create.

Expected resources include:

```text
VPC
Subnets
NAT Gateway
EKS Cluster
Node Groups
IAM Roles
Security Groups
```

---

# Step 5: Create EKS Cluster

Apply the Terraform configuration.

```bash
terraform apply -auto-approve
```

Cluster creation may take:

```text
20–30 minutes
```

Terraform will automatically provision the complete EKS environment.

---

# Step 6: Configure kubectl Access

Update the Kubernetes configuration.

```bash
aws eks update-kubeconfig \
--name devops-eks \
--region ap-south-1
```

Verify cluster connectivity.

```bash
kubectl get nodes
```

Expected output:

```text
NAME                                      STATUS
ip-xxx-xxx-xxx.compute.internal           Ready
```

---

# Step 7: Deploy Application to Terraform-Managed EKS

Deploy the application Deployment.

```bash
kubectl apply -f flask-deployment.yaml
```

Deploy the Service.

```bash
kubectl apply -f flask-service.yaml
```

Verify application Pods.

```bash
kubectl get pods
```

Verify Services.

```bash
kubectl get services
```

For LoadBalancer services, retrieve:

```text
EXTERNAL-IP
```

Use this address to access the application.

---

# Step 8: Destroy EKS Cluster

After completing the lab, remove all Terraform-managed infrastructure.

```bash
terraform destroy -auto-approve
```

Terraform automatically deletes:

- EKS cluster.
- Worker nodes.
- VPC.
- Subnets.
- NAT Gateway.
- Related AWS resources.

---

# Cost Warning

> **Important**
>
> EKS infrastructure can generate significant AWS charges.
>
> Resources such as:
>
> - EKS cluster
> - NAT Gateway
> - Load Balancer
> - EC2 worker nodes
>
> may cost approximately **$5–$10/hour** depending on usage.
>
> Always run:
>
> ```bash
> terraform destroy -auto-approve
> ```
>
> after completing the lab.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Create a Terraform EKS project.
- ✅ Use Terraform AWS modules.
- ✅ Provision VPC networking automatically.
- ✅ Create an EKS cluster.
- ✅ Configure Kubernetes access.
- ✅ Deploy an application to EKS.
- ✅ Verify Kubernetes resources.
- ✅ Destroy the complete infrastructure.

---

# Summary

In this lab, you learned how to:

- Automate EKS cluster creation using Terraform.
- Use reusable Terraform modules.
- Provision complete Kubernetes infrastructure.
- Connect Terraform-managed EKS clusters with `kubectl`.
- Deploy applications to AWS Kubernetes environments.
- Remove infrastructure safely using Terraform.

This completes the Terraform and Kubernetes infrastructure automation section and prepares you for configuration management using Ansible.

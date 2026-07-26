# Lab 18: Introduction to Terraform and Setting Up a Project

# Overview

In this lab, you will learn the fundamentals of Infrastructure as Code (IaC) using Terraform.

You will install Terraform on an AWS EC2 instance, understand the Terraform workflow, create your first Terraform configuration using HashiCorp Configuration Language (HCL), and provision an AWS S3 bucket using Terraform.

Terraform enables DevOps teams to define, provision, and manage cloud infrastructure using reusable configuration files instead of manual cloud console operations.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install Terraform on an EC2 instance.
- Understand the Terraform workflow:
  - `init`
  - `plan`
  - `apply`
  - `destroy`
- Write Terraform configuration files using HCL.
- Configure AWS providers in Terraform.
- Create AWS resources using Terraform.
- Understand Terraform state management.

---

# Prerequisites

Before starting this lab, ensure that you have:

- An AWS account.
- An Ubuntu EC2 instance.
- AWS CLI configured with valid IAM credentials.
- Basic understanding of AWS resources.
- Basic Linux command-line knowledge.

---

# Terraform Workflow Overview

Terraform follows a simple infrastructure lifecycle:

```text
Terraform Configuration
          |
          v
    terraform init
          |
          v
    terraform plan
          |
          v
    terraform apply
          |
          v
   AWS Infrastructure
          |
          v
  terraform destroy
```

---

# Terraform Commands

| Command | Purpose |
|---------|---------|
| `terraform init` | Initializes the Terraform project and downloads providers. |
| `terraform plan` | Shows planned infrastructure changes. |
| `terraform apply` | Creates or updates infrastructure. |
| `terraform destroy` | Removes Terraform-managed resources. |
| `terraform state` | Manages Terraform resource state. |

---

# Lab Steps

## Step 1: Install Terraform

Update package information.

```bash
sudo apt update && sudo apt install -y gnupg software-properties-common
```

Download the HashiCorp GPG key.

```bash
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor \
| sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

Add the HashiCorp repository.

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
| sudo tee /etc/apt/sources.list.d/hashicorp.list
```

Install Terraform.

```bash
sudo apt update && sudo apt install terraform -y
```

Verify the installation.

```bash
terraform version
```

Expected output:

```text
Terraform v1.x.x
```

---

# Step 2: Create Terraform Project

Create the Terraform project directory.

```bash
mkdir terraform-labs && cd terraform-labs
```

Create a directory for Lab 18.

```bash
mkdir lab18 && cd lab18
```

Project structure:

```text
terraform-labs/
└── lab18/
    └── main.tf
```

---

# Step 3: Create Terraform Configuration

Create the Terraform configuration file.

```bash
nano main.tf
```

Add the following content:

```hcl
terraform {

  required_providers {

    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }

  }

}

provider "aws" {

  region = "ap-south-1"

}


resource "aws_s3_bucket" "devops_bucket" {

  bucket = "vinod-devops-lab-bucket-2024"

  tags = {

    Name = "DevOps Lab Bucket"

    Environment = "Lab"

  }

}
```

---

# Terraform Configuration Explanation

| Block | Description |
|-------|-------------|
| `terraform` | Defines Terraform settings and required providers. |
| `required_providers` | Specifies external providers required by Terraform. |
| `provider` | Configures AWS connection details. |
| `resource` | Defines infrastructure resources to create. |
| `aws_s3_bucket` | Creates an AWS S3 bucket. |

---

# Step 4: Initialize Terraform

Initialize the Terraform project.

```bash
terraform init
```

Terraform will:

- Download the AWS provider plugin.
- Create the `.terraform` directory.
- Prepare the project for execution.

Verify provider files:

```bash
ls .terraform/
```

---

# Step 5: Review Terraform Plan

Generate an execution plan.

```bash
terraform plan
```

Terraform displays the resources that will be created.

Review the planned resource:

```text
+ aws_s3_bucket.devops_bucket
```

The `+` symbol indicates a new resource will be created.

---

# Step 6: Apply Terraform Configuration

Create the AWS S3 bucket.

```bash
terraform apply
```

Confirm the deployment:

```text
Enter a value: yes
```

Terraform will create the S3 bucket.

Verify the resource:

```text
AWS Console → S3 → Buckets
```

---

# Step 7: Manage Terraform State

Terraform stores information about managed resources in the state file.

---

## View Current State

```bash
terraform show
```

Displays the current Terraform-managed infrastructure.

---

## View Raw State File

```bash
cat terraform.tfstate
```

The state file contains Terraform resource information.

> **Note**
>
> Avoid manually editing `terraform.tfstate`. Terraform manages this file automatically.

---

## List Terraform Resources

```bash
terraform state list
```

Example output:

```text
aws_s3_bucket.devops_bucket
```

---

## View Terraform Outputs

```bash
terraform output
```

Displays configured Terraform outputs.

---

# Step 8: Destroy Terraform Resources

Remove the resources created by Terraform.

```bash
terraform destroy
```

Confirm deletion:

```text
Enter a value: yes
```

Terraform will delete the S3 bucket.

Verify deletion:

```text
AWS Console → S3 → Bucket List
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install Terraform on EC2.
- ✅ Configure an AWS Terraform provider.
- ✅ Create a Terraform project.
- ✅ Write an HCL configuration file.
- ✅ Initialize Terraform.
- ✅ Review infrastructure changes using `terraform plan`.
- ✅ Create AWS resources using `terraform apply`.
- ✅ Manage Terraform state.
- ✅ Remove resources using `terraform destroy`.

---

# Summary

In this lab, you learned how to:

- Set up Terraform for AWS infrastructure management.
- Understand the Terraform lifecycle workflow.
- Write Terraform configurations using HCL.
- Provision AWS resources automatically.
- Manage Terraform state information.
- Safely remove infrastructure using Terraform.

Terraform introduced in this lab will be used in upcoming labs for automated AWS resource provisioning and EKS cluster creation.

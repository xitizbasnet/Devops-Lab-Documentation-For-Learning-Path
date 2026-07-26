# Lab 19: Creating and Managing Resources with Terraform

# Overview

In this lab, you will use Terraform to create and manage AWS infrastructure resources.

You will provision a complete AWS environment including a Virtual Private Cloud (VPC), subnet, Internet Gateway, security group, and EC2 instance. You will also learn how to use Terraform variables, outputs, and resource management practices.

This lab demonstrates how Infrastructure as Code (IaC) can automate cloud infrastructure provisioning in a repeatable and controlled way.

---

# Learning Objectives

After completing this lab, you will be able to:

- Create AWS networking resources using Terraform.
- Provision EC2 instances using Terraform.
- Use Terraform variables.
- Generate Terraform outputs.
- Understand Terraform resource dependencies.
- Manage infrastructure changes using Terraform.
- Clean up resources using Terraform destroy.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 18: Introduction to Terraform and Setting Up a Project**.
- Terraform installed on an EC2 instance.
- AWS CLI configured with valid IAM credentials.
- Basic understanding of AWS networking concepts.
- An existing EC2 key pair.

---

# Infrastructure Architecture

The infrastructure created in this lab:

```text
                 AWS Cloud
                     |
                     |
                  VPC
            10.0.0.0/16
                     |
                     |
                 Subnet
            10.0.1.0/24
                     |
                     |
          Internet Gateway (IGW)
                     |
                     |
             Security Group
                     |
                     |
              EC2 Instance
            Ubuntu 22.04 LTS
```

---

# Terraform Resources Created

| Resource | Purpose |
|----------|---------|
| VPC | Creates an isolated AWS network. |
| Subnet | Provides a network segment inside the VPC. |
| Internet Gateway | Enables internet connectivity. |
| Security Group | Controls inbound and outbound traffic. |
| EC2 Instance | Creates a virtual server. |
| Variables | Stores reusable configuration values. |
| Outputs | Displays important resource information. |

---

# Lab Steps

## Step 1: Create Variables File

Create the Lab 19 directory.

```bash
mkdir lab19 && cd lab19
```

Create the variables file.

```bash
nano variables.tf
```

Add the following content:

```hcl
variable "region" {

  default = "ap-south-1"

}


variable "instance_type" {

  default = "t2.micro"

}


variable "key_name" {

  description = "EC2 Key Pair name"

  default = "devops-key"

}
```

---

# Variables Explanation

| Variable | Purpose |
|----------|---------|
| `region` | AWS deployment region. |
| `instance_type` | EC2 instance size. |
| `key_name` | SSH key pair used for EC2 access. |

---

# Step 2: Create VPC and Subnet

Create the Terraform configuration file.

```bash
nano main.tf
```

---

## Create VPC

Add the VPC resource:

```hcl
resource "aws_vpc" "devops_vpc" {

  cidr_block = "10.0.0.0/16"

  tags = {

    Name = "devops-vpc"

  }

}
```

---

## Create Subnet

Add the subnet configuration:

```hcl
resource "aws_subnet" "devops_subnet" {

  vpc_id = aws_vpc.devops_vpc.id

  cidr_block = "10.0.1.0/24"

  availability_zone = "ap-south-1a"

  map_public_ip_on_launch = true

  tags = {

    Name = "devops-subnet"

  }

}
```

---

## Create Internet Gateway

Add the Internet Gateway:

```hcl
resource "aws_internet_gateway" "devops_igw" {

  vpc_id = aws_vpc.devops_vpc.id

  tags = {

    Name = "devops-igw"

  }

}
```

---

# Step 3: Create Security Group and EC2 Instance

## Create Security Group

Add the security group configuration:

```hcl
resource "aws_security_group" "devops_sg" {

  vpc_id = aws_vpc.devops_vpc.id


  ingress {

    from_port = 22

    to_port = 22

    protocol = "tcp"

    cidr_blocks = ["0.0.0.0/0"]

  }


  egress {

    from_port = 0

    to_port = 0

    protocol = "-1"

    cidr_blocks = ["0.0.0.0/0"]

  }

}
```

---

## Create EC2 Instance

Add the EC2 resource:

```hcl
resource "aws_instance" "devops_ec2" {

  ami = "ami-0f58b397bc5c1f2e8"

  instance_type = var.instance_type

  key_name = var.key_name

  subnet_id = aws_subnet.devops_subnet.id

  vpc_security_group_ids = [
    aws_security_group.devops_sg.id
  ]


  tags = {

    Name = "terraform-ec2"

  }

}
```

---

# EC2 Configuration Explanation

| Setting | Description |
|---------|-------------|
| AMI | Ubuntu 22.04 image used for EC2. |
| Instance Type | Defines compute capacity. |
| Key Pair | Enables SSH access. |
| Subnet ID | Places the instance inside the Terraform-created subnet. |
| Security Group | Controls network access. |

---

# Step 4: Create Outputs File

Create an output configuration file.

```bash
nano outputs.tf
```

Add the following content:

```hcl
output "instance_public_ip" {

  value = aws_instance.devops_ec2.public_ip

}


output "vpc_id" {

  value = aws_vpc.devops_vpc.id

}
```

---

# Output Explanation

| Output | Description |
|--------|-------------|
| `instance_public_ip` | Displays the public IP address of the EC2 instance. |
| `vpc_id` | Displays the created VPC ID. |

---

# Step 5: Apply and Verify Infrastructure

Initialize Terraform.

```bash
terraform init
```

Review the execution plan.

```bash
terraform plan
```

Create the AWS resources.

```bash
terraform apply -auto-approve
```

Display Terraform outputs.

```bash
terraform output
```

Example output:

```text
instance_public_ip = "xx.xx.xx.xx"

vpc_id = "vpc-xxxxxxxx"
```

---

# Connect to the New EC2 Instance

Use the generated public IP address:

```bash
ssh -i devops-key.pem ubuntu@<INSTANCE_PUBLIC_IP>
```

Verify successful access.

---

# Step 6: Modify and Re-Apply Terraform Changes

Terraform tracks infrastructure changes automatically.

Modify a resource configuration in:

```text
main.tf
```

Example:

```text
Change instance configuration or resource properties.
```

Review changes:

```bash
terraform plan
```

Apply updates:

```bash
terraform apply
```

Terraform only updates the resources that have changed.

---

# Terraform Change Management

Terraform compares:

```text
Current Infrastructure State
          +
Terraform Configuration
          |
          v
Required Changes
```

Only necessary updates are performed.

---

# Step 7: Cleanup Resources

Remove all Terraform-managed resources.

```bash
terraform destroy -auto-approve
```

Terraform will delete:

- EC2 instance
- Security group
- Internet Gateway
- Subnet
- VPC

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Create Terraform variables.
- ✅ Create AWS VPC resources.
- ✅ Create subnets using Terraform.
- ✅ Configure an Internet Gateway.
- ✅ Create security groups.
- ✅ Launch EC2 instances using Terraform.
- ✅ Display resource information using outputs.
- ✅ Update infrastructure using Terraform changes.
- ✅ Destroy infrastructure safely.

---

# Summary

In this lab, you learned how to:

- Provision complete AWS infrastructure using Terraform.
- Manage networking resources as code.
- Use variables and outputs for reusable configurations.
- Apply infrastructure updates safely.
- Destroy resources when they are no longer required.

Terraform resource management skills learned in this lab are essential for building scalable cloud environments and will be used in upcoming EKS automation labs.

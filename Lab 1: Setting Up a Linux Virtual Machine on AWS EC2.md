# Lab 1: Setting Up a Linux Virtual Machine on AWS EC2

# Overview

In this lab, you will launch a Linux virtual machine on Amazon EC2 using the AWS Free Tier. You will configure networking, create a key pair for secure access, and connect to the instance using SSH.

---

# Learning Objectives

After completing this lab, you will be able to:

- Launch an Amazon EC2 instance (Amazon Linux 2 or Ubuntu) using the AWS Free Tier.
- Configure Security Groups to allow SSH access.
- Connect to an EC2 instance using SSH from your local machine.
- Understand the following EC2 concepts:
  - Amazon Machine Image (AMI)
  - Instance Type
  - Key Pair

---

# Prerequisites

Before you begin, ensure you have:

- An active AWS account.
- Access to the AWS Management Console.
- A stable internet connection.
- A terminal application:
  - **Linux/macOS:** Terminal
  - **Windows:** PowerShell, Windows Terminal, or PuTTY

---

# Lab Steps

## Step 1: Log in to the AWS Management Console

1. Open the AWS Management Console.

   ```
   https://console.aws.amazon.com
   ```

2. Sign in using your AWS account.

3. From the **Region** selector in the upper-right corner, select:

   ```
   ap-south-1 (Mumbai)
   ```

---

## Step 2: Navigate to Amazon EC2

1. In the AWS search bar, search for:

   ```
   EC2
   ```

2. Select **EC2** from the search results.

3. Click **Launch Instance**.

---

## Step 3: Name Your Instance

Under **Name and tags**, enter the following value:

```text
devops-lab-vm
```

---

## Step 4: Choose an Amazon Machine Image (AMI)

Select:

```text
Ubuntu Server 22.04 LTS (HVM), SSD Volume Type
```

Verify that the AMI displays:

```text
Free tier eligible
```

---

## Step 5: Choose an Instance Type

Select the following instance type:

```text
t2.micro
```

Ensure it is marked as:

```text
Free tier eligible
```

---

## Step 6: Create a Key Pair

1. Click **Create new key pair**.

2. Configure the following settings:

| Setting | Value |
|---------|-------|
| Key Pair Name | `devops-key` |
| Key Pair Type | RSA |
| Key File Format | `.pem` |

3. Click **Create key pair**.

4. Save the downloaded `.pem` file securely on your local computer.

> **Important**
>
> The private key (`.pem`) can only be downloaded once. Store it securely because it is required to connect to your EC2 instance.

---

## Step 7: Configure Network Settings (Security Group)

Under **Network Settings**, click **Edit**.

Configure the following inbound rules:

| Type | Port | Source |
|------|------|--------|
| SSH | 22 | My IP (Recommended) |
| HTTP | 80 | Anywhere |

> **Tip**
>
> Restricting SSH access to **My IP** is recommended for improved security.

---

## Step 8: Configure Storage

Keep the default storage configuration:

| Setting | Value |
|---------|-------|
| Volume Size | 8 GB |
| Volume Type | gp2 |

> **Note**
>
> The AWS Free Tier includes up to **30 GB** of eligible storage.

---

## Step 9: Launch the Instance

1. Review the instance configuration.
2. Click **Launch Instance**.
3. Select **View all instances**.
4. Wait until the **Instance State** changes to:

```text
Running
```

5. Copy the instance's **Public IPv4 Address**.

---

## Step 10: Connect to the Instance Using SSH

Open a terminal:

- **Linux/macOS:** Terminal
- **Windows:** PowerShell or Windows Terminal

### Set Permissions (Linux/macOS Only)

```bash
chmod 400 devops-key.pem
```

### Connect to the EC2 Instance

```bash
ssh -i devops-key.pem ubuntu@<YOUR_PUBLIC_IP>
```

Replace:

```text
<YOUR_PUBLIC_IP>
```

with your EC2 instance's public IPv4 address.

---

## Tip

If you're using **Windows**:

- Use **PuTTY** or **Windows Terminal** with OpenSSH.
- If using PuTTY, convert the `.pem` file to a `.ppk` file using **PuTTYgen** before connecting.

---

## Important

Always **stop** your EC2 instance when it is not in use to avoid unnecessary AWS charges.

Navigate to:

```text
EC2 → Select Instance → Instance State → Stop
```

---

# Summary

In this lab, you accomplished the following tasks:

- ✅ Logged in to the AWS Management Console.
- ✅ Launched an Ubuntu EC2 instance.
- ✅ Selected a Free Tier eligible AMI and instance type.
- ✅ Created and downloaded an SSH key pair.
- ✅ Configured Security Group rules.
- ✅ Connected to the EC2 instance using SSH.
- ✅ Learned basic Amazon EC2 concepts, including AMI, Instance Type, and Key Pair.

---


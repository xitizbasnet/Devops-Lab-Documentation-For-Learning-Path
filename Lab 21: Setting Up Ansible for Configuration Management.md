# Lab 21: Setting Up Ansible for Configuration Management

# Overview

In this lab, you will set up Ansible for configuration management and automation.

You will create an Ansible control node, configure managed nodes, establish SSH key-based authentication, create an inventory file, and execute Ansible commands remotely.

Ansible allows DevOps teams to automate server configuration, software installation, application deployment, and system management tasks across multiple servers.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install Ansible on a control node.
- Configure SSH-based communication between servers.
- Create and manage Ansible inventory files.
- Test connectivity between Ansible control and managed nodes.
- Execute Ansible ad-hoc commands.
- Automate basic server administration tasks.

---

# Prerequisites

Before starting this lab, ensure that you have:

- An AWS account.
- Basic EC2 knowledge.
- Ubuntu 22.04 instances available.
- SSH key pair (`devops-key.pem`).
- Basic Linux command-line knowledge.

---

# Ansible Architecture

Ansible follows a control node and managed node architecture.

```text
                 Ansible Control Node

                        |
                        |
                  SSH Connection

        --------------------------------
        |                              |
        v                              v

   Managed Node 1                Managed Node 2

      node1                         node2

```

---

# Components

| Component | Description |
|-----------|-------------|
| Control Node | Server where Ansible commands and playbooks are executed. |
| Managed Nodes | Servers managed by Ansible. |
| Inventory File | Defines managed servers and connection details. |
| SSH Key | Provides secure authentication between nodes. |
| Ad-hoc Commands | One-time Ansible automation commands. |

---

# Lab Steps

## Step 1: Launch 3 EC2 Instances

Create three Ubuntu EC2 instances.

### Instance Configuration

| Role | Instance Name | OS | Instance Type |
|------|---------------|----|---------------|
| Control Node | `ansible-control` | Ubuntu 22.04 | t2.micro |
| Managed Node 1 | `node1` | Ubuntu 22.04 | t2.micro |
| Managed Node 2 | `node2` | Ubuntu 22.04 | t2.micro |

---

## Configuration Requirements

For all instances:

- Use the same `devops-key` key pair.
- Use the same Security Group.
- Ensure SSH access is available.
- Note down all private IP addresses.

Example:

```text
Control Node:
<CONTROL_PRIVATE_IP>

Managed Node 1:
<NODE1_PRIVATE_IP>

Managed Node 2:
<NODE2_PRIVATE_IP>
```

---

# Step 2: Install Ansible on Control Node

Connect to the control node.

```bash
ssh -i devops-key.pem ubuntu@<CONTROL_IP>
```

Update package information.

```bash
sudo apt update
```

Install required packages.

```bash
sudo apt install -y software-properties-common
```

Add Ansible repository.

```bash
sudo add-apt-repository --yes --update ppa:ansible/ansible
```

Install Ansible.

```bash
sudo apt install ansible -y
```

Verify installation.

```bash
ansible --version
```

Example output:

```text
ansible [core 2.x.x]
```

---

# Step 3: Copy SSH Key to Control Node

Copy the private key from your local machine.

```bash
scp -i devops-key.pem devops-key.pem ubuntu@<CONTROL_IP>:~/.ssh/
```

Connect to the control node.

```bash
ssh -i devops-key.pem ubuntu@<CONTROL_IP>
```

Set correct key permissions.

```bash
chmod 400 ~/.ssh/devops-key.pem
```

---

# Step 4: Create Ansible Inventory

The inventory file contains details about managed servers.

Create a custom inventory file.

```bash
nano ~/inventory.ini
```

Add the following content:

```ini
[webservers]

node1 ansible_host=<NODE1_PRIVATE_IP> ansible_user=ubuntu \
ansible_ssh_private_key_file=~/.ssh/devops-key.pem


node2 ansible_host=<NODE2_PRIVATE_IP> ansible_user=ubuntu \
ansible_ssh_private_key_file=~/.ssh/devops-key.pem


[all:vars]

ansible_python_interpreter=/usr/bin/python3
```

---

# Inventory File Explanation

| Configuration | Purpose |
|--------------|---------|
| `[webservers]` | Defines a group of managed servers. |
| `ansible_host` | Private IP address of the node. |
| `ansible_user` | SSH username. |
| `ansible_ssh_private_key_file` | SSH authentication key. |
| `ansible_python_interpreter` | Python path used by Ansible. |

---

# Step 5: Test Connectivity

Verify communication between the control node and managed nodes.

```bash
ansible -i inventory.ini all -m ping
```

Expected output:

```text
node1 | SUCCESS => {
    "ping": "pong"
}

node2 | SUCCESS => {
    "ping": "pong"
}
```

Successful output confirms:

- SSH authentication works.
- Ansible can communicate with managed servers.

---

# Step 6: Run Ansible Ad-Hoc Commands

Ansible ad-hoc commands allow administrators to execute quick automation tasks without creating playbooks.

---

## Check Server Uptime

Run:

```bash
ansible -i inventory.ini all -m command -a 'uptime'
```

Example output:

```text
node1 | SUCCESS
node2 | SUCCESS
```

---

## Install Nginx on Managed Nodes

Install Nginx using the Ansible apt module.

```bash
ansible -i inventory.ini webservers -m apt \
-a 'name=nginx state=present' \
--become
```

Explanation:

| Option | Description |
|--------|-------------|
| `-m apt` | Uses the Ansible package management module. |
| `name=nginx` | Package to install. |
| `state=present` | Ensures the package exists. |
| `--become` | Executes command with sudo privileges. |

---

## Copy Files to Managed Nodes

Copy a file from the control node.

```bash
ansible -i inventory.ini all -m copy \
-a 'src=/etc/hosts dest=/tmp/hosts'
```

---

## Gather System Facts

Retrieve system information.

```bash
ansible -i inventory.ini node1 -m setup | grep ansible_distribution
```

Example output:

```text
ansible_distribution: Ubuntu
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Launch Ansible control and managed nodes.
- ✅ Install Ansible on the control node.
- ✅ Configure SSH key authentication.
- ✅ Create an Ansible inventory file.
- ✅ Test node connectivity.
- ✅ Execute Ansible ad-hoc commands.
- ✅ Install packages remotely.
- ✅ Gather system information.

---

# Summary

In this lab, you learned how to:

- Set up an Ansible control node.
- Configure managed servers.
- Connect servers securely using SSH.
- Create an inventory structure.
- Execute remote automation commands.

The Ansible foundation created in this lab will be used in the next lab to create reusable automation workflows using Ansible Playbooks.

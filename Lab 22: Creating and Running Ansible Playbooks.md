# Lab 22: Creating and Running Ansible Playbooks

# Overview

In this lab, you will learn how to create and execute Ansible Playbooks for automated configuration management.

You will write automation workflows using YAML, install and configure Nginx, deploy a web application, and understand advanced Ansible concepts including:

- Variables
- Handlers
- Roles
- Ansible Galaxy

Ansible Playbooks allow DevOps teams to define repeatable automation tasks and maintain consistent server configurations across multiple environments.

---

# Learning Objectives

After completing this lab, you will be able to:

- Write Ansible Playbooks using YAML syntax.
- Automate software installation using Ansible.
- Configure and manage Nginx servers.
- Deploy applications using Ansible automation.
- Use Ansible variables and handlers.
- Create reusable Ansible roles.
- Download and manage community roles using Ansible Galaxy.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 21: Setting Up Ansible for Configuration Management**.
- Ansible installed on the control node.
- Managed nodes configured in `inventory.ini`.
- SSH connectivity between Ansible control and managed nodes.
- Basic YAML knowledge.

---

# Ansible Playbook Architecture

```text
                 Ansible Control Node

                         |
                         |
                  Ansible Playbook
                    (YAML File)

                         |
        --------------------------------
        |                              |
        v                              v

   Managed Node 1                Managed Node 2

        |
        |
 Configuration Tasks
        |
        |
 Application Deployment

```

---

# Key Ansible Components

| Component | Description |
|-----------|-------------|
| Playbook | YAML file containing automation tasks. |
| Task | Individual automation action. |
| Module | Built-in Ansible functionality. |
| Variable | Stores reusable values. |
| Handler | Executes actions when notified by tasks. |
| Role | Reusable collection of Ansible content. |
| Galaxy | Repository for community Ansible roles. |

---

# Lab Steps

# Step 1: Basic Playbook — Install Nginx

Create an Ansible Playbook.

```bash
nano nginx-install.yml
```

Add the following content:

```yaml
---
- name: Install and Start Nginx

  hosts: webservers

  become: yes

  tasks:

    - name: Update apt cache

      apt:

        update_cache: yes


    - name: Install nginx

      apt:

        name: nginx

        state: present


    - name: Start and enable nginx

      service:

        name: nginx

        state: started

        enabled: yes
```

---

# Playbook Explanation

| Configuration | Description |
|--------------|-------------|
| `hosts` | Defines target managed servers. |
| `become` | Enables sudo privileges. |
| `tasks` | Contains automation steps. |
| `apt` | Installs and manages packages. |
| `service` | Controls system services. |

---

# Step 2: Run the Playbook

Execute the playbook.

```bash
ansible-playbook -i inventory.ini nginx-install.yml
```

Example output:

```text
PLAY RECAP

node1     : ok=3 changed=2
node2     : ok=3 changed=2
```

---

# Verify Nginx Installation

Test the web server.

```bash
curl http://<NODE1_IP>
```

Expected result:

```html
Welcome to nginx!
```

---

# Step 3: Playbook with Variables and Handlers

Create a deployment playbook.

```bash
nano deploy-app.yml
```

Add the following content:

```yaml
---
- name: Deploy Web Application

  hosts: webservers

  become: yes


  vars:

    app_port: 5000

    app_dir: /opt/devops-app


  tasks:


    - name: Create app directory

      file:

        path: "{{ app_dir }}"

        state: directory

        owner: ubuntu



    - name: Copy app files

      copy:

        src: ./app/

        dest: "{{ app_dir }}/"



    - name: Install Python and Flask

      apt:

        name: "{{ item }}"

        state: present

      loop:

        - python3

        - python3-pip



    - name: Install Flask

      pip:

        name: flask



    - name: Start Flask app

      shell:

        nohup python3 {{ app_dir }}/app.py &


  handlers:


    - name: restart nginx

      service:

        name: nginx

        state: restarted
```

---

# Variables Explanation

| Variable | Value | Purpose |
|----------|-------|---------|
| `app_port` | `5000` | Application listening port. |
| `app_dir` | `/opt/devops-app` | Application deployment directory. |

---

# Run Application Deployment Playbook

Execute:

```bash
ansible-playbook -i inventory.ini deploy-app.yml
```

The playbook will:

- Create application directories.
- Copy application files.
- Install required packages.
- Install Flask.
- Start the application.

---

# Step 4: Create Ansible Roles Structure

Roles help organize Ansible automation into reusable components.

Create a role:

```bash
ansible-galaxy init roles/webserver
```

View the role structure:

```bash
tree roles/webserver/
```

Expected structure:

```text
roles/webserver/

├── tasks/
│   └── main.yml

├── handlers/
│   └── main.yml

├── vars/
│   └── main.yml

├── templates/

├── files/

├── defaults/
│   └── main.yml
```

---

# Ansible Role Directory Explanation

| Directory | Purpose |
|-----------|---------|
| `tasks/` | Contains automation tasks. |
| `handlers/` | Contains service restart actions. |
| `vars/` | Stores role variables. |
| `templates/` | Stores Jinja2 templates. |
| `files/` | Stores static files. |
| `defaults/` | Stores default variables. |

---

# Step 5: Use Role in Playbook

Create a site playbook.

```bash
nano site.yml
```

Add the following content:

```yaml
---
- hosts: webservers

  become: yes

  roles:

    - webserver
```

---

# Run Role-Based Playbook

Execute:

```bash
ansible-playbook -i inventory.ini site.yml
```

Ansible will automatically load:

- Role tasks.
- Variables.
- Handlers.
- Templates.
- Files.

---

# Step 6: Ansible Galaxy — Download Community Roles

Install a community Docker role.

```bash
ansible-galaxy install geerlingguy.docker
```

List installed roles.

```bash
ansible-galaxy list
```

Example output:

```text
geerlingguy.docker
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Create Ansible YAML playbooks.
- ✅ Install Nginx using a playbook.
- ✅ Execute playbooks against managed nodes.
- ✅ Use variables inside playbooks.
- ✅ Configure handlers.
- ✅ Create reusable Ansible roles.
- ✅ Use Ansible Galaxy community roles.

---

# Summary

In this lab, you learned how to:

- Create Ansible automation workflows using Playbooks.
- Automate server configuration tasks.
- Deploy applications using Ansible.
- Use variables and handlers for flexible automation.
- Organize automation using roles.
- Extend Ansible functionality using Galaxy roles.

The concepts covered in this lab are the foundation for enterprise-level configuration management and automated infrastructure deployment.

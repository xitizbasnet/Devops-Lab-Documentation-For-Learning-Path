# Lab 2: Basic Linux Commands and File System Navigation

# Overview

In this lab, you will learn the fundamental Linux command-line operations required for system administration and DevOps. You will practice navigating the Linux file system, managing files and directories, searching for content, and viewing system information.

---

# Learning Objectives

After completing this lab, you will be able to:

- Navigate the Linux file system using command-line interface (CLI) commands.
- Create, read, update, and delete files and directories.
- Use pipes, redirects, and search commands.
- Understand basic Linux file permissions.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 1: Setting Up a Linux Virtual Machine on AWS EC2**.
- Connected to your EC2 instance using SSH.

---

# Lab Steps

## Step 1: Navigation Commands

Use the following commands to navigate through the Linux file system.

| Command | Description |
|---------|-------------|
| `pwd` | Show the current working directory. |
| `ls -la` | List all files and directories, including hidden files, with detailed information. |
| `cd /var/log` | Change to the `/var/log` directory. |
| `cd ~` | Return to your home directory. |
| `cd ..` | Move up one directory level. |

> **Tip**
>
> Use the `Tab` key to auto-complete file and directory names while working in the terminal.

---

## Step 2: File and Directory Operations

Practice creating, modifying, copying, moving, and deleting files and directories.

| Command | Description |
|---------|-------------|
| `mkdir myproject && cd myproject` | Create a directory named `myproject` and navigate into it. |
| `touch index.html` | Create an empty file named `index.html`. |
| `echo "Hello DevOps" > hello.txt` | Create a file and write text to it. |
| `cat hello.txt` | Display the contents of the file. |
| `echo "Second line" >> hello.txt` | Append additional text to the existing file. |
| `cp hello.txt hello_backup.txt` | Create a copy of the file. |
| `mv hello.txt greet.txt` | Rename or move the file. |
| `rm hello_backup.txt` | Delete a file. |
| `rm -rf old_folder` | Delete a directory and all of its contents recursively. |

> **Warning**
>
> The `rm -rf` command permanently deletes files and directories without confirmation. Use it carefully.

---

## Step 3: Search and Filter

Use the following commands to search files and filter command output.

| Command | Description |
|---------|-------------|
| `grep 'DevOps' greet.txt` | Search for the text `DevOps` within a file. |
| `find /home -name '*.txt'` | Find all `.txt` files under the `/home` directory. |
| `ls -la \| grep '.txt'` | Filter the output of `ls` to display only text files. |
| `wc -l greet.txt` | Count the number of lines in a file. |
| `cat /var/log/syslog \| less` | View large files one page at a time. |

> **Note**
>
> The pipe operator (`|`) sends the output of one command as input to another command, making it easier to filter and process data.

---

## Step 4: System Information

Retrieve information about the Linux system.

| Command | Description |
|---------|-------------|
| `df -h` | Display disk usage in a human-readable format. |
| `free -m` | Display memory usage in megabytes. |
| `top` *(Press `q` to quit.)* | Display running processes and system resource utilization. |
| `cat /etc/os-release` | Display operating system information. |
| `ip addr show` | Display network interface configuration and IP address information. |

> **Tip**
>
> Use `Ctrl + C` to stop most running terminal commands when necessary.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Display your current working directory.
- ✅ Navigate between directories.
- ✅ Create and remove files and directories.
- ✅ Rename and copy files.
- ✅ Search for files using `find`.
- ✅ Search text within files using `grep`.
- ✅ View system resource information.
- ✅ Display your operating system version and network configuration.

---

# Summary

In this lab, you learned how to:

- Navigate the Linux file system.
- Create, modify, copy, move, and delete files and directories.
- Search files and filter command output using pipes.
- View important system information, including disk usage, memory usage, operating system details, and network configuration.
- Practice essential Linux commands used in daily DevOps and system administration tasks.

---


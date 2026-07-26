# Lab 4: Setting Up a Git Repository

# Overview

In this lab, you will install Git on your EC2 instance, configure your Git identity, create a local Git repository, make your first commit, push the repository to GitHub, clone a repository, and review commit history.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install Git on an EC2 instance and configure your Git identity.
- Initialize a local Git repository.
- Stage and commit changes.
- Push a local repository to GitHub.
- Clone an existing GitHub repository.
- View Git commit history.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed the previous Linux labs.
- Connected to your EC2 instance using SSH.
- A GitHub account.
- Permission to create repositories in your GitHub account.

---

# Lab Steps

## Step 1: Install Git on EC2

Update the package index.

```bash
sudo apt update
```

Install Git.

```bash
sudo apt install git -y
```

Verify the installed version.

```bash
git --version
```

---

## Step 2: Configure Git Identity

Configure your Git username.

```bash
git config --global user.name "Vinod Muleva"
```

Configure your Git email address.

```bash
git config --global user.email "your@email.com"
```

Verify the configuration.

```bash
git config --list
```

> **Note**
>
> Replace `your@email.com` with the email address associated with your GitHub account.

---

## Step 3: Initialize a Repository

Create a project directory and navigate into it.

```bash
mkdir my-devops-app && cd my-devops-app
```

Initialize a new Git repository.

```bash
git init
```

Verify that the repository has been initialized.

```bash
ls -la
```

You should see a hidden `.git` directory.

---

## Step 4: Create Files and Make the First Commit

Create a README file.

```bash
echo "# My DevOps App" > README.md
```

Create a Python application.

```bash
echo "print('Hello DevOps')" > app.py
```

Check the repository status.

```bash
git status
```

Stage all files.

```bash
git add .
```

Verify the staged files.

```bash
git status
```

Create the first commit.

```bash
git commit -m "Initial commit: Add README and app.py"
```

---

## Step 5: Create a GitHub Repository and Push the Code

### Create a GitHub Repository

1. Open GitHub.
2. Select **New Repository**.
3. Configure the repository with the following settings:

| Setting | Value |
|----------|-------|
| Repository Name | `my-devops-app` |
| Visibility | Public |

4. Create the repository.

### Connect the Local Repository

Add the GitHub repository as the remote origin.

```bash
git remote add origin https://github.com/<username>/my-devops-app.git
```

Rename the default branch.

```bash
git branch -M main
```

Push the repository to GitHub.

```bash
git push -u origin main
```

> **Important**
>
> Replace `<username>` with your GitHub username.

---

## Step 6: Clone a Repository

Navigate to your home directory.

```bash
cd ~
```

Clone the GitHub repository.

```bash
git clone https://github.com/<username>/my-devops-app.git cloned-app
```

Navigate into the cloned repository.

```bash
cd cloned-app
```

Verify the cloned contents.

```bash
ls -la
```

---

## Step 7: View Commit History

Display a concise commit history.

```bash
git log --oneline
```

Display the commit history as a graph.

```bash
git log --oneline --graph --all
```

---

## Important

GitHub no longer supports password authentication for Git operations over HTTPS.

Use a **Personal Access Token (PAT)** instead of your GitHub password.

Generate a PAT by navigating to:

```text
GitHub → Settings → Developer Settings → Personal Access Tokens → Generate new token
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install Git.
- ✅ Configure your Git username and email.
- ✅ Initialize a Git repository.
- ✅ Stage and commit files.
- ✅ Create a repository on GitHub.
- ✅ Push a local repository to GitHub.
- ✅ Clone a GitHub repository.
- ✅ View the Git commit history.

---

# Summary

In this lab, you learned how to:

- Install and configure Git.
- Create and initialize a local Git repository.
- Track files and create commits.
- Connect a local repository to GitHub.
- Push code to a remote repository.
- Clone repositories from GitHub.
- Review repository commit history.

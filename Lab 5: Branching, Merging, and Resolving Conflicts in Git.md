# Lab 5: Branching, Merging, and Resolving Conflicts in Git

# Overview

In this lab, you will learn how to work with Git branches, merge changes, resolve merge conflicts, temporarily save uncommitted work using Git Stash, and safely undo commits using Git Revert. These are essential Git workflows used in collaborative software development.

---

# Learning Objectives

After completing this lab, you will be able to:

- Create and switch between Git branches.
- Merge branches using Git.
- Simulate and resolve merge conflicts.
- Temporarily save uncommitted changes using Git Stash.
- Revert commits without rewriting Git history.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 4: Setting Up a Git Repository**.
- A local Git repository named `my-devops-app`.
- Git installed and configured.
- A GitHub repository connected to your local project (optional but recommended).

---

# Lab Steps

## Step 1: Create and Switch Branches

Navigate to your project directory.

```bash
cd my-devops-app
```

Create a new feature branch.

```bash
git branch feature/login
```

Switch to the new branch.

```bash
git checkout feature/login
```

Alternatively, create and switch to a new branch in a single command.

```bash
git checkout -b feature/payment
```

> **Tip**
>
> Using feature branches allows developers to work independently without affecting the main codebase.

---

## Step 2: Make Changes on the Branch

Append a login function to the application.

```bash
echo "def login(): pass" >> app.py
```

Stage the changes.

```bash
git add .
```

Commit the changes.

```bash
git commit -m "Add login function"
```

---

## Step 3: Merge the Branch into Main

Switch back to the main branch.

```bash
git checkout main
```

Merge the feature branch.

```bash
git merge feature/login
```

View the commit history.

```bash
git log --oneline
```

> **Note**
>
> If there are no conflicting changes, Git performs the merge automatically.

---

## Step 4: Simulate a Merge Conflict

### Create a Change on the Main Branch

Create a configuration file.

```bash
echo "version = 1.0" > config.txt
```

Stage and commit the change.

```bash
git add .
git commit -m "Add config on main"
```

### Create a Conflicting Change on a Feature Branch

Create and switch to a new branch.

```bash
git checkout -b feature/config
```

Modify the same file.

```bash
echo "version = 2.0" > config.txt
```

Stage and commit the change.

```bash
git add .
git commit -m "Add config on feature"
```

### Attempt the Merge

Switch back to the main branch.

```bash
git checkout main
```

Merge the feature branch.

```bash
git merge feature/config
```

Git detects conflicting changes and reports a **merge conflict**.

---

## Step 5: Resolve the Merge Conflict

Open the conflicting file.

```bash
cat config.txt
```

You will see conflict markers similar to the following:

```text
<<<<<<< HEAD
version = 1.0
=======
version = 2.0
>>>>>>> feature/config
```

Edit the file and keep the desired version.

```bash
echo "version = 2.0" > config.txt
```

Stage the resolved file.

```bash
git add config.txt
```

Complete the merge by creating a commit.

```bash
git commit -m "Resolve merge conflict: use version 2.0"
```

> **Important**
>
> Always review the conflicting code before resolving a merge conflict to ensure that no required changes are lost.

---

## Step 6: Save Work Temporarily Using Git Stash

Make an uncommitted change.

```bash
echo "temp work" >> app.py
```

Save the uncommitted changes.

```bash
git stash
```

View the list of stashed changes.

```bash
git stash list
```

Restore the most recently stashed changes.

```bash
git stash pop
```

> **Tip**
>
> Git Stash is useful when you need to temporarily switch tasks without committing incomplete work.

---

## Step 7: Revert a Commit

View the commit history.

```bash
git log --oneline
```

Identify the commit hash you want to revert.

Revert the commit.

```bash
git revert <commit-hash>
```

Verify that Git created a new commit to undo the selected changes.

```bash
git log --oneline
```

> **Note**
>
> Unlike `git reset`, `git revert` preserves the project history by creating a new commit that reverses the selected changes.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Create and switch between branches.
- ✅ Commit changes on a feature branch.
- ✅ Merge a feature branch into the main branch.
- ✅ Simulate and resolve a merge conflict.
- ✅ Save and restore uncommitted work using Git Stash.
- ✅ Revert a commit using Git Revert.
- ✅ Review the Git commit history.

---

# Summary

In this lab, you learned how to:

- Create and manage Git branches.
- Merge feature branches into the main branch.
- Identify and resolve merge conflicts.
- Temporarily save uncommitted work using Git Stash.
- Safely undo changes using Git Revert while preserving repository history.

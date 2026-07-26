# Lab 3: User and Group Management in Linux

# Overview

In this lab, you will learn how to create and manage Linux users and groups, configure file ownership and permissions, assign administrative privileges, and switch between user accounts. These are essential system administration tasks commonly used in Linux environments.

---

# Learning Objectives

After completing this lab, you will be able to:

- Create and manage Linux users and groups.
- Set file ownership and permissions using `chmod` and `chown`.
- Configure `sudo` privileges for users.
- Switch between different user accounts.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed **Lab 1: Setting Up a Linux Virtual Machine on AWS EC2**.
- Completed **Lab 2: Basic Linux Commands and File System Navigation**.
- Connected to your EC2 instance using SSH.
- A user account with `sudo` privileges.

---

# Lab Steps

## Step 1: Create a New User

Create a new user account named **devopsuser**.

```bash
sudo adduser devopsuser
```

Follow the prompts to set the user's password and optional account information.

Add the user to the **sudo** group to grant administrative privileges.

```bash
sudo adduser devopsuser sudo
```

> **Note**
>
> Members of the `sudo` group can execute administrative commands using the `sudo` command.

---

## Step 2: Verify the User

Verify that the user has been successfully created.

Display the user's information:

```bash
id devopsuser
```

Verify the user entry in the system password file:

```bash
cat /etc/passwd | grep devopsuser
```

---

## Step 3: Create a Group

Create a new group named **devteam**.

```bash
sudo groupadd devteam
```

Add **devopsuser** to the newly created group.

```bash
sudo usermod -aG devteam devopsuser
```

Verify the user's group memberships.

```bash
groups devopsuser
```

---

## Step 4: Switch Between Users

Switch to the newly created user account.

```bash
su - devopsuser
```

Enter the password when prompted.

Verify the current logged-in user.

```bash
whoami
```

Return to the original user.

```bash
exit
```

---

## Step 5: Manage File Permissions (`chmod`)

Create a sample shell script.

```bash
touch testfile.sh
```

View the current file permissions.

```bash
ls -l testfile.sh
```

Set the permissions to **755**.

```bash
chmod 755 testfile.sh
```

Grant execute permission to all users.

```bash
chmod +x testfile.sh
```

Set read and write permissions for the file owner only.

```bash
chmod 600 secret.txt
```

### Common Permission Values

| Permission | Description |
|------------|-------------|
| `755` | Owner: Read, Write, Execute • Group: Read, Execute • Others: Read, Execute |
| `600` | Owner: Read, Write • Group: No Access • Others: No Access |

> **Tip**
>
> Numeric permission values are calculated as:
>
> - **4** = Read (`r`)
> - **2** = Write (`w`)
> - **1** = Execute (`x`)
>
> For example:
>
> ```text
> chmod 755
> ```
>
> Results in:
>
> ```text
> Owner  : rwx
> Group  : r-x
> Others : r-x
> ```

---

## Step 6: Change File Ownership (`chown`)

Assign ownership of the file to **devopsuser** and the **devteam** group.

```bash
sudo chown devopsuser:devteam testfile.sh
```

Verify the ownership changes.

```bash
ls -l testfile.sh
```

---

## Step 7: Configure Sudo Privileges

Open the sudoers configuration file using the recommended editor.

```bash
sudo visudo
```

Add the following line at the end of the file.

```text
devopsuser ALL=(ALL:ALL) NOPASSWD:ALL
```

> **Important**
>
> Always edit the sudoers file using `visudo`. It performs syntax validation before saving, helping prevent configuration errors that could lock users out of administrative access.

---

## Step 8: Delete a User

Delete the user account and remove its home directory.

```bash
sudo userdel -r devopsuser
```

> **Note**
>
> The `-r` option removes both the user account and the associated home directory.

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Create a new Linux user.
- ✅ Create and manage Linux groups.
- ✅ Add users to groups.
- ✅ Switch between user accounts.
- ✅ Modify file permissions using `chmod`.
- ✅ Change file ownership using `chown`.
- ✅ Configure passwordless `sudo` access.
- ✅ Delete a user account and its home directory.

---

# Summary

In this lab, you learned how to:

- Create and manage Linux users.
- Create and manage Linux groups.
- Assign users to groups.
- Switch between user accounts.
- Configure file permissions using `chmod`.
- Change file ownership using `chown`.
- Grant administrative privileges using `sudo`.
- Remove user accounts when they are no longer required.

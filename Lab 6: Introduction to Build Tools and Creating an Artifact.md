# Lab 6: Introduction to Build Tools and Creating an Artifact

# Overview

In this lab, you will learn the role of build tools in the software development lifecycle and CI/CD pipelines. You will install and configure Maven and Node.js build tools, create application builds, generate artifacts, and understand how build outputs are used in DevOps workflows.

---

# Learning Objectives

After completing this lab, you will be able to:

- Install Maven and Node.js build tools on an EC2 instance.
- Build a Java application and create a JAR artifact.
- Build a Node.js application and create a production build.
- Understand the purpose of build tools in CI/CD pipelines.

---

# Prerequisites

Before starting this lab, ensure that you have:

- Completed previous Linux and Git labs.
- Access to an EC2 Linux instance.
- SSH access configured to the EC2 instance.
- Basic understanding of Git commands.

---

# Overview of Build Tools

Build tools automate the process of compiling source code, managing dependencies, running tests, and packaging applications into deployable artifacts.

Common examples:

| Build Tool | Technology | Artifact Type |
|------------|------------|---------------|
| Maven | Java | JAR/WAR |
| npm | Node.js | Production Build |
| Gradle | Java/Kotlin | JAR/WAR |

In CI/CD pipelines, build tools are used to create consistent and repeatable application packages that can be stored, tested, and deployed.

---

# Lab Steps

## Step 1: Install Java and Maven

Update the package repository.

```bash
sudo apt update
```

Install Java Development Kit (JDK).

```bash
sudo apt install openjdk-17-jdk -y
```

Verify the Java installation.

```bash
java -version
```

Install Maven.

```bash
sudo apt install maven -y
```

Verify the Maven installation.

```bash
mvn -version
```

---

## Step 2: Create a Maven Java Project

Generate a new Maven project using the Maven archetype template.

```bash
mvn archetype:generate \
-DgroupId=com.devops \
-DartifactId=my-app \
-DarchetypeArtifactId=maven-archetype-quickstart \
-DinteractiveMode=false
```

Navigate into the project directory.

```bash
cd my-app
```

List the project files.

```bash
ls -la
```

A standard Maven project structure will be created:

```text
my-app/
├── pom.xml
└── src/
    ├── main/
    └── test/
```

---

## Step 3: Build the Project and Create a JAR Artifact

Build the Maven project.

```bash
mvn package
```

The build process creates a JAR artifact inside the `target` directory.

View the generated artifact.

```bash
ls target/
```

Expected output:

```text
my-app-1.0-SNAPSHOT.jar
```

Run the Java application using the generated JAR file.

```bash
java -cp target/my-app-1.0-SNAPSHOT.jar com.devops.App
```

---

## Step 4: Install Node.js

Download and configure the NodeSource repository.

```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```

Install Node.js.

```bash
sudo apt install nodejs -y
```

Verify the installation.

```bash
node -v && npm -v
```

---

## Step 5: Create a Node.js Project

Create a new Node.js application directory.

```bash
mkdir node-app && cd node-app
```

Initialize a Node.js project.

```bash
npm init -y
```

View the generated package configuration.

```bash
cat package.json
```

The `package.json` file contains project metadata, dependencies, and build scripts.

---

## Step 6: Install Dependencies and Build the Application

Create the application file.

```bash
echo "console.log('Node App Built!');" > index.js
```

Install project dependencies.

```bash
npm install
```

Run the Node.js application.

```bash
node index.js
```

Expected output:

```text
Node App Built!
```

---

## Step 7: Push Artifact Project to Git

Navigate back to the Maven project.

```bash
cd my-app
```

Initialize a Git repository.

```bash
git init
```

Stage all project files.

```bash
git add .
```

Create a commit.

```bash
git commit -m "Add Maven Java project"
```

Push the project to a GitHub repository.

```text
# Push to GitHub repository
```

---

# Validation

Verify that you can successfully perform the following tasks:

- ✅ Install Java and Maven.
- ✅ Create a Maven Java project.
- ✅ Generate a JAR artifact using Maven.
- ✅ Run the Java application using the generated artifact.
- ✅ Install Node.js and npm.
- ✅ Create and execute a Node.js application.
- ✅ Store project files in Git.

---

# Summary

In this lab, you learned how to:

- Install and configure common DevOps build tools.
- Create a Java project using Maven.
- Generate a deployable JAR artifact.
- Create and run a Node.js application.
- Understand the role of build tools in CI/CD workflows.
- Store application source code and build files using Git.

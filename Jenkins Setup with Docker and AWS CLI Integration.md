# **Jenkins Installation with Docker and AWS CLI Setup**

## Table of Contents
1. [Pre-requisites](#pre-requisites)
2. [Install Docker](#install-docker)
3. [Install Jenkins](#install-jenkins)
4. [Install AWS CLI](#install-aws-cli)
5. [Configure Jenkins for Docker Access](#configure-jenkins-for-docker-access)
6. [Verification Steps](#verification-steps)
7. [Troubleshooting Commands](#troubleshooting-commands)
8. [Summary](#summary)



## docker set up and permission
sudo apt install docker.io -y
sudo usermod -aG docker jenkins
sudo chmod 666 /var/run/docker.sock


---

## 1. Pre-requisites
Ensure the following before starting:

- Ubuntu Server 20.04+ or equivalent.
- A user with sudo privileges.
- Basic knowledge of Linux terminal commands.
- Stable internet connection.

---

## 2. Install Docker
Docker is required for building and running containers.

### a) Update System and Install Dependencies
```bash
sudo apt update -y
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
```

### b) Add Docker GPG Key and Repository
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### c) Install Docker Engine
```bash
sudo apt update -y
sudo apt install -y docker-ce docker-ce-cli containerd.io
```

### d) Start and Enable Docker
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### e) Verify Docker Installation
```bash
docker --version
```


##sudo apt update
sudo apt upgrade -y
3. Install Docker Using the Official Docker Repository
Instead of using docker.io, install Docker directly from the official Docker repository, which resolves most conflicts.

Add Docker’s official GPG key and repository:

bash
Copy code
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
Update the package list again:

bash
Copy code
sudo apt update
Install Docker and related tools:

bash
Copy code
sudo apt install docker-ce docker-ce-cli containerd.io -y

## 3. Install Jenkins
Jenkins requires Java to run.

### a) Install Java and Add Jenkins Repository
```bash
sudo apt update -y
sudo apt install -y openjdk-11-jdk

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo gpg --dearmor -o /usr/share/keyrings/jenkins.gpg
echo "deb [signed-by=/usr/share/keyrings/jenkins.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
```

### b) Install Jenkins
```bash
sudo apt update -y
sudo apt install -y jenkins
```

### c) Start and Enable Jenkins Service
```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### d) Verify Jenkins Installation
```bash
sudo systemctl status jenkins
```

---

## 4. Install AWS CLI

### a) Download AWS CLI v2 Installer
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```

### b) Install unzip Utility and Extract AWS CLI
```bash
sudo apt install -y unzip
unzip awscliv2.zip
```

### c) Run the AWS CLI Installer
```bash
sudo ./aws/install
```

### d) Verify AWS CLI Installation
```bash
aws --version
```

---

## 5. Configure Jenkins for Docker Access
Jenkins needs permission to access the Docker socket.

### a) Add Jenkins User to Docker Group
```bash
sudo usermod -aG docker jenkins
```

### b) Restart Jenkins and Docker
```bash
sudo systemctl restart docker
sudo systemctl restart jenkins
```

### c) Test Jenkins Access to Docker
Switch to the Jenkins user and run Docker commands:
```bash
sudo su - jenkins
docker ps
```

---

## 6. Verification Steps

### Check Docker Installation:
```bash
docker --version
```

### Check AWS CLI Installation:
```bash
aws --version
```

### Test AWS CLI Access:
```bash
aws sts get-caller-identity
```

### Check Jenkins Service:
```bash
sudo systemctl status jenkins
```

### Check Jenkins User in Docker Group:
```bash
groups jenkins
```

---

## 7. Troubleshooting Commands

### a) Docker Troubleshooting
**Check Docker Service Status:**
```bash
sudo systemctl status docker
```

**Restart Docker:**
```bash
sudo systemctl restart docker
```

**Check Docker Daemon Logs:**
```bash
sudo journalctl -u docker
```

**Fix Docker Socket Permissions (Temporary Fix):**
```bash
sudo chmod 666 /var/run/docker.sock
```

### b) AWS CLI Troubleshooting
**Check AWS Configuration:**
```bash
cat ~/.aws/credentials
cat ~/.aws/config
```

**Test AWS Access:**
```bash
aws sts get-caller-identity
```

**Reconfigure AWS CLI:**
```bash
aws configure
```

### c) Jenkins Troubleshooting
**Check Jenkins Service Status:**
```bash
sudo systemctl status jenkins
```

**Restart Jenkins Service:**
```bash
sudo systemctl restart jenkins
```

**Check Jenkins Logs:**
```bash
sudo tail -n 50 /var/log/jenkins/jenkins.log
```

**Print Jenkins Environment Variables in Pipeline:**
```groovy
stage('Debug Env') {
    steps {
        sh 'env'
    }
}
```

---

## Summary
By following the above steps, you will:

1. Install Docker and AWS CLI on your Jenkins server.
2. Configure Jenkins to access Docker and AWS services.
3. Use the troubleshooting commands to resolve any potential issues.

This guide is suitable for setting up Jenkins with Docker and AWS CLI support on a newly configured system.


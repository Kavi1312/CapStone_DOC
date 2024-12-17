# Jenkins Setup with Docker and AWS CLI Integration

## Table of Contents
1. [Pre-requisites](#pre-requisites)
2. [Install Docker](#install-docker)
3. [Install Jenkins](#install-jenkins)
4. [Install AWS CLI](#install-aws-cli)
5. [Configure Jenkins for Docker Access](#configure-jenkins-for-docker-access)
6. [Verification Steps](#verification-steps)
7. [Troubleshooting Commands](#troubleshooting-commands)

---

## 1. Pre-requisites

Ensure you have the following:
- Ubuntu Server **20.04+** or equivalent
- A user with **sudo** privileges
- Stable **internet connection**
- Basic knowledge of Linux terminal commands

---

## 2. Install Docker

Docker is required for building and running containers.

### a) Update System and Install Dependencies
```bash
sudo apt update -y
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common



2. Install Docker
Docker is required for building and running containers.

a) Update System and Install Dependencies
bash
Copy code
sudo apt update -y
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common
b) Add Docker GPG Key and Repository
bash
Copy code
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
c) Install Docker Engine
bash
Copy code
sudo apt update -y
sudo apt install -y docker-ce docker-ce-cli containerd.io
d) Start and Enable Docker
bash
Copy code
sudo systemctl start docker
sudo systemctl enable docker
e) Verify Docker Installation
bash
Copy code
docker --version
3. Install Jenkins
Jenkins requires Java to run.

a) Install Java and Add Jenkins Repository
bash
Copy code
sudo apt update -y
sudo apt install -y openjdk-11-jdk

curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo gpg --dearmor -o /usr/share/keyrings/jenkins.gpg
echo "deb [signed-by=/usr/share/keyrings/jenkins.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
b) Install Jenkins
bash
Copy code
sudo apt update -y
sudo apt install -y jenkins
c) Start and Enable Jenkins Service
bash
Copy code
sudo systemctl start jenkins
sudo systemctl enable jenkins
d) Verify Jenkins Installation
bash
Copy code
sudo systemctl status jenkins
4. Install AWS CLI
AWS CLI allows Jenkins to interact with AWS services.

a) Download AWS CLI v2 Installer
bash
Copy code
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
b) Install Unzip Utility and Extract AWS CLI
bash
Copy code
sudo apt install -y unzip
unzip awscliv2.zip
c) Run the AWS CLI Installer
bash
Copy code
sudo ./aws/install
d) Verify AWS CLI Installation
bash
Copy code
aws --version
You should see output like:

bash
Copy code
aws-cli/2.x.x Python/3.x.x Linux/x86_64
5. Configure Jenkins for Docker Access
To allow Jenkins to use Docker, add Jenkins to the docker group.

a) Add Jenkins User to Docker Group
bash
Copy code
sudo usermod -aG docker jenkins
b) Restart Docker and Jenkins Services
bash
Copy code
sudo systemctl restart docker
sudo systemctl restart jenkins
c) Test Jenkins Access to Docker
Switch to the jenkins user and test Docker:

bash
Copy code
sudo su - jenkins
docker ps
6. Verification Steps
Run these commands to ensure all components are installed correctly:

a) Check Docker Installation
bash
Copy code
docker --version
b) Check AWS CLI Installation
bash
Copy code
aws --version
c) Verify AWS CLI Access
Test AWS credentials:

bash
Copy code
aws sts get-caller-identity
d) Check Jenkins Service Status
bash
Copy code
sudo systemctl status jenkins
e) Check Jenkins User Docker Access
bash
Copy code
groups jenkins
The output should include docker.

7. Troubleshooting Commands
Use the following commands to troubleshoot common issues.

a) Docker Troubleshooting
Check Docker Service Status:
bash
Copy code
sudo systemctl status docker
Restart Docker:
bash
Copy code
sudo systemctl restart docker
Check Docker Logs:
bash
Copy code
sudo journalctl -u docker
Fix Docker Socket Permissions (Temporary):
bash
Copy code
sudo chmod 666 /var/run/docker.sock
b) AWS CLI Troubleshooting
Verify AWS Configuration:
bash
Copy code
cat ~/.aws/credentials
cat ~/.aws/config
Test AWS CLI Access:
bash
Copy code
aws sts get-caller-identity
Reconfigure AWS CLI:
bash
Copy code
aws configure
c) Jenkins Troubleshooting
Check Jenkins Service Status:
bash
Copy code
sudo systemctl status jenkins
Restart Jenkins Service:
bash
Copy code
sudo systemctl restart jenkins
Check Jenkins Logs:
bash
Copy code
sudo tail -n 50 /var/log/jenkins/jenkins.log
Print Jenkins Environment Variables (in Pipeline):
groovy
Copy code
stage('Debug Env') {
    steps {
        sh 'env'
    }
}
Summary
By following this guide, you will:

Install Docker, Jenkins, and AWS CLI.
Configure Jenkins to use Docker and AWS CLI.
Troubleshoot any installation or permission issues effectively.
This setup enables Jenkins to build Docker images and interact with AWS services seamlessly. 🚀

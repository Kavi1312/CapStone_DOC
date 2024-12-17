# **Jenkins Setup and AWS ECR Image Push - Full Command Guide**

## **Table of Contents**
1. [Prerequisites](#prerequisites)
2. [Update System and Install Dependencies](#update-system-and-install-dependencies)
3. [Install Java and Jenkins](#install-java-and-jenkins)
4. [Install AWS CLI](#install-aws-cli)
5. [Install Docker](#install-docker)
6. [Configure Jenkins for Docker and AWS](#configure-jenkins-for-docker-and-aws)
7. [Pipeline to Push Docker Image to ECR](#pipeline-to-push-docker-image-to-ecr)
8. [Verification Steps](#verification-steps)
9. [Troubleshooting Commands](#troubleshooting-commands)

---

## **1. Prerequisites**
Ensure the following before starting:

- Ubuntu Server 20.04+ on an EC2 instance.
- Open ports **8080** (Jenkins) and **22** (SSH) in the security group.
- AWS Account with IAM access for ECR.
- IAM Role: `AmazonEC2ContainerRegistryFullAccess` attached to EC2.
- Jenkins and Docker installed.

---

## **2. Update System and Install Dependencies**
Update the system and install required utilities.

```bash
sudo apt-get update -y
sudo apt-get install -y ca-certificates curl gnupg fontconfig openjdk-17-jre
```

---

## **3. Install Java and Jenkins**
Install Java and set up Jenkins repository.

```bash
# Add Jenkins GPG Key and Repository
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg

echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Install Jenkins
sudo apt-get update -y
sudo apt-get install -y jenkins

# Start and Enable Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Retrieve Initial Jenkins Admin Password
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

Access Jenkins at `http://<EC2-Public-IP>:8080`.

---

## **4. Install AWS CLI**
Install AWS CLI v2 to interact with AWS services.

```bash
# Download and Install AWS CLI
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt-get install -y unzip
unzip awscliv2.zip
sudo ./aws/install

# Verify AWS CLI Installation
aws --version
```

---

## **5. Install Docker**
Install Docker for building and pushing images.

```bash
# Install Docker Engine
sudo apt-get update -y
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt-get install -y docker-ce docker-ce-cli containerd.io

# Start Docker Service
sudo systemctl start docker
sudo systemctl enable docker

# Add Jenkins User to Docker Group
sudo usermod -aG docker jenkins
sudo systemctl restart docker
sudo systemctl restart jenkins

# Verify Docker Installation
docker --version
```

---

## **6. Configure Jenkins for Docker and AWS**

1. **Install Jenkins Plugins**:
   - Go to **Manage Jenkins > Plugin Manager > Available**.
   - Install the following:
     - **Docker Pipeline**
     - **Amazon ECR Plugin**.

2. **Add AWS Credentials in Jenkins**:
   - Go to **Manage Jenkins > Credentials > System > Add Credentials**.
   - Add the following:
     - **Username**: IAM Access Key ID.
     - **Password**: IAM Secret Access Key.
     - **ID**: `aws-ecr-credentials`.

3. **Verify Jenkins Docker Access**:
   ```bash
   sudo su - jenkins
   docker ps
   ```

---

## **7. Pipeline to Push Docker Image to ECR**
Create a pipeline job to build and push Docker images to ECR.

### **Pipeline Script**

```groovy
pipeline {
    agent any
    environment {
        AWS_DEFAULT_REGION = 'us-east-1' // Replace with your AWS region
        ECR_REPO = '<your-ecr-repository>'
        IMAGE_TAG = 'latest'
        AWS_ACCOUNT_ID = '<your-aws-account-id>'
    }
    stages {
        stage('Checkout Code') {
            steps {
                echo 'Cloning repository...'
                checkout scm
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker Image...'
                sh 'docker build -t $ECR_REPO:$IMAGE_TAG .'
            }
        }
        stage('Push to ECR') {
            steps {
                echo 'Pushing Image to ECR...'
                sh "$(aws ecr get-login-password --region $AWS_DEFAULT_REGION) | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com"
                sh 'docker tag $ECR_REPO:$IMAGE_TAG $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG'
                sh 'docker push $AWS_ACCOUNT_ID.dkr.ecr.$AWS_DEFAULT_REGION.amazonaws.com/$ECR_REPO:$IMAGE_TAG'
            }
        }
    }
}
```

---

## **8. Verification Steps**

1. **Check Jenkins Service**:
   ```bash
   sudo systemctl status jenkins
   ```

2. **Verify Docker Installation**:
   ```bash
   docker --version
   ```

3. **Verify AWS CLI Installation**:
   ```bash
   aws --version
   ```

4. **Check Image in ECR**:
   - Go to **AWS Console > ECR**.
   - Verify that the image is successfully pushed.

---

## **9. Troubleshooting Commands**

### **Docker Troubleshooting**
- Restart Docker Service:
   ```bash
   sudo systemctl restart docker
   ```
- Fix Docker Socket Permissions:
   ```bash
   sudo chmod 666 /var/run/docker.sock
   ```

### **Jenkins Troubleshooting**
- Check Jenkins Logs:
   ```bash
   sudo tail -n 50 /var/log/jenkins/jenkins.log
   ```
- Restart Jenkins Service:
   ```bash
   sudo systemctl restart jenkins
   ```

### **AWS CLI Troubleshooting**
- Verify AWS Configuration:
   ```bash
   aws configure list
   ```
- Test AWS Access:
   ```bash
   aws sts get-caller-identity
   ```

---

## **Summary**
By following this guide, you have:

1. Installed Jenkins, Docker, and AWS CLI on your EC2 instance.
2. Configured Jenkins to push Docker images to AWS ECR.
3. Created a Jenkins pipeline for automation.
4. Verified and troubleshot common issues.

This setup provides a complete CI/CD pipeline for building and pushing images to AWS ECR. 🚀


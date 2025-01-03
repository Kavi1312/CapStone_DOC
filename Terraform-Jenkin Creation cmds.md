# Setting up Terraform and Jenkins with Kubernetes, AWS CLI

## Prerequisites
1. **AWS Bucket Creation**
   - Create an S3 bucket in the AWS region `us-east-2`.
   - Note the bucket name and update it in the `backend.tf` file in the repository [Infracreation](https://github.com/ashrafgate/Infracreation.git):

```hcl
terraform {
  backend "s3" {
    bucket = "mytodoappbucket"
    key    = "jenkins/terraform.tfstate"
    region = "us-east-2"
  }
}
```

## Jenkins Installation

### Installation Script
Edit the `jenkins-server/jenkins-install.sh` file and include the following script for Jenkins installation:

```bash
#!/bin/bash

# Update and install required dependencies
sudo apt update
sudo apt install -y openjdk-11-jre

# Docker and Jenkins dependencies
sudo apt-get update -y
sudo apt-get install -y ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo $VERSION_CODENAME) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update -y
sudo apt install -y fontconfig openjdk-17-jre

# Add Jenkins repository and install Jenkins
sudo curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
sudo echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
sudo apt-get install -y jenkins

# Start Jenkins service
sudo systemctl start jenkins
sudo systemctl status jenkins
```

## Terraform Installation

Run the following commands to install Terraform:

```bash
sudo apt-get update && sudo apt-get install -y gnupg software-properties-common
wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update
sudo apt-get install terraform -y
```

## Kubernetes Installation

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

## AWS CLI Installation

```bash
sudo apt install unzip
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

## Setting Up Terraform

1. Open VS Code and navigate to the repository folder:

```bash
cd ./infracreation
cd ./Jenkins-server
```

2. Configure AWS credentials:

```bash
aws configure
```
   - Use the region `us-east-2` (same as the S3 bucket).

3. Initialize Terraform:

```bash
terraform init
terraform init -reconfigure
terraform plan
terraform apply
```

4. To destroy resources:

```bash
terraform destroy
```

## Connecting to Jenkins

1. Check the EC2 instance `jenkins_server` status and connect once it is ready.
2. Retrieve the initial Jenkins admin password:

```bash
cd /var/lib/jenkins/secrets
chmod 777 initialAdminPassword
cat initialAdminPassword
```
3. Use the password in the browser to finalize Jenkins setup.

---

## Comments
1. **Clarity**: Each script and command block should have clear inline comments for easier understanding.
2. **Security**: Use minimal permissions (`chmod 777` is excessive) for sensitive files.
3. **Consistency**: Standardize commands, such as ensuring regions and key names match throughout the setup.
4. **Error Handling**: Add validations for critical operations, such as checking the status of Terraform and Jenkins commands.

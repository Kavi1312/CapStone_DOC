
---# EKS Setup and Installation Documentation

## Overview
This document provides step-by-step instructions to set up Amazon EKS, including installing necessary tools (`awscli`, `kubectl`, `eksctl`), configuring AWS CLI, and creating an EKS cluster with managed nodes.

---

## Prerequisites
- AWS account with necessary permissions.
- A Linux-based system (e.g., Ubuntu).
- IAM role or credentials with the following policies:
  - `AmazonEKSClusterPolicy`
  - `AmazonEKSWorkerNodePolicy`
  - `AmazonEC2ContainerRegistryReadOnly`

---

## Step-by-Step Commands

### 1. Install AWS CLI
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install -y unzip
unzip awscliv2.zip
sudo ./aws/install
aws --version
```
**Purpose**: Installs AWS CLI to enable interaction with AWS services.

---

### 2. Install `eksctl`

•	curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tm
sudo mv /tmp/eksctl /usr/local/bin
```
**Purpose**: Installs `eksctl`, a CLI tool to simplify EKS cluster creation and management.

---

### 3. Install `kubectl`
#### Method 1: Using Snap
```bash
snap install kubectl --classic
```

#### Method 2: Using Binary Download
```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl.sha256"
echo "$(cat kubectl.sha256)  kubectl" | sha256sum --check
chmod +x kubectl
sudo mv kubectl /usr/local/bin/
kubectl version --client
```
**Purpose**: Installs `kubectl`, the Kubernetes CLI tool for managing clusters.

---

### 4. Configure AWS CLI
```bash
aws configure
```
Provide:
- **Access Key ID**
- **Secret Access Key**
- **Default region** (e.g., `us-east-1`)
- **Output format** (e.g., `json`).

**Purpose**: Configures AWS CLI with credentials and region.

---

### 5. Create an EKS Cluster
```bash
eksctl create cluster \
    --name demo-eks \
    --region us-east-1 \
    --nodegroup-name my-nodes \
    --node-type t2.micro \
    --managed \
    --nodes 2
```
**Flags**:
- `--name`: Name of the cluster.
- `--region`: AWS region for the cluster.
- `--nodegroup-name`: Name of the worker node group.
- `--node-type`: Instance type for the worker nodes.
- `--managed`: Enables managed nodes.
- `--nodes`: Number of worker nodes.

---

### 6. Verify Cluster and Nodes
```bash
kubectl get nodes
```
**Purpose**: Lists the nodes in the cluster to verify connectivity.

---

### 7. Attach IAM Policies
```bash
aws iam attach-role-policy --role-name <role-name> --policy-arn arn:aws:iam::aws:policy/AmazonEKSClusterPolicy
aws iam list-attached-role-policies --role-name eksClusterRole
```
**Purpose**: Ensures necessary IAM policies are attached to the role.

---

### 8. Verify Cluster Status
```bash
aws eks describe-cluster --name demo-eks --query "cluster.status" --region us-east-1
```
**Purpose**: Checks the status of the EKS cluster (e.g., `ACTIVE`).

---

### 9. Additional Steps
#### Verify Metadata Service
```bash
curl http://169.254.169.254/latest/meta-data/
```
**Purpose**: Confirms that the EC2 metadata service is enabled for IAM role retrieval.

#### Test AWS CLI Configuration
```bash
aws sts get-caller-identity
```
**Purpose**: Verifies the identity and permissions of the AWS CLI configuration.

---

## Additional Commands Used

### Repeated or Troubleshooting Commands
```bash
snap install aws-cli
sudo apt install -y awscli
sudo apt-get update
sudo apt-get install -y kubectl
```
**Purpose**: These commands were used to retry or resolve issues during the setup process.

---

### Alternative Methods for Installing Tools
```bash
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
kubectl version --client --output=yaml
```
**Purpose**: Provides alternative paths for installing and verifying `kubectl`.

---

### Verifications and Status Checks
```bash
aws configure
aws sts get-caller-identity
aws --version
```
**Purpose**: Ensures the AWS CLI and credentials are correctly configured.

---

## Summary
1. Installed required tools (`awscli`, `eksctl`, `kubectl`).
2. Configured AWS CLI with credentials and region.
3. Created an EKS cluster with a managed node group.
4. Verified cluster connectivity using `kubectl`.
5. Ensured correct IAM policies for role-based access.

**Note**: Follow these steps sequentially to ensure a successful EKS setup.

---




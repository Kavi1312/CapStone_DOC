# ECS Fargate Setup with ECR Image

## Step 1: Log in to AWS Management Console
1. Go to the [AWS Console](https://aws.amazon.com/console/).
2. Navigate to **Elastic Container Service (ECS)** under **Containers**.

---

## Step 2: Create an ECS Cluster (Fargate)
1. On the ECS page, click **Clusters** from the left panel.
2. Click on **Create Cluster**.
3. Select **Networking only (Powered by AWS Fargate)**.
   - Fargate is a serverless compute engine for containers. You don’t need to manage servers.
4. Enter a name for your cluster, e.g., `webapp-cluster`.
5. Click **Create**. AWS will provision a new ECS Fargate cluster.

---

## Step 3: Create a Task Definition
A **task definition** is a blueprint that tells ECS which container image to use, CPU/memory limits, and other settings.

### 3.1: Create a New Task Definition
1. On the ECS page, click **Task Definitions** from the left panel.
2. Click **Create new Task Definition**.
3. Choose **Fargate** as the launch type.
4. Click **Next Step**.

### 3.2: Configure Task Definition
1. **Task Definition Name**: `webapp-task`
2. **Task Role**: Leave it as **None**.
3. **Operating System Family**: Choose **Linux**.
4. **Task Memory (GB)**: `0.5 GB` or higher based on your app.
5. **Task CPU (vCPU)**: `0.25 vCPU` or higher based on your app.

### 3.3: Add Container Details
1. Click **Add container**.
2. Enter the following details:
   - **Container name**: `webapp-container`.
   - **Image URI**:  
     ```
     225989348530.dkr.ecr.us-east-1.amazonaws.com/app/webapprepo:latest
     ```
   - **Port mappings**:  
     - **Port**: `80` (if your app runs on HTTP).
3. Click **Add** to save container details.
4. Scroll down and click **Create** to finalize the task definition.

---

## Step 4: Create a Service in ECS Cluster
A **Service** ensures your task runs continuously.

1. Go back to your **Cluster** (`webapp-cluster`).
2. Click **Create** under the **Services** tab.

### 4.1: Service Configuration
1. **Launch type**: Select **Fargate**.
2. **Task Definition**: Choose the task definition you created earlier (`webapp-task`).
3. **Cluster**: Select your cluster (`webapp-cluster`).
4. **Service name**: Enter `webapp-service`.
5. **Number of tasks**: `1` (this will run 1 container).

### 4.2: Configure Networking
1. **Cluster VPC**: Select the default VPC or your existing VPC.
2. **Subnets**: Choose **public subnets** to allow external access.
3. **Security groups**: Select or create a security group that allows HTTP traffic (port `80`).

---

## Step 5: Verify and Deploy
1. Once the service is created, go to the **Tasks** tab under your cluster.
2. Ensure the task is in the **RUNNING** state.
3. Note the public IP or DNS of the task to access your application.

---

## Notes
- Ensure your ECR image is pushed and accessible before creating the task.
- If you are using a custom security group, allow inbound HTTP access (port `80`).
- Monitor logs and metrics using **CloudWatch** for troubleshooting.

---

### References
- [AWS ECS Documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/what-is-ecs.html)
- [AWS Fargate Overview](https://aws.amazon.com/fargate/)

# Deploy Super Mario Game on EKS using Terraform via AWS EC2 🚀🎮

## Project Overview 🕹️

Who doesn't love **Super Mario**? It's a timeless game that takes us back to our childhood. In this project, we'll deploy the Super Mario game on **Amazon EKS (Elastic Kubernetes Service)** using **Terraform**. We'll go through every step, from setting up the EC2 instance to running the game on Kubernetes!

Don't worry—I'll explain the purpose of each step, so it's not just a set of instructions but a learning experience. Whether you're new to AWS or a seasoned DevOps professional, this guide will make sure you understand why we do things the way we do.

---

## Prerequisites ✅

1. **AWS Account:** Make sure you have access to your AWS account.
2. **Basic AWS Knowledge:** Familiarity with AWS, EC2, and EKS will be helpful.
3. **Terraform:** This project uses Terraform, so having a basic understanding of it will help.

---

## Completion Steps 🛠️

We'll go through the following steps:

- [Step 1: Login and Basic Setup](#step-1:-login-and-basic-setup-🔑)
- **Step 2**: Setup Docker, Terraform, AWS CLI, and Kubectl
- **Step 3**: Create IAM Role for EC2
- **Step 4**: Attach IAM Role to EC2
- **Step 5**: Build Infrastructure using Terraform
- **Step 6**: Create Deployment and Service for EKS
- **Step 7**: Destroy the Infrastructure (save your AWS bill!)

Let's get started! 🎮

---

## Step 1: Login and Basic Setup 🔑

1. **Login to your AWS Account:** Log in as a root user.
2. **Launch an EC2 Instance:** Head to the EC2 console and launch an instance with the following:
   - Allow HTTP, HTTPS, and set a key pair.
   - Choose an Ubuntu AMI for compatibility with Docker and Terraform.
3. **Connect to EC2:** 
   - Go to the EC2 dashboard, select your instance, and click **Connect**.
   - Use the **EC2 Instance Connect** option to get terminal access.
4. **Run some basic commands:**
   ```bash
   sudo su
   apt update -y
   ```

---

## Step 2: Setup Docker, Terraform, AWS CLI, and Kubectl 🛠️

### **Install Docker**
Docker helps us containerize applications. It will play a crucial role in deploying Kubernetes resources.

```bash
apt install docker.io -y
usermod -aG docker $USER
newgrp docker
```

### **Install Terraform**
Terraform is the backbone of this project, enabling us to define and deploy infrastructure as code.

```bash
sudo apt install wget -y
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y
```

### **Install AWS CLI**
AWS CLI allows you to interact with AWS resources via command-line, which is essential for managing EKS.

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt-get install unzip -y
unzip awscliv2.zip
sudo ./aws/install
```

### **Install Kubectl**
Kubectl is the command-line tool to interact with Kubernetes clusters.

```bash
sudo apt install curl -y
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

---

## Step 3: Create IAM Role for EC2 🔐

### **Why IAM Role?**
We need an IAM role to allow our EC2 instance to interact with AWS services (like EKS, S3, etc.) without hardcoding credentials.

1. **Open IAM** from the AWS console.
2. **Create a Role** with **EC2** as the trusted entity.
3. **Select Administrator Access** as the permission policy (for simplicity in this project).
4. **Give the role a name** and create it.

---

## Step 4: Attach IAM Role to EC2 🔗

1. Go to the **EC2 dashboard**.
2. Click on **Actions > Security > Modify IAM Role**.
3. Select the IAM role you created in Step 3 and attach it to your EC2 instance.

---

## Step 5: Building Infrastructure Using Terraform 🛠️

Now that everything is set up, let's use Terraform to build the infrastructure.

### **Clone the Super Mario GitHub Repo**
```bash
mkdir super_mario
cd super_mario
git clone https://github.com/Aakibgithuber/Deployment-of-super-Mario-on-Kubernetes-using-terraform.git
cd Deployment-of-super-Mario-on-Kubernetes-using-terraform/EKS-TF
```

### **Edit `backend.tf`**
Make sure to provide your S3 bucket name and region in the `backend.tf` file. This allows Terraform to store the state file in S3.

### **Run Terraform Commands**

1. **Initialize Terraform**:
   ```bash
   terraform init
   ```
   This command sets up your working environment.

2. **Validate the Configuration**:
   ```bash
   terraform validate
   ```
   This ensures there are no syntax errors in your Terraform files.

3. **Plan the Infrastructure**:
   ```bash
   terraform plan
   ```
   Terraform will show you a blueprint of what resources will be created.

4. **Apply the Plan**:
   ```bash
   terraform apply --auto-approve
   ```
   This command creates the infrastructure automatically.

---

## Step 6: Create Deployment and Service for EKS 🎮

Now that EKS is up, let’s deploy the Super Mario game!

1. Change directory to where the deployment and service YAML files are stored:
   ```bash
   cd ..
   ```

2. **Create Deployment**:
   ```bash
   kubectl apply -f deployment.yaml
   ```

3. **Create Service**:
   ```bash
   kubectl apply -f service.yaml
   ```

4. **Get All Resources**:
   ```bash
   kubectl get all
   ```

5. **Get the Load Balancer Ingress**:
   ```bash
   kubectl describe service mario-service
   ```
   Copy the **Load Balancer Ingress** and paste it in your browser. The Super Mario game should now be running! 🕹️

---

## Step 7: Destroy the Infrastructure 🧹

Before you leave, remember to clean up to avoid unnecessary AWS charges!

1. **Delete the Service and Deployment**:
   ```bash
   kubectl delete service mario-service
   kubectl delete deployment mario-deployment
   ```

2. **Destroy the EKS Cluster**:
   ```bash
   cd EKS-TF
   terraform destroy --auto-approve
   ```

3. **Terminate the EC2 Instance**:
   Go to the EC2 dashboard and terminate your instance.

---

## Conclusion 🎉

That's it! You've successfully deployed and played the Super Mario game on EKS using Terraform. This project helps you understand the fundamentals of Kubernetes, EKS, and how Terraform can manage infrastructure effortlessly. See you in the next project! 👋

# Deploy Super Mario Game on EKS using Terraform via AWS EC2 🚀🎮

## Project Overview 🕹️

![chrome_oOzsyPjd4Z](https://github.com/user-attachments/assets/d289a6ad-10d0-4fa9-9551-4062cf380917)

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

- [Step 1: Login and Basic Setup](#step-1:-login-and-basic-setup)
- **Step 2**: Setup Docker, Terraform, AWS CLI, and Kubectl
- **Step 3**: Create IAM Role for EC2
- **Step 4**: Attach IAM Role to EC2
- **Step 5**: Build Infrastructure using Terraform
- **Step 6**: Create Deployment and Service for EKS
- **Step 7**: Destroy the Infrastructure (save your AWS bill!)

Let's get started! 🎮

---

## **Step 1: Login and Basic Setup** 🔑
1. **Login to your AWS Account:** Log in as a root user.

![chrome_59e9QQf6An](https://github.com/user-attachments/assets/59478c23-5f96-43f8-a1a1-01657b6107a6)

2. **Launch an EC2 Instance:** Head to the EC2 console and launch an instance with the following:
   - **Name:** Mario Game
   - **AMI**: Amazon Linux 2 (Choose an Ubuntu AMI for compatibility with Docker and Terraform.)
   - Allow HTTP, HTTPS, and set a key pair.

![chrome_UIrRkLTVr3](https://github.com/user-attachments/assets/fbe1f4a2-bd25-49df-96c8-1585c61a42b0)

3. **Connect to EC2:** 
   - Go to the EC2 dashboard, select your instance, and click **Connect**.
   - Use the **EC2 Instance Connect** or via **SSH Client** option to get terminal access.

![chrome_2fcojOLbly](https://github.com/user-attachments/assets/0b8b7346-42b7-4bd1-ba80-96333ce6359d)

4. **Run some basic commands:**
   ```bash
   sudo su
   apt update -y
   ```

![chrome_GKxHuZbjqv](https://github.com/user-attachments/assets/c61965ef-47c4-4abb-b687-6004686ecdbf)

---

## Step 2: Setup Docker, Terraform, AWS CLI, and Kubectl 🛠️

### **Install Docker**
Docker helps us containerize applications. It will play a crucial role in deploying Kubernetes resources.

```bash
apt install docker.io -y
usermod -aG docker $USER
newgrp docker
```

![chrome_KCqK8rLW8t](https://github.com/user-attachments/assets/98300aaa-ca1c-45f9-9143-84bdbec4d5a7)

### **Install Terraform**
Terraform is the backbone of this project, enabling us to define and deploy infrastructure as code.

```bash
sudo apt install wget -y
wget -O- https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list
sudo apt update && sudo apt install terraform -y
```

![chrome_t81eCGG7YI](https://github.com/user-attachments/assets/1e6f345b-4cef-4c46-a5e2-eb2832898ab3)

### **Install AWS CLI**
AWS CLI allows you to interact with AWS resources via command-line, which is essential for managing EKS.

```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt-get install unzip -y
unzip awscliv2.zip
sudo ./aws/install
```

![chrome_ke5iAth3PV](https://github.com/user-attachments/assets/05efac3b-c3bc-4006-a59b-2d72da36f633)

### **Install Kubectl**
Kubectl is the command-line tool to interact with Kubernetes clusters.

```bash
sudo apt install curl -y
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

![chrome_R6Q9sgSgMl](https://github.com/user-attachments/assets/22af46db-c63a-467d-b98f-baf86fb1a20f)

---

## Step 3: Create IAM Role for EC2 🔐

### **Why IAM Role?**
We need an IAM role to allow our EC2 instance to interact with AWS services (like EKS, S3, etc.) without hardcoding credentials.

1. **Open IAM** from the AWS console.
2. **Create a Role** with **EC2** as the trusted entity.
3. **Select Administrator Access** as the permission policy (for simplicity in this project).
4. **Give the role a name** and create it.

![chrome_Meui5kYW0F](https://github.com/user-attachments/assets/d136fedd-ced6-4d3d-8fc2-f21719836a83)

---

## Step 4: Attach IAM Role to EC2 🔗

1. Go to the **EC2 dashboard**.
2. Click on **Actions > Security > Modify IAM Role**.
3. Select the IAM role you created in Step 3 and attach it to your EC2 instance.

![chrome_GgUVYNex5Z](https://github.com/user-attachments/assets/ad5ca74a-5252-463c-8d47-8ed79e360228)

---

## Step 5: Building Infrastructure Using Terraform 🛠️

Now that everything is set up, let's use Terraform to build the infrastructure.

### **Clone the Super Mario GitHub Repo**
```bash
mkdir super_mario
cd super_mario
git clone https://github.com/dahrihadri/SuperMario-EKS-Terraform-Deployment.git
cd SuperMario-EKS-Terraform-Deployment/EKS-TF
```

![chrome_xQbu3B64Sj](https://github.com/user-attachments/assets/38a9046d-29d9-4a8f-acbd-73a0e18b14f2)


### **Edit `backend.tf`**
Make sure to provide your S3 bucket name and region in the `backend.tf` file. This allows Terraform to store the state file in S3.

![image](https://github.com/user-attachments/assets/5e3cc980-f6cc-4021-87ca-638f0f20bba9)

### **Run Terraform Commands**

1. **Initialize Terraform**:
   ```bash
   terraform init
   ```
   This command sets up your working environment.

![chrome_OgRDsGMbzg](https://github.com/user-attachments/assets/13977652-5f93-47a3-8040-addbdf4166fe)

2. **Validate the Configuration**:
   ```bash
   terraform validate
   ```
   This ensures there are no syntax errors in your Terraform files.

![chrome_qeitS98lz0](https://github.com/user-attachments/assets/453f0e1b-b7c1-4fa1-8f00-71906d41b9a8)

3. **Plan the Infrastructure**:
   ```bash
   terraform plan
   ```
   Terraform will show you a blueprint of what resources will be created.

![chrome_IiV5QD2NrJ](https://github.com/user-attachments/assets/4fae7d0f-2840-4963-bdfb-16585e27f39d)


4. **Apply the Plan**:
   ```bash
   terraform apply --auto-approve
   ```
   This command creates the infrastructure automatically.

Below command is used to update the configuration of EKS

   ```bash
   aws eks update-kubeconfig --name EKS_CLOUD --region us-east-1
   ```
   
The command aws eks update-kubeconfig --name EKS_CLOUD --region us-east-1 is like telling our computer, "Hey, I'm using Amazon EKS (Elastic Kubernetes Service) in the 'us-east-1' region, and I want to connect to it. you could use your desired location

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

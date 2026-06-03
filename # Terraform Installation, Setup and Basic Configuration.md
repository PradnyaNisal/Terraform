# Complete Terraform Setup on AWS - Step-by-Step Guide

## Objective

In this project, you will:

* Create an AWS Account
* Create an IAM User
* Install AWS CLI
* Install Terraform
* Configure AWS Credentials
* Create Your First Terraform Project
* Deploy an EC2 Instance using Terraform
* Verify Resources in AWS
* Destroy Resources

---

# Step 1: Create AWS Account

1. Go to AWS Console
2. Sign up with email and password
3. Verify phone number
4. Add payment method
5. Login to AWS Management Console

---

# Step 2: Create IAM User

### Open IAM Service

AWS Console → IAM → Users → Create User

### User Details

```text
User Name: terraform-user
```

Enable:

```text
Provide user access to AWS Management Console
```

---

### Attach Permissions

Choose:

```text
AdministratorAccess
```

(For learning purposes only)

Create User.

---

# Step 3: Generate Access Keys

Open:

```text
IAM → Users → terraform-user
```

Select:

```text
Security Credentials
```

Click:

```text
Create Access Key
```

Choose:

```text
Command Line Interface (CLI)
```

Save:

```text
Access Key ID
Secret Access Key
```

Important: Store these safely.

---

# Step 4: Launch EC2 Instance

AWS Console → EC2 → Launch Instance

Configuration:

```text
Name: Terraform-Server
AMI: Ubuntu 22.04
Instance Type: t2.micro
Key Pair: Create New Key Pair
Security Group:
  SSH (22) → My IP
```

Launch Instance.

---

# Step 5: Connect to EC2

From your terminal:

```bash
chmod 400 terraform.pem

ssh -i terraform.pem ubuntu@<PUBLIC-IP>
```

Example:

```bash
ssh -i terraform.pem ubuntu@54.xx.xx.xx
```

---

# Step 6: Update Ubuntu Server

```bash
sudo apt update
sudo apt upgrade -y
```

---

# Step 7: Install AWS CLI

```bash
sudo apt install awscli -y
```

Verify:

```bash
aws --version
```

Expected:

```text
aws-cli/2.x.x
```

---

# Step 8: Configure AWS CLI

Run:

```bash
aws configure
```

Enter:

```text
AWS Access Key ID
AWS Secret Access Key
Default region: us-east-1
Output format: json
```

Verify:

```bash
aws sts get-caller-identity
```

---

# Step 9: Install Terraform

Install dependencies:

```bash
sudo apt update
sudo apt install -y gnupg software-properties-common curl
```

Add HashiCorp GPG Key:

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg
```

Add Repository:

```bash
echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com \
$(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list
```

Install Terraform:

```bash
sudo apt update
sudo apt install terraform -y
```

Verify:

```bash
terraform version
```

---

# Step 10: Create Terraform Project

```bash
mkdir terraform-project

cd terraform-project
```

Create files:

```bash
touch main.tf
touch variables.tf
touch outputs.tf
```

Check:

```bash
ls
```

Output:

```text
main.tf
variables.tf
outputs.tf
```

---

# Step 11: Write Terraform Code

## main.tf

```terraform
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web_server" {
  ami           = "ami-091138d0f0d41ff90"
  instance_type = "t2.micro"

  tags = {
    Name = "Terraform-Web-Server"
  }
}
```

---

## outputs.tf

```terraform
output "instance_id" {
  value = aws_instance.web_server.id
}

output "public_ip" {
  value = aws_instance.web_server.public_ip
}
```

---

# Step 12: Initialize Terraform

```bash
terraform init
```

Expected:

```text
Terraform has been successfully initialized!
```

---

# Step 13: Validate Configuration

```bash
terraform validate
```

Expected:

```text
Success! The configuration is valid.
```

---

# Step 14: Create Execution Plan

```bash
terraform plan
```

Terraform shows resources that will be created.

---

# Step 15: Deploy Infrastructure

```bash
terraform apply
```

Type:

```text
yes
```

Terraform starts creating resources.

Expected:

```text
Apply complete!
```

---

# Step 16: Verify Resource

Check EC2 Console:

```text
AWS Console → EC2 → Instances
```

You should see:

```text
Terraform-Web-Server
```

Running successfully.

---

# Step 17: View Outputs

```bash
terraform output
```

Example:

```text
instance_id = i-xxxxxxxxxxxx
public_ip   = 54.xx.xx.xx
```

---

# Step 18: View Terraform State

```bash
terraform state list
```

Output:

```text
aws_instance.web_server
```

---

# Step 19: Modify Infrastructure

Change:

```terraform
instance_type = "t3.micro"
```

Run:

```bash
terraform plan
terraform apply
```

Terraform updates infrastructure automatically.

---

# Step 20: Destroy Infrastructure

To avoid AWS charges:

```bash
terraform destroy
```

Type:

```text
yes
```

Expected:

```text
Destroy complete!
```

---

# Complete Terraform Workflow

```text
Create AWS Account
        ↓
Create IAM User
        ↓
Generate Access Keys
        ↓
Launch EC2 Server
        ↓
Install AWS CLI
        ↓
Configure AWS Credentials
        ↓
Install Terraform
        ↓
Write Terraform Code
        ↓
terraform init
        ↓
terraform validate
        ↓
terraform plan
        ↓
terraform apply
        ↓
Verify Resources
        ↓
terraform destroy
```

---

# Skills Demonstrated

* AWS IAM
* AWS EC2
* AWS CLI
* Terraform Providers
* Terraform Resources
* Terraform State Management
* Infrastructure as Code (IaC)
* Cloud Automation
* DevOps Fundamentals



## Prerequisites

Before installing Terraform, make sure you have:

* AWS Account
* IAM User with Programmatic Access
* AWS CLI Installed
* Git Installed

---

# Step 1: Install Terraform

## Ubuntu

```bash
sudo apt update

sudo apt install -y gnupg software-properties-common curl

curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update

sudo apt install terraform -y
```

Verify installation:

```bash
terraform version
```

---

# Step 2: Install AWS CLI

```bash
sudo apt update
sudo apt install awscli -y
```

Verify:

```bash
aws --version
```

---

# Step 3: Configure AWS Credentials

```bash
aws configure
```

Enter:

```text
AWS Access Key ID
AWS Secret Access Key
Region Name (us-east-1)
Output Format (json)
```

Verify:

```bash
aws sts get-caller-identity
```

---

# Step 4: Create Terraform Project

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

Project Structure:

```text
terraform-project/
│
├── main.tf
├── variables.tf
├── outputs.tf
└── README.md
```

---

# Basic Terraform Code

## main.tf

```terraform
provider "aws" {
  region = "us-east-1"
}

resource "aws_instance" "web" {
  ami           = "ami-091138d0f0d41ff90"
  instance_type = "t3.micro"

  tags = {
    Name = "Terraform-EC2"
  }
}
```

---

## variables.tf

```terraform
variable "region" {
  default = "us-east-1"
}

variable "instance_type" {
  default = "t3.micro"
}
```

---

## outputs.tf

```terraform
output "instance_id" {
  value = aws_instance.web.id
}

output "public_ip" {
  value = aws_instance.web.public_ip
}
```

---

# Terraform Commands

## Initialize

```bash
terraform init
```

## Validate

```bash
terraform validate
```

## Format Code

```bash
terraform fmt
```

## Create Execution Plan

```bash
terraform plan
```

## Deploy Infrastructure

```bash
terraform apply
```

Type:

```text
yes
```

---

## View Resources

```bash
terraform state list
```

---

## View Outputs

```bash
terraform output
```

---

## Destroy Infrastructure

```bash
terraform destroy
```

Type:

```text
yes
```

---

# Terraform Workflow

```text
Write Code
     ↓
terraform init
     ↓
terraform validate
     ↓
terraform plan
     ↓
terraform apply
     ↓
Resources Created
     ↓
terraform destroy
```

---

# Common Interview Questions

### What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool developed by HashiCorp that allows cloud infrastructure to be provisioned and managed using code.

### What is a Provider?

A provider is a plugin that allows Terraform to interact with cloud platforms such as AWS, Azure, and GCP.

### What is a Resource?

A resource is any infrastructure component managed by Terraform, such as EC2 instances, VPCs, Subnets, and Load Balancers.

### Difference Between terraform plan and terraform apply?

* `terraform plan` shows what changes Terraform will make.
* `terraform apply` actually creates or updates the infrastructure.

### What is Terraform State?

Terraform State is a file (`terraform.tfstate`) that stores information about managed infrastructure resources.

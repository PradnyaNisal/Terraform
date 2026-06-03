

# This Terraform project provisions two EC2 instances in AWS using Infrastructure as Code (IaC).

The project demonstrates:

* AWS Provider Configuration
* EC2 Instance Creation
* Terraform Resource Management
* Terraform Outputs
* Infrastructure Automation

---

## Architecture

```text
                    AWS Cloud
                        |
                        |
                +---------------+
                | Terraform     |
                +---------------+
                        |
        --------------------------------
        |                              |
        v                              v

 +----------------+          +----------------+
 | EC2 Instance 1 |          | EC2 Instance 2 |
 | Name: my_ec2   |          | Name: terraform|
 +----------------+          +----------------+
```

---

## Project Structure

```text
terraform-ec2/
│
├── provider.tf
├── main.tf
├── outputs.tf
├── variables.tf
└── README.md
```

---

## Provider Configuration

### provider.tf

```terraform
provider "aws" {
  region = "eu-north-1"
}
```

### Why Used?

The AWS Provider allows Terraform to communicate with AWS services.

### Benefits

* Connects Terraform with AWS
* Manages cloud resources
* Supports Infrastructure as Code

---

## EC2 Instance Configuration

### main.tf

```terraform
resource "aws_instance" "my_ec2" {
  ami           = "ami-091138d0f0d41ff90"
  instance_type = "t3.micro"

  tags = {
    Name = "my_ec2"
  }
}

resource "aws_instance" "terraform" {
  ami           = "ami-091138d0f0d41ff90"
  instance_type = "t3.micro"

  tags = {
    Name = "terraform"
  }
}
```

### What Does This Create?

* 2 EC2 Instances
* Instance Type: t3.micro
* Region: eu-north-1
* Custom Tags

---

## Outputs

### outputs.tf

```terraform
output "my_ec2_instance_id" {
  value = aws_instance.my_ec2.id
}

output "terraform_instance_id" {
  value = aws_instance.terraform.id
}

output "my_ec2_public_ip" {
  value = aws_instance.my_ec2.public_ip
}

output "terraform_public_ip" {
  value = aws_instance.terraform.public_ip
}
```

### Why Outputs?

Outputs display useful information after deployment.

Examples:

* Instance ID
* Public IP Address
* Resource ARN

## Sample Output

```text
my_ec2_instance_id = i-0123456789abcdef0

terraform_instance_id = i-0abcdef1234567890

my_ec2_public_ip = 13.48.xxx.xxx

terraform_public_ip = 16.170.xxx.xxx
```

---

# Technologies Used

* Terraform
* AWS EC2
* AWS Provider
* Infrastructure as Code (IaC)

---

# Benefits

* Automated Infrastructure Provisioning
* Repeatable Deployments
* Version Controlled Infrastructure
* Easy Resource Management
* Reduced Manual Configuration

---

# Interview Questions

## What is Terraform?

Terraform is an Infrastructure as Code (IaC) tool developed by HashiCorp used to automate cloud infrastructure provisioning.

## What is a Provider?

A Provider is a plugin that enables Terraform to interact with cloud platforms such as AWS, Azure, and GCP.

## What is a Resource?

A Resource represents an infrastructure component managed by Terraform, such as EC2, VPC, or RDS.

## What is terraform init?

Downloads providers and initializes the Terraform working directory.

## What is terraform plan?

Shows the execution plan before resources are created.

## What is terraform apply?

Creates or updates infrastructure resources.

## What is terraform destroy?

Removes all resources managed by Terraform.

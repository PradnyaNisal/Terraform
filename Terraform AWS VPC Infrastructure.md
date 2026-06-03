# Terraform AWS VPC Infrastructure

## Project Overview

This project provisions a secure AWS networking infrastructure using Terraform.

The infrastructure includes:

* Custom VPC
* Public Subnet
* Private Subnet
* Internet Gateway
* Elastic IP
* NAT Gateway
* Public Route Table
* Private Route Table
* Route Table Associations

The architecture follows AWS networking best practices by separating public and private resources while allowing secure internet access for private resources through a NAT Gateway.

---

## Architecture Diagram

```text
                         Internet
                             |
                             |
                    +----------------+
                    | Internet Gateway|
                    +----------------+
                             |
                             |
                    +----------------+
                    |   VPC          |
                    | 10.0.0.0/16    |
                    +----------------+
                      /            \
                     /              \
                    /                \
      +-------------------+    +-------------------+
      | Public Subnet     |    | Private Subnet    |
      | 10.0.1.0/24       |    | 10.0.2.0/24       |
      | us-east-1a        |    | us-east-1b        |
      +-------------------+    +-------------------+
                |                         |
                |                         |
       +----------------+        +----------------+
       | NAT Gateway    |------->| Private EC2    |
       +----------------+        +----------------+
                |
                |
         +-------------+
         | Elastic IP  |
         +-------------+
```

---

## AWS Resources Used

| Resource                 | Purpose                               |
| ------------------------ | ------------------------------------- |
| VPC                      | Creates an isolated AWS network       |
| Public Subnet            | Hosts internet-facing resources       |
| Private Subnet           | Hosts secure internal resources       |
| Internet Gateway         | Enables internet connectivity         |
| Elastic IP               | Provides static public IP             |
| NAT Gateway              | Allows private subnet internet access |
| Route Tables             | Controls network traffic              |
| Route Table Associations | Connects subnets to route tables      |

---

## Project Structure

```text
Terraform/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── README.md
```

---

## Terraform Configuration

### AWS Provider

```terraform
provider "aws" {
  region = "us-east-1"
}
```

### VPC

```terraform
resource "aws_vpc" "home_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "home-vpc"
  }
}
```

### Public Subnet

```terraform
resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.home_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true
}
```

### Private Subnet

```terraform
resource "aws_subnet" "private_subnet" {
  vpc_id            = aws_vpc.home_vpc.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"
}
```

### Internet Gateway

```terraform
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.home_vpc.id
}
```

### NAT Gateway

```terraform
resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat_eip.id
  subnet_id     = aws_subnet.public_subnet.id
}
```

---

## Routing Configuration

### Public Route

```text
0.0.0.0/0 → Internet Gateway
```

### Private Route

```text
0.0.0.0/0 → NAT Gateway
```

---

## Deployment Steps

### Initialize Terraform

```bash
terraform init
```

### Validate Configuration

```bash
terraform validate
```

### Review Execution Plan

```bash
terraform plan
```

### Deploy Infrastructure

```bash
terraform apply
```

### Destroy Infrastructure

```bash
terraform destroy
```

---

## Screenshots

Add screenshots after deployment.

### VPC

```text
screenshots/vpc.png
```

### Subnets

```text
screenshots/subnets.png
```

### Route Tables

```text
screenshots/route-tables.png
```

### NAT Gateway

```text
screenshots/nat-gateway.png
```

### Terraform Apply

```text
screenshots/terraform-apply.png
```

---

## Interview Questions Covered

### What is a VPC?

A Virtual Private Cloud (VPC) is a logically isolated network in AWS where resources can be launched securely.

### Why use Public and Private Subnets?

Public subnets allow direct internet access, while private subnets improve security by preventing direct inbound internet traffic.

### Why use a NAT Gateway?

A NAT Gateway enables private resources to access the internet for updates and package downloads without exposing them to inbound internet traffic.

### What is an Internet Gateway?

An Internet Gateway connects a VPC to the public internet.

### What is Infrastructure as Code (IaC)?

Infrastructure as Code allows cloud resources to be managed and provisioned using code instead of manual configuration.

---

## Benefits

* Infrastructure as Code (IaC)
* Secure AWS Networking
* Reusable Terraform Configuration
* Automated Deployment
* Production-Ready Architecture
* Easy Maintenance and Scaling

---

## Technologies Used

* Terraform
* AWS VPC
* AWS Subnets
* AWS Internet Gateway
* AWS NAT Gateway
* AWS Route Tables
* AWS Elastic IP
* AWS CLI

---

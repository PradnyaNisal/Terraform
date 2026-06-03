

## Project Overview

This project demonstrates how to build AWS infrastructure using Terraform Modules.

The infrastructure includes:

* VPC
* Public Subnet
* Private Subnet
* Internet Gateway
* NAT Gateway
* Route Tables
* EC2 Instance
* RDS MySQL Database

The project follows a modular architecture, making the code reusable, maintainable, and scalable.

---

# Project Structure

```text
terraform-project/
│
├── provider.tf
├── main.tf
│
└── modules/
    │
    ├── vpc/
    │   └── main.tf
    │
    ├── subnet/
    │   └── main.tf
    │
    ├── igw/
    │   └── main.tf
    │
    ├── natgateway/
    │   └── main.tf
    │
    ├── routetable/
    │   └── main.tf
    │
    ├── ec2/
    │   └── main.tf
    │
    └── rds/
        └── main.tf
```

---

# 1. provider.tf

```terraform
provider "aws" {
  region = "us-east-1"
}
```

## What is it?

The provider block tells Terraform which cloud platform to interact with.

## Why Used?

* Connects Terraform with AWS.
* Downloads AWS provider plugins.
* Enables resource creation inside AWS.

## Benefits

* Multi-cloud support.
* Easy integration with AWS services.
* Standardized infrastructure management.

---

# 2. VPC Module

```terraform
resource "aws_vpc" "this" {
  cidr_block = var.vpc_cidr
}
```

## What is VPC?

A Virtual Private Cloud is a logically isolated network in AWS.

## Why Used?

* Provides network isolation.
* Defines IP ranges.
* Hosts all AWS resources.

## Benefits

* Secure environment.
* Full network control.
* Scalable architecture.

---

# 3. Subnet Module

```terraform
resource "aws_subnet" "public"
resource "aws_subnet" "private"
```

## What is a Subnet?

A subnet divides a VPC into smaller networks.

## Why Used?

### Public Subnet

Used for:

* Load Balancers
* Bastion Hosts
* Public EC2 Instances

### Private Subnet

Used for:

* Application Servers
* Databases
* Internal Services

## Benefits

* Better security.
* Network segmentation.
* Controlled access.

---

# 4. Internet Gateway Module

```terraform
resource "aws_internet_gateway" "igw"
```

## What is IGW?

Internet Gateway provides internet connectivity to a VPC.

## Why Used?

* Allows communication with the internet.
* Required for public subnet resources.

## Benefits

* Public accessibility.
* Outbound internet access.

---

# 5. NAT Gateway Module

```terraform
resource "aws_nat_gateway" "nat"
```

## What is NAT Gateway?

A NAT Gateway enables private subnet resources to access the internet.

## Why Used?

Private servers need internet access for:

* OS updates
* Package installations
* Security patches

without exposing them publicly.

## Benefits

* Improved security.
* Controlled internet access.
* Production-ready architecture.

---

# 6. Route Table Module

```terraform
resource "aws_route_table" "public"
resource "aws_route_table" "private"
```

## What is a Route Table?

A route table controls network traffic.

## Why Used?

### Public Route

```text
0.0.0.0/0 → Internet Gateway
```

### Private Route

```text
0.0.0.0/0 → NAT Gateway
```

## Benefits

* Proper traffic routing.
* Network isolation.
* Secure communication.

---

# 7. EC2 Module

```terraform
resource "aws_instance" "ec2"
```

## What is EC2?

Elastic Compute Cloud (EC2) is a virtual server in AWS.

## Why Used?

* Hosting applications.
* Running web servers.
* Running backend services.

## Benefits

* Flexible compute.
* Pay-as-you-go pricing.
* Auto Scaling support.

---

# 8. RDS Module

```terraform
resource "aws_db_instance" "db"
```

## What is RDS?

Amazon Relational Database Service (RDS) is a managed database service.

## Why Used?

* Store application data.
* Eliminate manual database management.

## Benefits

* Automated backups.
* High availability.
* Security updates.
* Monitoring.

---

# Module Communication Flow

```text
Provider
   |
   v
VPC
   |
   v
Subnets
   |
   +----------------+
   |                |
   v                v

IGW            NAT Gateway
   |                |
   +--------+-------+
            |
            v

      Route Tables
            |
            |
     +------+------+
     |             |
     v             v

   EC2           RDS
```

---

# Why Use Terraform Modules?

Without Modules:

* Large codebase
* Difficult maintenance
* Duplicate code

With Modules:

* Reusable code
* Better organization
* Easy maintenance
* Team collaboration
* Scalable infrastructure

---

# Advantages of This Architecture

✅ Infrastructure as Code (IaC)

✅ Modular Design

✅ Reusable Components

✅ Secure Public/Private Network

✅ Database Isolation

✅ Production Ready

✅ Easy Scaling

✅ AWS Best Practices

---

# Technologies Used

* Terraform
* AWS VPC
* AWS Subnet
* AWS Internet Gateway
* AWS NAT Gateway
* AWS Route Tables
* AWS EC2
* AWS RDS MySQL
* Infrastructure as Code (IaC)

---

# Interview Questions Covered

### What is a Terraform Module?

A module is a container for multiple Terraform resources used together as a reusable component.

### Why use NAT Gateway?

To allow private resources to access the internet without exposing them publicly.

### Difference between Public and Private Subnet?

Public Subnet has direct internet access through an Internet Gateway, while a Private Subnet accesses the internet through a NAT Gateway.

### Why use RDS instead of installing MySQL on EC2?

RDS provides automated backups, monitoring, patching, scalability, and high availability.

### Benefits of Modular Terraform?

* Code Reusability
* Maintainability
* Scalability
* Better Collaboration

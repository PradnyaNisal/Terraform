# Terraform AWS VPC Infrastructure

## Project Overview

This Terraform project creates a basic AWS networking infrastructure consisting of:

* Custom VPC
* Public Subnet
* Private Subnet
* Internet Gateway
* Elastic IP
* NAT Gateway
* Public Route Table
* Private Route Table
* Route Table Associations

The architecture allows public resources to access the internet directly while private resources access the internet securely through a NAT Gateway.

---

## Resources Used

### 1. VPC (Virtual Private Cloud)

```terraform
aws_vpc
```

**Purpose:**
Creates an isolated virtual network inside AWS.

**Why Used:**

* Provides a secure networking environment.
* Allows complete control over IP addressing and routing.
* Acts as the foundation for all AWS resources.

---

### 2. Public Subnet

```terraform
aws_subnet
```

**Purpose:**
Creates a subnet that can communicate directly with the internet.

**Why Used:**

* Hosts internet-facing resources.
* Suitable for Load Balancers, Bastion Hosts, and NAT Gateways.

---

### 3. Private Subnet

```terraform
aws_subnet
```

**Purpose:**
Creates a subnet that is not directly accessible from the internet.

**Why Used:**

* Hosts application servers and databases securely.
* Improves security by restricting direct internet access.

---

### 4. Internet Gateway (IGW)

```terraform
aws_internet_gateway
```

**Purpose:**
Provides internet connectivity to resources inside the VPC.

**Why Used:**

* Enables communication between the VPC and the internet.
* Required for public subnets.

---

### 5. Elastic IP (EIP)

```terraform
aws_eip
```

**Purpose:**
Allocates a static public IP address.

**Why Used:**

* Provides a permanent public IP.
* Required by the NAT Gateway.

---

### 6. NAT Gateway

```terraform
aws_nat_gateway
```

**Purpose:**
Allows private subnet resources to access the internet.

**Why Used:**

* Enables software updates and package downloads.
* Prevents inbound internet connections to private resources.

---

### 7. Public Route Table

```terraform
aws_route_table
```

**Purpose:**
Defines routing rules for the public subnet.

**Why Used:**

* Routes internet traffic through the Internet Gateway.

Route:

```text
0.0.0.0/0 → Internet Gateway
```

---

### 8. Private Route Table

```terraform
aws_route_table
```

**Purpose:**
Defines routing rules for the private subnet.

**Why Used:**

* Routes outbound traffic through the NAT Gateway.

Route:

```text
0.0.0.0/0 → NAT Gateway
```

---

### 9. Route Table Associations

```terraform
aws_route_table_association
```

**Purpose:**
Associates subnets with route tables.

**Why Used:**

* Connects the public subnet to the public route table.
* Connects the private subnet to the private route table.

---

## Architecture Diagram

Internet
│
▼
Internet Gateway
│
▼
VPC (10.0.0.0/16)
├── Public Subnet (10.0.1.0/24)
│ └── NAT Gateway
│
└── Private Subnet (10.0.2.0/24)
└── Private Resources

Public Subnet → Internet Gateway → Internet

Private Subnet → NAT Gateway → Internet

---

## Benefits

* Secure network architecture.
* Separation of public and private resources.
* Controlled internet access.
* Infrastructure as Code (IaC) using Terraform.
* Easy deployment and management.

## Technologies Used

* Terraform
* AWS VPC
* AWS Subnets
* AWS Internet Gateway
* AWS NAT Gateway
* AWS Route Tables
* AWS Elastic IP

provider "aws" {
  region = "us-east-1"
}

# ---------------- VPC ----------------

resource "aws_vpc" "home_vpc" {
  cidr_block = "10.0.0.0/16"

  tags = {
    Name = "home-vpc"
  }
}

## Terraform file
# ---------------- Public Subnet ----------------

resource "aws_subnet" "public_subnet" {
  vpc_id                  = aws_vpc.home_vpc.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "us-east-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-subnet"
  }
}

# ---------------- Private Subnet ----------------

resource "aws_subnet" "private_subnet" {
  vpc_id            = aws_vpc.home_vpc.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "us-east-1b"

  tags = {
    Name = "private-subnet"
  }
}

# ---------------- Internet Gateway ----------------

resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.home_vpc.id

  tags = {
    Name = "home-igw"
  }
}

# ---------------- Elastic IP ----------------

resource "aws_eip" "nat_eip" {
  domain = "vpc"

  tags = {
    Name = "nat-eip"
  }
}

# ---------------- NAT Gateway ----------------

resource "aws_nat_gateway" "nat" {
  allocation_id = aws_eip.nat_eip.id
  subnet_id     = aws_subnet.public_subnet.id

  tags = {
    Name = "home-nat"
  }

  depends_on = [aws_internet_gateway.igw]
}

# ---------------- Public Route Table ----------------

resource "aws_route_table" "public_rt" {
  vpc_id = aws_vpc.home_vpc.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "public-route-table"
  }
}

# ---------------- Private Route Table ----------------

resource "aws_route_table" "private_rt" {
  vpc_id = aws_vpc.home_vpc.id

  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.nat.id
  }

  tags = {
    Name = "private-route-table"
  }
}

# ---------------- Public Route Table Association ----------------

resource "aws_route_table_association" "public_association" {
  subnet_id      = aws_subnet.public_subnet.id
  route_table_id = aws_route_table.public_rt.id
}

# ---------------- Private Route Table Association ----------------

resource "aws_route_table_association" "private_association" {
  subnet_id      = aws_subnet.private_subnet.id
  route_table_id = aws_route_table.private_rt.id
}

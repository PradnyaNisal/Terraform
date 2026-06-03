# Terraform AWS ALB + Auto Scaling Group + Path-Based Routing

## Project Overview

This project provisions a highly available web infrastructure on AWS using Terraform.

The infrastructure contains:

* Application Load Balancer (ALB)
* 2 Launch Templates
* 2 Auto Scaling Groups (ASG)
* 2 CloudWatch Alarms
* 2 Target Groups
* Listener and Listener Rules
* Path-Based Routing

### Architecture

```
Internet
    |
    v
+------------------+
| Application LB   |
+------------------+
       |
       +--------------------+
       |                    |
       v                    v

Path: /               Path: /cloth/*

Home Target Group     Cloth Target Group
       |                    |
       v                    v

Home ASG             Cloth ASG
       |                    |
       v                    v

EC2 Instance         EC2 Instance
Nginx Home Page      Nginx Cloth Page
```

---

## Launch Template

Launch Templates define how EC2 instances are created.

### Home Launch Template

```hcl
resource "aws_launch_template" "home-temp" {
  name          = "home-temp"
  image_id      = "ami-091138d0f0d41ff90"
  instance_type = "t3.micro"
  key_name      = "B69US"

  user_data = base64encode(<<-EOF
    #!/bin/bash
    apt update -y
    apt install nginx -y

    mkdir -p /var/www/html/home

    echo "Welcome to Home Page" > /var/www/html/home/index.html

    systemctl start nginx
    systemctl enable nginx
  EOF
  )
}
```

### Why Launch Template?

Benefits:

* Reusable server configuration
* Standardized deployments
* Faster provisioning
* Supports Auto Scaling

---

## Auto Scaling Group

Auto Scaling Group manages EC2 instances automatically.

```hcl
resource "aws_autoscaling_group" "home-asg" {

  name               = "home-asg"

  availability_zones = [
    "us-east-1a",
    "us-east-1b",
    "us-east-1c"
  ]

  desired_capacity = 1
  min_size         = 1
  max_size         = 1

  launch_template {
    id      = aws_launch_template.home-temp.id
    version = "$Latest"
  }
}
```

### Why Auto Scaling?

Benefits:

* High Availability
* Self-Healing
* Automatic Recovery
* Reduced Downtime

---

## Auto Scaling Policy

Defines scaling behavior.

```hcl
resource "aws_autoscaling_policy" "home-policy" {

  name                   = "home-policy"
  autoscaling_group_name = aws_autoscaling_group.home-asg.name

  adjustment_type  = "ChangeInCapacity"
  scaling_adjustment = -1
  cooldown         = 120
}
```

### Why Scaling Policies?

Benefits:

* Cost Optimization
* Automatic Resource Management
* Improved Performance

---

## CloudWatch Alarm

Monitors CPU usage.

```hcl
resource "aws_cloudwatch_metric_alarm" "home-alarm" {

  alarm_name          = "home-alarm"
  metric_name         = "CPUUtilization"
  namespace           = "AWS/EC2"

  threshold           = 20
  comparison_operator = "LessThanOrEqualToThreshold"

  evaluation_periods  = 5
  period              = 30
  statistic           = "Average"

  alarm_actions = [
    aws_autoscaling_policy.home-policy.arn
  ]
}
```

### Why CloudWatch?

Benefits:

* Real-time Monitoring
* Alerting
* Automated Scaling
* Performance Visibility

---

## Target Group

Receives traffic from the Load Balancer.

```hcl
resource "aws_lb_target_group" "home-tg" {

  name     = "home-tg"
  port     = 80
  protocol = "HTTP"

  vpc_id = "your-vpc-id"

  health_check {
    path     = "/"
    protocol = "HTTP"
  }
}
```

### Why Target Groups?

Benefits:

* Health Monitoring
* Traffic Distribution
* Fault Isolation

---

## Application Load Balancer

Distributes incoming traffic.

```hcl
resource "aws_lb" "alb" {

  name               = "alb"
  internal           = false

  load_balancer_type = "application"

  subnets = [
    "subnet-1",
    "subnet-2"
  ]

  security_groups = [
    "sg-id"
  ]
}
```

### Why ALB?

Benefits:

* High Availability
* Scalability
* Fault Tolerance
* Layer 7 Routing

---

## Listener

Receives requests on Port 80.

```hcl
resource "aws_lb_listener" "alb-listener" {

  load_balancer_arn = aws_lb.alb.arn

  port     = 80
  protocol = "HTTP"

  default_action {

    type = "forward"

    target_group_arn = aws_lb_target_group.home-tg.arn
  }
}
```

### Why Listener?

Benefits:

* Traffic Entry Point
* Request Processing
* Request Routing

---

## Listener Rule

Implements path-based routing.

```hcl
resource "aws_lb_listener_rule" "cloth-rule" {

  listener_arn = aws_lb_listener.alb-listener.arn

  priority = 1

  action {

    type = "forward"

    target_group_arn = aws_lb_target_group.cloth-tg.arn
  }

  condition {

    path_pattern {
      values = ["/cloth/*"]
    }
  }
}
```

### Why Listener Rules?

Benefits:

* Host Multiple Applications
* Cost Savings
* Efficient Traffic Routing

---

## Terraform Commands

Initialize:

```bash
terraform init
```

Validate:

```bash
terraform validate
```

Plan:

```bash
terraform plan
```

Deploy:

```bash
terraform apply
```

Destroy:

```bash
terraform destroy
```

---

## Technologies Used

* Terraform
* AWS EC2
* Auto Scaling Group
* CloudWatch
* Application Load Balancer
* IAM
* VPC
* Nginx

---




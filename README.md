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

## Terraform file

provider "aws" {
    region = "us-east-1"
}

## launch_templates
resource "aws_launch_template" "home-temp" {
    name = "home-temp"
    image_id = "ami-091138d0f0d41ff90"
    instance_type = "t3.micro"
    key_name = "B69US"
    user_data = base64encode(<<-EOF
        #!/bin/bash
        sudo apt update -y
        sudo apt install nginx -y
        echo "welcome to homepage" > /var/www/html/home/index.html
        systemctl start nginx
        systemctl enable nginx
    EOF
    )
    tags = {
        Name = "home-temp"
    }
}   

resource "aws_launch_template" "cloth-temp" {
    name = "cloth-temp"
    image_id = "ami-091138d0f0d41ff90"
    instance_type = "t3.micro"
    key_name = "B69US"
    user_data = base64encode(<<-EOF
        #!/bin/bash
        sudo apt update -y
        sudo apt install nginx -y
        mkdir -p /var/www/html/cloth
        echo "SALE!!! SALE!!! SALE!!!" > /var/www/html/cloth/index.html
        systemctl start nginx
        systemctl enable nginx
    EOF
    )
    tags = {
        Name = "cloth-temp"
    }
}

##  Auto_scaling_group

resource "aws_autoscaling_group" "home-asg" {
    name = "home-asg"
    availability_zones = ["us-east-1a", "us-east-1b", "aus-east-1c"]
    desired_capacity = 1
    max_size = 1
    min_size = 1
    health_check_type = "EC2"

    launch_template {
        id = aws_launch_template.home-temp.id 
        version = "$Latest"
    }

}

resource "aws_autoscaling_group" "cloth-asg" {
    name = "cloth-asg"
    availability_zones = ["us-east-1a", "us-east-1b", "aus-east-1c"]
    desired_capacity = 1
    max_size = 1
    min_size = 1
    health_check_type = "EC2"

    launch_template {
        id = aws_launch_template.cloth-temp.id 
        version = "$Latest"
    }

}

## ASG_policy

resource "aws_autoscaling_policy" "home-policy" {
    name = "home-policy"
    autoscaling_group_name = aws_autoscaling_group.home-asg.name
    adjustment_type = "ChangeInCapacity"
    scaling_adjustment = -1
    cooldown = 120
}
resource "aws_autoscaling_policy" "cloth-policy" {
    name = "cloth-policy"
    autoscaling_group_name = aws_autoscaling_group.cloth-asg.name
    adjustment_type = "ChangeInCapacity"
    scaling_adjustment = -1
    cooldown = 120
}

## Cloudwatch_Alarms

resource "aws_cloudwatch_metric_alarm" "home-alarm" {
    alarm_description = "this is alarm for home-asg"
    alarm_actions = [aws_autoscaling_policy.home-policy.arn]
    alarm_name = "home-alarm"
    comparison_operator = "LessThanOrEqualToThreshold"
    namespace = "AWS/EC2"
    metric_name = "CPUUtilization"
    threshold = "20"
    evaluation_periods = "5"
    period = "30"
    statistic = "Average"

    dimensions = {
        AutoScalingGroupName = aws_autoscaling_group.home-asg.name
    }
}

resource "aws_cloudwatch_metric_alarm" "cloth-alarm" {
    alarm_description = "this is alarm for cloth-asg"
    alarm_actions = [aws_autoscaling_policy.cloth-policy.arn]
    alarm_name = "cloth-alarm"
    comparison_operator = "LessThanOrEqualToThreshold"
    namespace = "AWS/EC2"
    metric_name = "CPUUtilization"
    threshold = "20"
    evaluation_periods = "5"
    period = "30"
    statistic = "Average"

    dimensions = {
        AutoScalingGroupName = aws_autoscaling_group.cloth-asg.name
    }
}

## Target_group
 resource "aws_lb_target_group" "home-tg" {
    name = "home-tg"
    port = 80
    vpc_id = "vpc-09c185e519425ab8c"
 health_check {
  path                = "/"
  port                = 80
  protocol            = "HTTP"
  matcher             = "200"
  interval            = 30
  timeout             = 5
  healthy_threshold   = 2
  unhealthy_threshold = 2
}
 }

  resource "aws_lb_target_group" "cloth-tg" {
    name = "cloth-tg"
    protocol  = "HTTP"
    vpc_id = "vpc-09c185e519425ab8c"
  health_check {

  path                = "/"
  port                = 80
  protocol            = "HTTP"
  matcher             = "200"
  interval            = 30
  timeout             = 5
  healthy_threshold   = 2
  unhealthy_threshold = 2
}
 }

 ## ASG_group_attachments

resource "aws_autoscaling_attachment" "home-attach" {
  autoscaling_group_name = aws_autoscaling_group.home-asg.id
  lb_target_group_arn    = aws_lb_target_group.home-tg.arn
}

resource "aws_autoscaling_attachment" "cloth-attach" {
  autoscaling_group_name = aws_autoscaling_group.cloth-asg.id
  lb_target_group_arn    = aws_lb_target_group.cloth-tg.arn
}

 ## ALB

 resource "aws_lb" "alb" {
    name = "alb"
    internal = false
    load_balancer_type = "application"
    subnets = ["subnet-03db9212073029c65", "subnet-027bbf0de97b20720"]
    security_groups = ["sg-0588494174e49935e"]

 }

 ## Listner and Rules

 resource "aws_lb_listener" "alb-listener" {
    load_balancer_arn = aws_lb.alb.arn
    port = "80"
    protocol = "HTTP"

    default_action {
        type = "forward"
        target_group_arn = aws_lb_target_group.home-tg.arn
    }
 }

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


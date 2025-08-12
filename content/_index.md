---
title : "Auto Scaling with ML Predictive Scaling"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---

# Building Auto Scaling Groups & ML Predictive Scaling

### Overall
In this lab you’ll build a **production-style baseline** for **ML Predictive Scaling**:
- **VPC** with **public** (ALB/NAT) and **private** (ASG) subnets
- **Application Load Balancer (ALB)** in public subnets
- **EC2 Auto Scaling Group (ASG)** running app instances in **private** subnets
- **Zero SSH/RDP**: admin via **AWS Systems Manager – Session Manager**
- **Auditable session logs** streamed privately to **Amazon S3**

You’ll use this baseline to later enable **Predictive Scaling**, so capacity scales **before** traffic spikes.

![ConnectPrivate](/images/arc-log.png) 

### Content
1. [Introduction](1-introduce/)  
2. [Preparation (VPC, ALB, ASG, SSM)](2-prerequiste/)  
3. [Connect to EC2 Instances (via Session Manager)](3-accessibilitytoinstances/)  
4. [Manage Session Logs (S3, private egress)](4-s3log/)  
5. [Port Forwarding (RDP over SSM)](5-Portfwd/)  
6. [Clean up Resources](6-cleanup/)

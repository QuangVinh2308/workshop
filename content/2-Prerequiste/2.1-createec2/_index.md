---
title : "Preparing VPC and EC2"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

| title                           | date            | weight | chapter | pre          |
|---------------------------------|-----------------|--------|---------|--------------|
| Prepare VPC & Auto Scaling Baseline | `r Sys.Date()` | 1      | false   | <b> 2.1 </b> |

In this step, you will build the **network & compute baseline** for the Predictive Scaling workshop:

- A VPC with **two public subnets** and **two private subnets**.
- An **Application Load Balancer (ALB)** in the public subnets.
- An **EC2 Auto Scaling Group (ASG)** of **Linux** application instances in the **private** subnets.
- **AWS Systems Manager (Session Manager)** access (via VPC endpoints) — **no SSH/22, no bastion host**.
- *(Optional)* A **Windows** instance in a private subnet only if you need a demo client or RDP test box.

This baseline allows us to enable **ML Predictive Scaling** later, so capacity scales **proactively** before traffic spikes and scales in to save cost when idle.

**Architecture after completing this step:**

![VPC](/images/arc-01.png)

To learn how to create EC2 and VPC with public/private subnets, refer to:
- [About Amazon EC2](https://000004.awsstudygroup.com/en/)
- [Works with Amazon VPC](https://000003.awsstudygroup.com/en/)

### Content
- [Create VPC](2.1.1-createvpc/)
- [Create Public Subnets (for ALB/NAT)](2.1.2-createpublicsubnet/)
- [Create Private Subnets (for ASG)](2.1.3-createprivatesubnet/)
- [Create Security Groups (ALB ↔ App)](2.1.4-createsecgroup/)
- [Create Launch Template](2.1.5-createlaunchtemplate/)
- [Create Auto Scaling Group behind ALB](2.1.6-createasg/)
- *(Optional)* [Create VPC Endpoints for SSM](2.1.7-create-ssm-endpoints/)

---
title : "Connect to EC2 Instances (via Session Manager)"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

To manage both **public** and **private** instances **without** opening SSH (22) or RDP (3389), we’ll use **AWS Systems Manager — Session Manager**.  
This fits our Predictive Scaling baseline: private ASG instances stay isolated, access is audited, and warm-ups stay smooth.

![SSM Overview](/images/3.connect/000-endpoints-overview.png)

### Content
3.1. [Connect to App Instance (via Session Manager)](3.1-ssm-public-or-app/)  
3.2. [Connect to Private Instance (via Session Manager)](3.2-connect-private/)  
  3.2.1. [Enable DNS hostnames](3.2.1-enablevpcdns/)  
  3.2.2. [Create VPC Endpoints for SSM](3.2.2-vpc-endpoints/)

---
title : "Create Launch Template (App Instances for ASG)"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 2.1.5 </b> "
---

In this step, you will create a **Launch Template** for application instances that will run in **private subnets** under an **Auto Scaling Group (ASG)**.  
We do **not** use SSH/RDP. Access and automation go through **AWS Systems Manager (Session Manager)**.

1. Go to [EC2 Console](https://console.aws.amazon.com/ec2/)
   + **Launch Templates** → **Create launch template**.

![EC2](/images/2.prerequisite/027-create-lt.png)

2. Basic configuration
   + **Launch template name**: `lt-ps-app`
   + **Template version description**: `v1`
   + **AMI**: **Amazon Linux 2023 (AL2023)**
   + **Instance type**: `t3.micro` (example)
   + **Key pair**: **Proceed without a key pair** (we use SSM)

![EC2](/images/2.prerequisite/027-create-lt.png)

3. Network settings
   + Do **not** specify **Subnet** (the ASG will pick **private subnets**).
   + **Auto-assign public IP**: **Disabled**
   + **Security groups**: select **`sg-app-asg`**

![EC2](/images/2.prerequisite/028-lt-network.png)

4. IAM role / Instance profile
   + **IAM instance profile**: `ps-ec2-ssm-role` (must include **AmazonSSMManagedInstanceCore**)
   + *(Optional)* add CloudWatch Agent permissions if you plan to ship custom metrics/logs.

![EC2](/images/2.prerequisite/029-lt-iam.png)

5. Storage & tags
   + Root volume: keep defaults (e.g., `gp3`, `8 GiB`) or adjust to your app.
   + Add tags: `Project=PS-Workshop`, `Role=App`, `ManagedBy=ASG`

![EC2](/images/2.prerequisite/030-lt-storage.png)

6. User data (bootstrapping)
   Paste the following script to install a simple HTTP page for ALB health checks:
   ```bash
   #!/bin/bash
   dnf -y update
   dnf -y install httpd
   echo "Hello from $(hostname)" > /var/www/html/index.html
   systemctl enable --now httpd

---
title : "Create IAM Role for SSM (EC2)"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

In this step, you will create an **IAM role** that lets your EC2 instances connect to **AWS Systems Manager (SSM)**.  
We avoid opening **SSH (22)** or **RDP (3389)**. All administration goes through **Session Manager**.

> Result: an instance profile **`ps-ec2-ssm-role`** attached to your Launch Template **`lt-ps-app`**, so all ASG instances are SSM-managed by default.

---

### A) Create the IAM Role

1. Open **IAM → Roles → Create role**  
   - **Trusted entity type**: **AWS service**  
   - **Use case**: **EC2** → **Next**

![IAM](/images/2.prerequisite/044-iam-create-role.png)

2. Attach policies (least-privilege for SSM)
   - **AmazonSSMManagedInstanceCore** *(required)*
   - **CloudWatchAgentServerPolicy** *(optional for metrics/logs)*  
   - **Next**

![IAM](/images/2.prerequisite/045-iam-attach-policies.png)

3. Name and create the role
   - **Role name**: `ps-ec2-ssm-role`  
   - (Optional) Tags: `Project=PS-Workshop`, `Role=App`  
   - **Create role**

![IAM](/images/2.prerequisite/046-iam-name-role.png)

4. Verify **Instance profile**
   - Open the role `ps-ec2-ssm-role` → check **Instance profile** tab  
   - You should see an instance pro

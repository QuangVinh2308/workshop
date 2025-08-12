+++
title = "Clean up resources"
date = 2025
weight = 6
chapter = false
pre = "<b>6. </b>"
+++

We’ll remove the workshop resources in a **safe order** so there are no dependency errors.

---

#### 1) Delete Auto Scaling resources

1. EC2 → **Auto Scaling Groups**  
   - Open **`asg-ps-app`** → set **Desired capacity = 0** and wait for instances to terminate.  
   - Then **Delete** the ASG.

2. (Optional) EC2 → **Launch Templates**  
   - Delete **`lt-ps-app`** after the ASG is gone.

![Clean](/images/6.clean/001-clean.png)

---

#### 2) Delete ALB and Target Group

1. EC2 → **Load Balancers** → select **`alb-ps`** → **Delete** (ensure no listeners/attachments).  
2. EC2 → **Target Groups** → select **`tg-ps-app`** → **Delete**.

![Clean](/images/6.clean/002-clean.png)

---

#### 3) Disable Session logs and delete S3 bucket

1. Systems Manager → **Session Manager → Preferences**  
   - Edit **S3 logging** → **Disable** → **Save**.  
2. S3 → open your bucket (e.g., `ps-ssm-logs-yourname-1234`)  
   - **Empty** the bucket → then **Delete bucket**.

![Clean](/images/6.clean/003-clean.png)

---

#### 4) Delete VPC Endpoints

VPC → **Endpoints** → select and **Delete**:
- **Interface**: `com.amazonaws.<region>.ssm`, `ssmmessages`, `ec2messages`  
- **Gateway**: `com.amazonaws.<region>.s3`

![Clean](/images/6.clean/004-clean.png)

---

#### 5) Tear down networking pieces

1. **NAT Gateway**: delete NAT (VPC → NAT Gateways), then **release** its **Elastic IP** (EC2 → Elastic IPs).  
2. **Internet Gateway**: **detach** from VPC → **delete** IGW.  
3. **Route tables**: delete custom **`rtb-public`** / **`rtb-private`** (after disassociating subnets).  
4. **Subnets**: delete **`ps-public-a/b`** and **`ps-private-a/b`** (ensure no ENIs or resources remain).

![Clean](/images/6.clean/005-clean.png)

---

#### 6) Delete VPC & IAM artifacts

1. **VPC**: delete **`workshop-ps-vpc`** (when all dependencies are removed).  
2. **IAM Role**: delete **`ps-ec2-ssm-role`** (not in use by any LT/instance).  
3. *(Optional lab)* **IAM User**: delete **`portfwd`** if you created it (prod nên dùng IAM Identity Center).

![Clean](/images/6.clean/006-clean.png)

---
title : "Create Public Subnet"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.1.2 </b> "
---

#### Create Public Subnets (for ALB/NAT)

> Goal: two **public** subnets across **two AZs** to host the **Application Load Balancer (ALB)** and **NAT Gateway**. App instances will live in **private** subnets for security & cost control (needed later for **ML Predictive Scaling**).

1. Open **VPC → Subnets**  
   - Click **Create subnet**.

![VPC](/images/2.prerequisite/003-createsubnet.png)

2. On **Create subnet**:
   - **VPC ID**: select **workshop-ps-vpc** (your VPC).
   - **Subnet name**: `ps-public-a`  
   - **Availability Zone**: choose the **first** AZ (e.g., `ap-southeast-1a`).  
   - **IPv4 CIDR block**: `10.0.0.0/24`  
   - Click **Add new subnet** to create the second subnet:
     - **Subnet name**: `ps-public-b`  
     - **Availability Zone**: the **second** AZ (e.g., `ap-southeast-1b`)  
     - **IPv4 CIDR block**: `10.0.1.0/24`  
   - Click **Create subnet**.

![VPC](/images/2.prerequisite/004-createsubnet.png)

3. Enable public IP assignment (for ALB/NAT only):
   - Open subnet **ps-public-a** → **Actions → Edit subnet settings**.
   - **Enable auto-assign public IPv4 address** → **Save**.
   - Repeat for **ps-public-b**.

![VPC](/images/2.prerequisite/005-createsubnet.png)
![VPC](/images/2.prerequisite/006-createsubnet.png)

4. Create an **Internet Gateway (IGW)**:
   - **Internet Gateways → Create internet gateway**.

![VPC](/images/2.prerequisite/007-createigw.png)

5. On **Create internet gateway**:
   - **Name tag**: `ps-igw`
   - Click **Create internet gateway**.

![VPC](/images/2.prerequisite/008-createigw.png)

6. Attach IGW to your VPC:
   - **Actions → Attach to VPC**  
   - **Available VPCs**: choose **workshop-ps-vpc** → **Attach internet gateway**.  
   - Confirm status shows **attached**.

![VPC](/images/2.prerequisite/009-createigw.png)
![VPC](/images/2.prerequisite/010-createigw.png)

7. Create a **public route table** and route 0.0.0.0/0 → IGW:
   - **Route Tables → Create route table**.

![VPC](/images/2.prerequisite/011-creatertb.png)

8. On **Create route table**:
   - **Name**: `rtb-public`
   - **VPC**: **workshop-ps-vpc**
   - **Create route table** → open it → **Edit routes**.

![VPC](/images/2.prerequisite/012-creatertb.png)

9. On **Edit routes**:
   - **Add route** → **Destination**: `0.0.0.0/0`  
   - **Target**: **Internet Gateway** → select **ps-igw** → **Save changes**.

![VPC](/images/2.prerequisite/013-creatertb.png)

10. Associate both public subnets:
   - **Subnet associations** tab → **Edit subnet associations**.  
   - Tick **ps-public-a** và **ps-public-b** → **Save associations**.

![VPC](/images/2.prerequisite/014-creatertb.png)
![VPC](/images/2.prerequisite/015-creatertb.png)

11. Validate
   - `rtb-public` shows **0.0.0.0/0 → ps-igw**.
   - Both **ps-public-a/b** are associated to `rtb-public`.
   - Both public subnets have **Auto-assign public IPv4: Yes**.

![VPC](/images/2.prerequisite/016-creatertb.png)

> Why it matters for Predictive Scaling: ALB sits in these **public** subnets and forwards traffic to an **Auto Scaling Group** in **private** subnets. Predictive Scaling will later forecast demand and scale the ASG **proactively** while keeping your app isolated from the internet.

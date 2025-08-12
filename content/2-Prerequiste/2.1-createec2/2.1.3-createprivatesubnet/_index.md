---
title : "Create Private Subnets (for ASG)"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 2.1.3 </b> "
---

#### Create Private Subnets (for ASG)

> Goal: create **two private subnets** across **two AZs** for the **EC2 Auto Scaling Group (ASG)**.  
> These subnets **must not** auto-assign public IPs. Outbound Internet will go via **NAT Gateway** (created below) for package updates and **SSM** access — no SSH/22, no bastion.

1. Go to **VPC → Subnets**  
   + Click **Create subnet**.

![VPC](/images/2.prerequisite/017-createsubnet.png)

2. On **Create subnet**:
   + **VPC ID**: select **workshop-ps-vpc**.
   + **Subnet name**: `ps-private-a`
   + **Availability Zone**: first AZ (e.g., `ap-southeast-1a`)
   + **IPv4 CIDR block**: `10.0.10.0/24`
   + Click **Add new subnet** to add the second private subnet:
     - **Subnet name**: `ps-private-b`
     - **Availability Zone**: second AZ (e.g., `ap-southeast-1b`)
     - **IPv4 CIDR block**: `10.0.11.0/24`
   + Click **Create subnet**.

![VPC](/images/2.prerequisite/018-createsubnet.png)

3. Ensure **auto-assign public IPv4** is **disabled** on both private subnets:
   + Open **ps-private-a** → **Actions → Edit subnet settings** → *Auto-assign public IPv4*: **Disabled** → **Save**.
   + Repeat for **ps-private-b**.

![VPC](/images/2.prerequisite/019-edit-private-subnet.png)
![VPC](/images/2.prerequisite/020-private-disable-publicip.png)

---

### (Required) NAT & Private Route Table

> Private subnets need **egress** for OS updates, SSM, container pulls, etc. We use **NAT Gateway** in a **public** subnet and route `0.0.0.0/0` from private subnets to that NAT.

4. Allocate an Elastic IP & create **NAT Gateway**:
   + **VPC → NAT Gateways → Create NAT gateway**
   + **Name**: `nat-a`
   + **Subnet**: `ps-public-a` (public subnet created in 2.1.2)
   + **Elastic IP allocation ID**: **Allocate new Elastic IP** → select it
   + **Create NAT gateway** and wait until **Available**.

![VPC](/images/2.prerequisite/021-create-nat.png)

5. Create **private route table** and route traffic to NAT:
   + **VPC → Route tables → Create route table**
   + **Name**: `rtb-private`
   + **VPC**: **workshop-ps-vpc** → **Create route table**
   + Open `rtb-private` → **Edit routes** → **Add route**
     - **Destination**: `0.0.0.0/0`
     - **Target**: **NAT gateway** → select **nat-a**
   + **Save changes**.

![VPC](/images/2.prerequisite/022-rtb-private.png)
![VPC](/images/2.prerequisite/023-rtb-private-route.png)

6. Associate both private subnets:
   + In `rtb-private` → **Subnet associations** → **Edit subnet associations**
   + Select **ps-private-a** and **ps-private-b** → **Save associations**.

![VPC](/images/2.prerequisite/024-associate-private-subnets.png)

7. Validate
   + **ps-private-a/b**: *Auto-assign public IPv4* = **No**.
   + **rtb-private**: shows `0.0.0.0/0 → nat-a`.
   + **Reachability (optional)**: From ALB (public subnets), target instances in private subnets should be reachable via their **security groups** only.

![VPC](/images/2.prerequisite/025-rtb-private-verify.png)

> Why it matters for Predictive Scaling: keeping the ASG in **private** subnets isolates the app from the Internet while still letting instances warm up (packages, agents) ahead of predicted surges. This is key to **proactive** scaling with stable latency.

**Next:** [2.1.4 – Create Security Groups (ALB ↔ App)](../2.1.4-createsecgroup/)

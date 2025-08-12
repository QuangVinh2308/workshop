---
title : "Create Security Groups (ALB ↔ App ↔ SSM Endpoints)"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 2.1.4 </b> "
---

#### Create Security Groups

In this step, you will create **least-privilege** security groups for the Predictive Scaling baseline.  
We will **not** open traditional admin ports like **SSH (22)** or **RDP (3389)**. Operations will go through **AWS Systems Manager (Session Manager)**.

---

#### A) Security group for the Application Load Balancer (public)

1) Go to **VPC → Security groups** → **Create security group**  
   - **Security group name**: `sg-alb-public`  
   - **Description**: ALB in public subnets  
   - **VPC**: `workshop-ps-vpc`  
   - Keep default **Outbound** = Allow all → **Create security group**.

![SG](/images/2.prerequisite/019-createsg.png)

2) Add **Inbound rules** to expose ALB to the Internet  
   - **HTTP** `80` from `0.0.0.0/0`  
   - **HTTPS** `443` from `0.0.0.0/0`  
   - **Save rules**.

![SG](/images/2.prerequisite/020-createsg.png)

> The ALB terminates client traffic on 80/443 and forwards to app targets in private subnets.

---

#### B) Security group for App instances (ASG in private subnets)

1) **Create security group**  
   - **Security group name**: `sg-app-asg`  
   - **Description**: App instances behind ALB (Auto Scaling Group)  
   - **VPC**: `workshop-ps-vpc`  
   - Keep **Outbound** = Allow all (needed for OS updates, ECR pulls, and SSM).  
   - **Create security group**.

![SG](/images/2.prerequisite/021-createsg.png)

2) Add **Inbound rule** to accept traffic **only from the ALB SG**  
   - **Type**: HTTP (or your app port) `80`  
   - **Source**: `sg-alb-public` (select by SG ID)  
   - **Save rules**.

![SG](/images/2.prerequisite/022-createsg.png)

{{%notice tip%}}
No inbound **22/3389**. For admin access, use **Session Manager** via VPC Interface Endpoints. This keeps private instances **not exposed** to the Internet — a must for stable, proactive scaling.
{{%/notice%}}

---

#### C) Security group for **VPC Interface Endpoints (SSM)**

We’ll restrict endpoint access to **only** our app instances.

1) **Create security group**  
   - **Security group name**: `sg-ssm-endpoints`  
   - **Description**: SSM VPC endpoints (ssm, ssmmessages, ec2messages)  
   - **VPC**: `workshop-ps-vpc`  
   - **Outbound**: none required → keep default or set to none.

![SG](/images/2.prerequisite/023-createsg.png)

2) Add **Inbound rule** to allow TLS from app instances  
   - **Type**: HTTPS `443`  
   - **Source**: `sg-app-asg` (select by SG ID)  
   - **Save rules**.

![SG](/images/2.prerequisite/024-createsg.png)

---

#### D) Attach SGs to resources

- **ALB (public subnets)** → `sg-alb-public`  
- **ASG Launch Template (private subnets)** → `sg-app-asg`  
- **VPC Interface Endpoints** (`ssm`, `ssmmessages`, `ec2messages`) → `sg-ssm-endpoints`

![SG](/images/2.prerequisite/025-createsg.png)

---

#### Validate

- ✅ No inbound **SSH (22)** or **RDP (3389)** on app instances  
- ✅ ALB reachable on **80/443** from the Internet  
- ✅ App instances accept traffic **only from ALB SG** on app port  
- ✅ SSM VPC endpoints allow **443** from `sg-app-asg` only

![SG](/images/2.prerequisite/026-createsg.png)

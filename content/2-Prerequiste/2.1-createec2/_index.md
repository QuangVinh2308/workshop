---
title : "Create Auto Scaling Group behind ALB"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1 </b> "
---

In this step, you will place your application instances (from **Launch Template `lt-ps-app`**) behind an **Application Load Balancer (ALB)** and manage them with an **Auto Scaling Group (ASG)** in **private subnets**.  
This is the final baseline before we enable **ML Predictive Scaling**.

---

### A) Create the Application Load Balancer (public)
1. EC2 Console → **Load Balancers** → **Create load balancer**
   - **Type**: Application Load Balancer
   - **Name**: `alb-ps`
   - **Scheme**: Internet-facing; **IP address type**: IPv4
   - **VPC**: `workshop-ps-vpc`
   - **Mappings**: select **ps-public-a** and **ps-public-b**

![ALB](/images/2.prerequisite/035-create-alb.png)

2. **Security groups & Listeners**
   - **Security groups**: `sg-alb-public`
   - **Listener**: HTTP :80 → **Forward** to target group `tg-ps-app`
   - *(Optional)* Add HTTPS :443 later when ACM cert is ready.

![ALB](/images/2.prerequisite/036-alb-security.png)
![ALB](/images/2.prerequisite/037-tg-attach.png)

---

### B) Create the Auto Scaling Group (private)
1. EC2 Console → **Auto Scaling Groups** → **Create**
   - **ASG name**: `asg-ps-app`
   - **Launch template**: `lt-ps-app` (v1)
   - **VPC and subnets**: choose **ps-private-a** and **ps-private-b**

![ASG](/images/2.prerequisite/038-create-asg.png)

2. **Load balancing & health checks**
   - Attach to **Application Load Balancer**
   - **Target group**: `tg-ps-app`
   - **Health check type**: **ELB** (recommended)

![ASG](/images/2.prerequisite/039-asg-loadbalancing.png)

3. **Capacity & warm-up**
   - **Desired**: `2` (example)
   - **Minimum**: `2` (keeps both AZs warm)
   - **Maximum**: `6` (example)
   - **Default instance warm-up**: `300` seconds

![ASG](/images/2.prerequisite/040-asg-capacity.png)

4. **Scaling policies (baseline)**
   - Choose **Target tracking scaling policy**
   - **Metric**: `ALB RequestCountPerTarget` (or `CPUUtilization`)
   - **Target value**: e.g., `100` requests/target (adjust later)
   - *(Predictive Scaling will be enabled in the next section.)*

![ASG](/images/2.prerequisite/041-asg-scaling.png)

5. **Review & create ASG**
   - Confirm: private subnets, `tg-ps-app` attached, **ELB health check**, warm-up set.
   - **Create Auto Scaling group**

![ASG](/images/2.prerequisite/042-asg-review.png)

---

### C) Validate
- **Target Groups → `tg-ps-app`**: targets become **healthy (2/2)** after a few minutes.
- **Load Balancers → `alb-ps`**: copy the **DNS name** and open it in a browser; you should see the hello page from user data.
- No inbound **22/3389** on app instances; admin goes via **Session Manager**.

![Validate](/images/2.prerequisite/043-validate-alb.png)

> Why this matters: Having ALB+ASG across two AZs with warm-up configured gives the ML model a stable baseline. Predictive Scaling will then forecast demand and scale **before** traffic spikes to keep latency smooth.

**Next:** We’ll enable **Predictive Scaling** on this ASG and tune metrics/warm-up in the scaling policy.



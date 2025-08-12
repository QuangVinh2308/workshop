---
title : "Create Endpoint ssmmessages"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2.2.2 </b> "
---

#### Create VPC Endpoint **ssmmessages** (Interface)

> Goal: allow Systems Manager **session data channels** to work **privately** from instances in **private subnets** — no Internet egress.

1. Go to [VPC Console](https://console.aws.amazon.com/vpc/home)  
   + **Endpoints** → **Create endpoint**

![Connect](/images/3.connect/017-endpoint-create.png)

2. **Service**  
   + **Service category**: **AWS services**  
   + Search and select **Service name**: `com.amazonaws.<region>.ssmmessages` (e.g., `com.amazonaws.ap-southeast-1.ssmmessages`)  
   + **Type**: **Interface**  
   + **Enable Private DNS**: **Enabled**

![Connect](/images/3.connect/018-endpoint-choose-ssmmessages.png)

3. **VPC & Subnets**  
   + **VPC**: `workshop-ps-vpc`  
   + **Subnets**: select **both private subnets** (`ps-private-a`, `ps-private-b`) across two AZs

![Connect](/images/3.connect/019-endpoint-subnets.png)

4. **Security group**  
   + Attach **`sg-ssm-endpoints`** (from section 2.1.4)  
   + This SG should allow **HTTPS 443** **from** `sg-app-asg` (least privilege)

![Connect](/images/3.connect/020-endpoint-sg.png)

5. **Policy & Create**  
   + **Policy**: **Full access** for the lab (tighten later for prod)  
   + Click **Create endpoint** and wait until **Status = Available**.

![Connect](/images/3.connect/021-endpoint-policy-done.png)

{{% notice tip %}}
For full Session Manager functionality, you need **all three** Interface Endpoints:  
`ssm`, `ssmmessages`, and `ec2messages`. You’ve just created **ssmmessages**.
{{% /notice %}}

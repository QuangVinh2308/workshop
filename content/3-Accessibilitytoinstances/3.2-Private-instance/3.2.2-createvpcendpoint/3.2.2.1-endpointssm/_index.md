---
title : "Create Endpoint SSM"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.2.2.1 </b> "
---

#### Create VPC Endpoint for **SSM** (Interface)

> Goal: allow **Session Manager** to work **privately** from instances in **private subnets** (no Internet egress needed), which fits our **Predictive Scaling** baseline.

1. Go to [VPC Console](https://console.aws.amazon.com/vpc/home)  
   + **Endpoints** → **Create endpoint**

![Connect](/images/3.connect/011-endpoint-create.png)

2. **Service**  
   + **Service category**: **AWS services**  
   + Search and select **Service name**: `com.amazonaws.<region>.ssm` (e.g., `com.amazonaws.ap-southeast-1.ssm`)  
   + **Type**: **Interface**  
   + **Enable Private DNS**: **Enabled**

![Connect](/images/3.connect/012-endpoint-choose-ssm.png)

3. **VPC & Subnets**  
   + **VPC**: `workshop-ps-vpc`  
   + **Subnets**: choose **both private subnets** (`ps-private-a`, `ps-private-b`) across two AZs

![Connect](/images/3.connect/013-endpoint-subnets.png)

4. **Security group**  
   + Select **`sg-ssm-endpoints`** (created in 2.1.4)  
   + This SG should allow **HTTPS 443** **from** `sg-app-asg` only (least privilege)

![Connect](/images/3.connect/014-endpoint-sg.png)

5. **Policy**  
   + For the lab, choose **Full access** (you can restrict later for production)

![Connect](/images/3.connect/015-endpoint-policy.png)

6. Click **Create endpoint** and wait until **Status = Available**.  
   Instances in private subnets can now reach SSM **privately** via this endpoint.

![Connect](/images/3.connect/016-endpoint-done.png)

{{% notice tip %}}
Repeat these steps to create **Interface** endpoints for:

- `com.amazonaws.<region>.ssmmessages`
- `com.amazonaws.<region>.ec2messages`

Keep **Private DNS** enabled and reuse **sg-ssm-endpoints**.
{{% /notice %}}


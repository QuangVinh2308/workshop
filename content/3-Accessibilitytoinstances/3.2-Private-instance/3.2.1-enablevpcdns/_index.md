---
title : "Enable DNS hostnames"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.2.1 </b> "
---

#### Enable DNS hostnames on VPC (required for SSM endpoints & ALB)

1. We need **DNS hostnames** (and **DNS resolution**) enabled on the VPC so private instances can resolve:
   - **VPC Interface Endpoints** for `ssm`, `ssmmessages`, `ec2messages`
   - **ALB DNS name** for health checks and routing

   Go to [VPC console](https://console.aws.amazon.com/vpc/home)
   + **Your VPCs** → select **`workshop-ps-vpc`**
   + **Actions** → **Edit VPC settings** (or **Edit DNS hostnames** on older UI)

![Connect](/images/3.connect/009-connect.png)

2. On the **Edit VPC settings** page:
   + **DNS hostnames**: **Enabled**
   + **DNS resolution**: **Enabled**
   + **Save changes**

![Connect](/images/3.connect/010-connect.png)

{{% notice tip %}}
If these are disabled, instances in **private subnets** won’t resolve SSM/endpoint DNS and **Session Manager** will fail. Enabling DNS also ensures your **ALB** hostname resolves correctly for health checks.
{{% /notice %}}


---
title : "Create VPC Endpoints for Session Manager"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2.2 </b> "
---

![Endpoints Overview](/images/3.connect/000-endpoints-overview.png)

#### Why these endpoints?
To keep **ASG app instances** in **private subnets** fully manageable **without Internet egress**, we create **three Interface Endpoints** for Session Manager:

- `com.amazonaws.<region>.ssm`  
- `com.amazonaws.<region>.ssmmessages`  
- `com.amazonaws.<region>.ec2messages`

With these, admin traffic flows **over TLS (443)** to VPC endpoints — no SSH/22, no bastion, fully auditable.  
Official docs: [Session Manager prerequisites](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-prerequisites.html)

### Content
- [Create Endpoint ssm](./3.2.2.1-endpointssm/)
- [Create Endpoint ssmmessages](./3.2.2.2-endpointssmmessages/)
- [Create Endpoint ec2messages](./3.2.2.3-endpointec2messages/)

{{% notice tip %}}
Attach **`sg-ssm-endpoints`** to all three endpoints, and allow **HTTPS 443** **from** **`sg-app-asg`** only (least privilege).  
Make sure **DNS hostnames & DNS resolution** are enabled on the VPC (see **3.2.1**).
{{% /notice %}}

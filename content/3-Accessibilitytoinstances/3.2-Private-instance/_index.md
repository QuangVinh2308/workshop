---
title : "Connect to Private Instance (via Session Manager)"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 3.2. </b> "
---

For **instances in private subnets** (e.g., a **Windows** node or your **ASG app instances**), there is **no public IP** and **no Internet exposure**.  
Instead of a costly, risky **bastion host**, we use **AWS Systems Manager (Session Manager)** to get shell access **over TLS (443)** — **without** opening **SSH (22)** or **RDP (3389)**.

However, we also **don’t want 443 to the Internet** from private subnets. The solution is to keep SSM traffic **inside the VPC** using **VPC Interface Endpoints**:

![ConnectPrivate](/images/arc-03.png)

With **Interface Endpoints** attached to subnets, this method works for **private** and even **public** subnets — you can block outbound 443 to the Internet entirely and still use Session Manager privately.

### Why this fits Predictive Scaling
- Keeps app/Windows instances **isolated**, so you can **scale proactively** without managing bastions or keys.
- **Auditable access** (CloudTrail/SSM logs) and stable warm-up flows for instances added by the ASG.
- No dependency on public egress: instances can bootstrap and be managed **privately**.

### Content
- [Enable DNS hostnames](./3.2.1-enablevpcdns/)
- [Create VPC Endpoints for SSM](./3.2.2-createvpcendpoint/)
- [Connect Private Instance](./3.3.3-connectec2/)

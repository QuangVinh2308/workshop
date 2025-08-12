---
title : "Port Forwarding (RDP over Session Manager)"
date :  "`r Sys.Date()`" 
weight : 5 
chapter : false
pre : " <b> 5. </b> "
---

{{% notice info %}}
**Port Forwarding** lets you tunnel traffic from your laptop to a port on a **private instance** through **AWS Systems Manager (SSM)** — no SSH (22), no bastion, no public IP.  
In this lab, we’ll RDP to a **Windows instance in private subnets** using SSM.
{{% /notice %}}

![port-fwd](/images/5.fwd/arc-04.png) 

### A) Create a lab IAM user (for CLI) — or use IAM Identity Center in prod

1. Go to [IAM Console](https://console.aws.amazon.com/iamv2/home)
   + **Users** → **Add users**  
   + **User name**: `portfwd` (lab)  
   + **Access key - Programmatic access** → **Next: Permissions**

![FWD](/images/5.fwd/001-fwd.png)

2. Attach **least-privilege** policy (lab):
   - Needed actions: `ssm:StartSession`, `ssm:TerminateSession`, `ssm:DescribeSessions`  
   - (Optional) `ec2:DescribeInstances` to find instance IDs  
   - Scope to your target instance(s) via **ARN** or **tags** (recommended)

![FWD](/images/5.fwd/002-fwd.png)

{{% notice tip %}}
**Prod best practice:** avoid long-lived access keys. Use **IAM Identity Center** (SSO) or short-lived **STS** credentials. If you must use a user, lock it down by **condition keys** (e.g., `aws:ResourceTag/*`) and **MFA**.
{{% /notice %}}

### B) Install CLI & Session Manager Plugin

- Install **AWS CLI** and **Session Manager Plugin**:  
  Plugin docs: https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager-working-with-install-plugin.html  
- Configure credentials: `aws configure` (use **region** of your workshop).  
- On Windows, run **install.bat** as **Administrator** after extracting the plugin.

### C) Start port forwarding (RDP 3389 -> local 9999)

Run on your laptop (PowerShell / CMD / bash):

```bash
aws ssm start-session \
  --target i-xxxxxxxxxxxxxxxxx \
  --document-name AWS-StartPortForwardingSession \
  --parameters portNumber="3389",localPortNumber="9999" \
  --region <your-region>

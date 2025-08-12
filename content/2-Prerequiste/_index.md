---
title : "Create IAM Role for SSM (EC2)"
date : "`r Sys.Date()`"
weight : 2
chapter : false
pre : " <b> 2.2 </b> "
---

In this step, you will create an **IAM role** that lets your EC2 instances connect to **AWS Systems Manager (SSM)**.  
No **SSH (22)** or **RDP (3389)** is required — all administration goes through **Session Manager**.

**Why this matters for Network Compliance & Audit Automation**
- Enables **agent-based checks** and **auto-remediation** via SSM Automation runbooks.  
- Provides an **auditable** control plane for actions taken on instances.  
- Works with **AWS Config** / **Security Hub** to close the loop from finding → fix.

**Result:** an instance profile **`ps-ec2-ssm-role`** attached to your Launch Template **`lt-ps-app`**, so all ASG instances are SSM-managed by default.

---

### A) Create the IAM Role

1. Open **IAM → Roles → Create role**  
   - **Trusted entity type**: **AWS service**  
   - **Use case**: **EC2** → **Next**

![IAM](/images/2.prerequisite/044-iam-create-role.png)

2. Attach policies (least-privilege for SSM)  
   - **AmazonSSMManagedInstanceCore** *(required)*  
   - **CloudWatchAgentServerPolicy** *(optional, for metrics/logs)* → **Next**

![IAM](/images/2.prerequisite/045-iam-attach-policies.png)

3. Name and create the role  
   - **Role name**: `ps-ec2-ssm-role`  
   - (Optional) Tags: `Compliance=Network`, `Owner=SecOps`, `Env=Dev/Prod`  
   - **Create role**

![IAM](/images/2.prerequisite/046-iam-name-role.png)

4. Verify **Instance profile**  
   - Open role **`ps-ec2-ssm-role`** → tab **Instance profiles**  
   - Confirm an **instance profile with the same name** exists (used by EC2/Launch Template).

![IAM](/images/2.prerequisite/047-iam-instance-profile.png)

---

### B) Attach the role to your Launch Template

5. **EC2 → Launch Templates → `lt-ps-app` → Create new version**  
   - **Advanced details → IAM instance profile**: select **`ps-ec2-ssm-role`**  
   - Save as **new version** → (optional) **Set as default version**.

![EC2](/images/2.prerequisite/048-lt-choose-instance-profile.png)

> Tip: After your ASG launches instances with this profile, you can validate in **Systems Manager → Fleet Manager / Managed instances** (Managed = Yes, Ping = Online).

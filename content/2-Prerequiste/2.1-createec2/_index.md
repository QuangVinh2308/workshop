---
title : "Verify AWS Config & Rules Status"
date  : "`r Sys.Date()`"
weight: 1
chapter: false
pre    : " <b> 2.1 </b> "
---

Goal: ensure **AWS Config** is **recording** all **network** resources and **evaluating** them against rules/conformance packs to automatically detect and remediate drift.

---

### A) Verify AWS Config is enabled
1. Open **AWS Config Console** → **Dashboard**  
2. If you see **Get started**:  
   - Choose **Record all resources** (recommended) + **Include global resources**  
   - Select an **S3 bucket** for snapshots/delivery (create new or pick existing)  
   - (Optional) **SNS** for notifications  
   - Click **Confirm** to enable
3. If already enabled: make sure **Recording = On** and there is a valid **Delivery channel**.

![Config](/images/2.compliance/001-config-dashboard.png)

> Tip: for team standardization, dedicate a bucket/KMS for **compliance**.

---

### B) Ensure **network resource types** are recorded
Go to **Settings → Recorders** and confirm these types are **being recorded**:
- **VPC, Subnet, RouteTable, InternetGateway, NATGateway, VpcEndpoint**  
- **SecurityGroup, NetworkAcl, NetworkInterface**  
- **Elastic IP, Load Balancer/Target Group**

![Config](/images/2.compliance/002-config-recorder-types.png)

---

### C) Check **Managed Rules** for networking
Open **Rules** → filter by **AWS managed** and review common rule groups:
- **Security Groups**: do **not** allow **0.0.0.0/0** on sensitive ports (SSH/RDP/common ports)  
- **Default SG**: no unrestricted inbound/outbound  
- **VPC Flow Logs**: enabled for VPC/Subnet (per standard)  
- **Load Balancer**: **HTTPS** listener required (as needed), valid health checks  
- **Endpoints/NAT/IGW**: configured per internal policy

If missing, **Add rule** for the networking rules you use.

![Config](/images/2.compliance/003-config-rules-network.png)

{{% notice tip %}}
If you want a “packaged” setup, use the **Conformance Pack** **“Operational Best Practices for Amazon VPC”** or a pack aligned to your framework (SOC 2 / PCI DSS / HIPAA).
{{% /notice %}}

---

### D) (Optional) Deploy a **Conformance Pack** for networking
1. **Conformance packs → Deploy pack**  
2. Pick a template (e.g., *Operational Best Practices for Amazon VPC*)  
3. Review parameters → **Deploy**

![Config](/images/2.compliance/004-config-conformance-pack.png)

---

### E) Review results & confirm
- **Compliance summary**: Pass/Noncompliant/Not evaluated by **Rule** & **Resource**  
- Drill into any rule → see **Noncompliant** resources + suggested **fix**  
- Capture a baseline to compare later (trend reporting)

![Config](/images/2.compliance/005-config-compliance-summary.png)

> Next steps: **[2.1.2 – Enable Recording for Network Resource Types](2.1.2-enable-network-types/)** & **[2.1.3 – Check Managed Rules (Networking)](2.1.3-check-managed-rules/)** for detailed console walkthroughs.

---

### Content
- [Verify Config Recorder & Delivery Channel](2.1.1-verify-config-recorder/)
- [Enable Recording for Network Resource Types](2.1.2-enable-network-types/)
- [Check Managed Rules (Networking)](2.1.3-check-managed-rules/)
- [Deploy Network Conformance Pack (Optional)](2.1.4-deploy-conformance-pack/)
- [Set Auto-Remediation with SSM Automation](2.1.5-auto-remediation-ssm/)
- [Configure Config Aggregator (Multi-Account/Region)](2.1.6-config-aggregator/)
- [Notifications & Reporting (EventBridge/SNS)](2.1.7-notifications-reporting/)

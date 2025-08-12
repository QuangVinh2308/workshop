---
title : "Verify AWS Config & Rules Status"
date  : "`r Sys.Date()`"
weight: 1
chapter: false
pre    : " <b> 2.1 </b> "
---

In this step, you will verify that **AWS Config** is **recording network resources** and that key **managed rules** (or **conformance packs**) are in place. This is the baseline for **Network Compliance & Audit Automation**.

---

### A) Verify AWS Config is enabled
1. Open **AWS Config → Dashboard**  
   - If you see **Get started**: enable recording for **All resources** and **Include global resources**; set an **S3 delivery bucket** (and optional **SNS**).  
   - If already enabled: ensure **Recording = On** and the **delivery channel** is healthy.

![Config](/images/2.compliance/001-config-dashboard.png)

---

### B) Ensure recording for network resource types
2. Go to **Settings → Recorders** and confirm these **network types** are recorded:
   - **VPC, Subnet, RouteTable, InternetGateway, NATGateway, VpcEndpoint**
   - **SecurityGroup, NetworkAcl, NetworkInterface**
   - **Elastic IP, Load Balancer / Target Group**

![Config](/images/2.compliance/002-config-recorder-types.png)

---

### C) Check networking managed rules
3. Open **Rules** and review/add common networking rules:
   - Overly permissive **Security Groups** (e.g., 0.0.0.0/0 on sensitive ports)
   - **Default Security Group** restricted
   - **VPC Flow Logs** enabled (per your policy)
   - **ALB/NLB** HTTPS enforcement & valid health checks (per your policy)

![Config](/images/2.compliance/003-config-rules-network.png)

---

### D) (Optional) Deploy a network conformance pack
4. **Conformance packs → Deploy**  
   - Example: **Operational Best Practices for Amazon VPC**  
   - Review parameters → **Deploy**

![Config](/images/2.compliance/004-config-conformance-pack.png)

---

### E) Validate compliance summary
5. In **Compliance**, confirm **Pass/Noncompliant/Not evaluated** and drill-down into any failing rule to see impacted resources. Capture a baseline for reporting trends.

![Config](/images/2.compliance/005-config-compliance-summary.png)

> Why this matters: with Config recording + rules in place, you unlock auto-remediation, org-wide aggregation, and clean audit evidence for frameworks like **SOC 2 / PCI DSS / HIPAA**.

**Next:** We’ll go deeper with **auto-remediation** and **aggregated reporting**.

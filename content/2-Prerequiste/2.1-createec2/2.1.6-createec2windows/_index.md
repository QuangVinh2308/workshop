---
title : "Configure AWS Config Aggregator (Multi-Account/Region)"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 2.1.6 </b> "
---

Stand up an **AWS Config Aggregator** to get a **single-pane view** of network compliance (VPC, Subnet, Route Table, IGW, NAT, VPC Endpoints, Security Group, NACL, ELB/Target Group, EIP, etc.) **across accounts and regions**. This is the final building block for **Network Compliance & Audit Automation**.

> Outcome: Organization-wide **Compliance Summary**, filtering by **framework/tags**, with drill-downs to each rule’s violations and resource status.

---

## A) Create an Aggregator (Console)

1. Open **AWS Config** → **Aggregators** → **Add aggregator**  
2. **Name**: `network-compliance-agg`  
3. **Source accounts**  
   - **Using AWS Organizations (recommended):** select **Aggregate data sources from an organization**  
     - Enable **Trusted access** for AWS Config in Organizations (if not already)  
     - Choose **All accounts** (or specific OUs)  
   - **Specific accounts:** manually enter the Account IDs
4. **Regions**  
   - Choose **All regions** (recommended) to capture multi-region drift  
   - Check **Include global resources** (e.g., IAM where applicable)
5. **Permissions**  
   - Use the suggested service-linked role or a least-privilege role you maintain
6. **Create aggregator** → wait for status **Complete**

{{% notice tip %}}
Place the aggregator in a **security/audit account** to separate monitoring duties from workload accounts.
{{% /notice %}}

---

## B) Verify & Explore

- Go to **Aggregators → `network-compliance-agg`**  
  - **Compliance** tab: Pass/Fail/Not evaluated by **rule** / **conformance pack**  
  - **Filters**: by **Account**, **Region**, **Resource type** (VPC, Subnet, RouteTable, SG, etc.)  
  - Click a rule (e.g., *restricted-ssh*) to view **non-compliant resources** and drift details
- Ensure your networking rules/packs from **2.1.3–2.1.4** are feeding data into the aggregator.

---

## C) (Optional) Notifications & Reporting

- **EventBridge**: route **Config Rules Compliance Change** events to **SNS/ChatOps** (Slack/MS Teams)  
- **Athena + S3**: query historical compliance and power **QuickSight** dashboards (trends, top offenders)

---

## D) Clean Naming & Tags

- Tag the aggregator and rules: `Compliance=Network`, `Owner=SecOps`, `Env=Prod/Dev`  
- Use clear rule/pack names so QuickSight can slice by framework (SOC 2 / PCI / HIPAA)

---

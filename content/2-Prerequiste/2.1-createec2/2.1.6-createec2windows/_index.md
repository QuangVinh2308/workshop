---
title : "Configure AWS Config Aggregator (Multi-Account/Region)"
date : "`r Sys.Date()`"
weight : 6
chapter : false
pre : " <b> 2.1.6 </b> "
---

Stand up **AWS Config Aggregator** to get a **single-pane** view of network compliance (VPC/Subnet/RouteTable/IGW/NAT/VPC Endpoints/SG/NACL/ELB…) **across accounts & regions**. Đây là mảnh ghép chốt hạ cho **Network Compliance & Audit Automation**.

> Kết quả: bạn xem **Compliance Summary** toàn Org, lọc theo **framework/tag**, drill-down chi tiết vi phạm và status của từng rule.

---

## A) Create an Aggregator (Console)

1. Mở **AWS Config** → **Aggregators** → **Add aggregator**  
2. **Name**: `network-compliance-agg`
3. **Source accounts**:
   - Nếu dùng **AWS Organizations** (khuyến nghị): chọn **Aggregate data sources from an organization**  
     - Bật **Trusted access** cho AWS Config trong Organizations (nếu chưa)  
     - Chọn **All accounts** (hoặc OU cụ thể)
   - Hoặc **Specific accounts**: nhập danh sách Account IDs thủ công
4. **Regions**:
   - Chọn **All regions** (khuyến nghị) để bắt đầy đủ drift đa vùng
   - Tick **Include global resources** (IAM, v.v.)
5. **Permissions**: dùng role được gợi ý (service-linked) hoặc chọn role sẵn có theo chuẩn least-privilege của bạn
6. **Create aggregator** → đợi trạng thái **Complete**

> Tip: đặt aggregator này ở **security/audit account** để tách biệt nhiệm vụ giám sát với workload account.

---

## B) Verify & Explore

- Vào **Aggregators → network-compliance-agg**  
  - Tab **Compliance**: tổng quan Pass/Fail/Not evaluated theo **rule/conformance pack**  
  - **Filters**: lọc theo **Account**, **Region**, **Resource type** (VPC, Subnet, RouteTable, SG, …)  
  - Nhấn vào 1 rule (ví dụ *restricted-ssh*) để xem **non-compliant resources** và chi tiết drift
- Đảm bảo các rule/network packs ở bước 2.1.3–2.1.4 đã có dữ liệu trong aggregator

---

## C) (Optional) Wire-up Notifications & Reports

- **EventBridge**: match `Config Rules Compliance Change` → **SNS/ChatOps** (Slack/MSTeams)  
- **Athena + S3**: query lịch sử compliance, feed **QuickSight** dashboard (xu hướng theo thời gian, top lỗi)

---

## D) Clean Naming & Tags

- Tag aggregator & rules: `Compliance=Network`, `Owner=SecOps`, `Env=Prod/Dev`  
- Đặt chuẩn tên rule/pack để QuickSight lọc framework (SOC2/PCI/HIPAA) dễ dàng

---

### Done? What’s next
- Quay lại **2.1.7 – Notifications & Reporting** để bắn cảnh báo real-time và dựng báo cáo định kỳ.

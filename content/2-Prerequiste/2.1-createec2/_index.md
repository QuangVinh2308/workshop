---
title : "Verify AWS Config & Rules Status"
date  : "`r Sys.Date()`"
weight: 1
chapter: false
pre    : " <b> 2.1 </b> "
---

Mục tiêu: đảm bảo **AWS Config** đang **ghi nhận (recording)** toàn bộ tài nguyên **network** và đang **đánh giá (evaluate)** theo rule/conformance pack để tự động phát hiện + khắc phục lệch chuẩn.

---

### A) Kiểm tra AWS Config đang bật
1. Mở **AWS Config Console** → **Dashboard**  
2. Nếu thấy **Get started**:  
   - **Record all resources** (khuyến nghị) + **Include global resources**  
   - **S3 bucket** để lưu snapshot/delivery (tạo mới hoặc chọn sẵn)  
   - (Optional) **SNS** để bắn thông báo  
   - **Confirm** để bật
3. Nếu đã bật: đảm bảo **Recording = On** và có **Delivery channel** hợp lệ.  

![Config](/images/2.compliance/001-config-dashboard.png)

> Tip: để chuẩn hoá theo team, đặt bucket/KMS riêng cho **compliance**.

---

### B) Đảm bảo record **network resource types**
Vào **Settings → Recorders** và confirm các loại sau **đang được record**:
- **VPC, Subnet, RouteTable, InternetGateway, NATGateway, VpcEndpoint**  
- **SecurityGroup, NetworkAcl, NetworkInterface**  
- **Elastic IP, Load Balancer/Target Group**  

![Config](/images/2.compliance/002-config-recorder-types.png)

---

### C) Kiểm tra **Managed Rules** về network
Vào **Rules** → lọc theo **AWS managed** và kiểm tra các nhóm rule phổ biến:
- **Security Groups**: **không mở 0.0.0.0/0** cho cổng nhạy cảm (SSH/RDP/ports phổ biến)  
- **Default SG**: không cho phép inbound/outbound không giới hạn  
- **VPC Flow Logs**: được bật cho VPC/Subnet (tuỳ chuẩn)  
- **Load Balancer**: listener **HTTPS** bắt buộc (tuỳ yêu cầu), health checks hợp lệ  
- **Endpoints/NAT/IGW**: cấu hình theo policy nội bộ

Nếu thiếu, **Add rule** ngay các rule networking bạn dùng.

![Config](/images/2.compliance/003-config-rules-network.png)

{{% notice tip %}}
Muốn “đánh theo bộ”, dùng **Conformance Pack** “**Operational Best Practices for Amazon VPC**” hoặc pack tuỳ framework (SOC 2 / PCI DSS / HIPAA).
{{% /notice %}}

---

### D) (Optional) Deploy **Conformance Pack** cho network
1. **Conformance packs → Deploy pack**  
2. Chọn mẫu (ví dụ: *Operational Best Practices for Amazon VPC*)  
3. Review tham số → **Deploy**

![Config](/images/2.compliance/004-config-conformance-pack.png)

---

### E) Xem kết quả & xác nhận
- **Compliance summary**: Pass/Noncompliant/Not evaluated theo **Rule** & **Resource**  
- Drill-down 1 rule bất kỳ → xem tài nguyên **Noncompliant** + **fix** gợi ý  
- Ghi nhận baseline để so sánh sau này (báo cáo xu hướng)

![Config](/images/2.compliance/005-config-compliance-summary.png)

> Kết nối tiếp theo: **[2.1.2 – Enable Recording for Network Resource Types](2.1.2-enable-network-types/)** & **[2.1.3 – Check Managed Rules (Networking)](2.1.3-check-managed-rules/)** để đi chi tiết theo từng màn hình.

---

### Content
- [Verify Config Recorder & Delivery Channel](2.1.1-verify-config-recorder/)
- [Enable Recording for Network Resource Types](2.1.2-enable-network-types/)
- [Check Managed Rules (Networking)](2.1.3-check-managed-rules/)
- [Deploy Network Conformance Pack (Optional)](2.1.4-deploy-conformance-pack/)
- [Set Auto-Remediation with SSM Automation](2.1.5-auto-remediation-ssm/)
- [Configure Config Aggregator (Multi-Account/Region)](2.1.6-config-aggregator/)
- [Notifications & Reporting (EventBridge/SNS)](2.1.7-notifications-reporting/)

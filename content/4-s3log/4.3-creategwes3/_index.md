---
title : "Create S3 Gateway Endpoint"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 4.3 </b> "
---

We’ll create an **S3 Gateway Endpoint** so instances in **private subnets** can write **Session Manager logs** to S3 **without Internet egress** — khớp baseline Predictive Scaling.

1. Go to [VPC Console](https://console.aws.amazon.com/vpc/home)
   + **Endpoints** → **Create endpoint**

2. On **Create endpoint**:
   + **Name tag**: `S3GW`
   + **Service category**: **AWS services**
   + Search **S3** → choose **`com.amazonaws.<region>.s3`** (**Type: Gateway**)

![S3](/images/4.s3/008-s3.png)

3. **VPC & Route tables**
   + **VPC**: `workshop-ps-vpc`
   + **Route tables**: select **`rtb-private`** (bắt buộc). *(Optional)* chọn luôn `rtb-public` nếu bạn muốn public subnets cũng dùng S3 qua endpoint.
   + **Policy**: **Full access** (lab). Prod có thể siết lại bằng endpoint policy.

![S3](/images/4.s3/009-s3.png)

4. Click **Create endpoint**.

{{%notice tip%}}
**Least-privilege (prod idea):** dùng **Endpoint policy** để chỉ cho phép ghi vào bucket log của bạn, ví dụ:
```json
{
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:PutObject","s3:AbortMultipartUpload"],
      "Resource": "arn:aws:s3:::YOUR-BUCKET/ssm-logs/*"
    },
    {
      "Effect": "Allow",
      "Principal": "*",
      "Action": ["s3:GetBucketLocation","s3:ListBucket"],
      "Resource": "arn:aws:s3:::YOUR-BUCKET"
    }
  ]
}

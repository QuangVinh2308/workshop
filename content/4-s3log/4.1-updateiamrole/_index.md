---
title : "Update IAM Role (enable S3 logging)"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 4.1 </b> "
---

To archive **Session Manager** logs centrally, we’ll add **S3 write permissions** to the **EC2 instance role** used by the **ASG**. This keeps access **auditable** while instances scale up/down with **Predictive Scaling**.

#### Update IAM Role

1. Go to [IAM Console](https://console.aws.amazon.com/iamv2/)  
   + **Roles** → search **`ps-ec2-ssm-role`** (instance profile used in your Launch Template)  
   + Open the role → **Add permissions → Attach policies**.

![S3](/images/4.s3/002-s3.png)

2. For the lab, attach **AmazonS3FullAccess** (quick & easy).  
   In production, use a **least-privilege** custom policy (see sample below).  
   + Select **AmazonS3FullAccess** → **Attach policy**.

![S3](/images/4.s3/003-s3.png)

3. Verify the role now lists:  
   - **AmazonSSMManagedInstanceCore** (required)  
   - **AmazonS3FullAccess** (lab)  
   Next, we’ll create the S3 bucket and point SSM session logging to it.

![S3](/images/4.s3/004-s3.png)

{{%notice tip%}}
**Production policy (least privilege)** — replace `YOUR-BUCKET` with your bucket name and optional prefix:
```json
{
  "Version": "2012-10-17",
  "Statement": [
    { "Sid": "ListBucket",
      "Effect": "Allow",
      "Action": ["s3:ListBucket","s3:GetBucketLocation"],
      "Resource": "arn:aws:s3:::YOUR-BUCKET"
    },
    { "Sid": "PutSessionLogs",
      "Effect": "Allow",
      "Action": ["s3:PutObject"],
      "Resource": "arn:aws:s3:::YOUR-BUCKET/ssm-logs/*"
    }
  ]
}

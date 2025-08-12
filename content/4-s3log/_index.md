---
title : "Manage session logs"
date : "`r Sys.Date()`"
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

With **Session Manager**, you already see **Session history** (who/when/which instance).  
To get **full command trails** for audit as your **ASG** scales in/out, we’ll enable **session log streaming to S3** — fully **private** via **VPC Endpoints**.

![S3](/images/4.s3/001-s3.png)

In this section, you will **create an S3 bucket** and **wire Session Manager logs** so every shell session on your **private ASG instances** is captured.

![port-fwd](/images/arc-log.png)

### Content
- [Update IAM Role](./4.1-updateiamrole/)
- [Create **S3 Bucket**](./4.2-creates3bucket/)
- [Create **S3 Gateway Endpoint**](./4.3-creategwes3/)
- [Configure **Session logs**](./4.4-configsessionlogs/)

---
title : "Connect to App Instance (via Session Manager)"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 3.1. </b> "
---

![SSM App Instance](/images/3.connect/arc-02.png)

In this step, you will connect to an **application instance** running in **private subnets** (managed by **ASG**) using **AWS Systems Manager Session Manager**.  
No inbound **SSH (22)** or **RDP (3389)** is required.

1. Go to [EC2 Console](https://console.aws.amazon.com/ec2/v2/home)
   + Select one **app instance** (launched from `lt-ps-app`).
   + **Actions → Security → Modify IAM role**.

![Connect](/images/3.connect/001-connect.png)

2. At **Modify IAM role**
   + Ensure the instance profile is **`ps-ec2-ssm-role`** (contains **AmazonSSMManagedInstanceCore**).
   + Click **Save** if you changed it.

{{% notice note %}}
If you used the Launch Template from section 2.1.5, the role should already be attached.  
Allow a few minutes for SSM to register the instance if it’s new.
{{% /notice %}}

3. Open [AWS Systems Manager](https://console.aws.amazon.com/systems-manager/home)
   + In the left menu, select **Session Manager** → **Start session**.

![Connect](/images/3.connect/002-connect.png)

4. Select your **app instance** (Status: Online) → **Start session**.

![Connect](/images/3.connect/003-connect.png)

5. A browser terminal opens. Run a few checks:
   ```bash
   whoami
   hostname
   uptime
   sudo tcpdump -i any -nn port 443 -c 10
   ss -tnp | grep 443

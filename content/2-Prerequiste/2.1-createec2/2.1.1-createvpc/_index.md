---
title : "Create VPC"
date : "`r Sys.Date()`"
weight : 1
chapter : false
pre : " <b> 2.1.1 </b> "
---


| title   | date            | weight | chapter | pre           |
|---------|-----------------|--------|---------|---------------|
| Create VPC (Predictive Scaling baseline) | `r Sys.Date()` | 1 | false | <b>2.1.1</b> |

In this step, you will create a **VPC foundation** for the Predictive Scaling workshop:
- 1 VPC with **two public** and **two private** subnets across **two AZs**.
- **Internet Gateway** for public subnets and **NAT Gateway** for private egress.
- *(Optional but recommended)* **VPC Endpoints for SSM** so we can manage instances privately (no SSH/22, no bastion).

> This network layout is required so an **Application Load Balancer (ALB)** can live in public subnets and an **EC2 Auto Scaling Group (ASG)** can run in private subnets, ready for **ML Predictive Scaling**.

**Architecture after this step**

![VPC – baseline for Predictive Scaling](/images/2-1-1-vpc-arch.png)

---

## A) Create the VPC

1. Console → **VPC** → *Create VPC* → **VPC only**
2. Name: `workshop-ps-vpc`  
   IPv4 CIDR: `10.0.0.0/16`  
   DNS hostnames/support: **Enabled**
3. **Create VPC**

📸 Upload later:
- `/images/2-1-1-create-vpc.png` (Create VPC screen)

---

## B) Create public & private subnets (2 AZs)

Create **four** subnets:

| Name | AZ | CIDR |
|---|---|---|
| `public-a` | `az-a` | `10.0.0.0/24` |
| `public-b` | `az-b` | `10.0.1.0/24` |
| `private-a` | `az-a` | `10.0.10.0/24` |
| `private-b` | `az-b` | `10.0.11.0/24` |

> Keep **Auto-assign public IP** = **DISABLED** for private subnets; **ENABLED** for public subnets.

📸 `/images/2-1-1-subnets.png` (Subnets list)

---

## C) Internet Gateway (IGW) for public subnets

1. **Internet Gateways** → *Create IGW* → Name: `workshop-ps-igw`
2. Attach to `workshop-ps-vpc`.
3. **Route table (public)**: create `rtb-public`, add route `0.0.0.0/0 → igw`, associate **public-a** and **public-b**.

📸 `/images/2-1-1-igw.png` (IGW attached)  
📸 `/images/2-1-1-rt-public.png` (Public RT with 0.0.0.0/0 → IGW)

---

## D) NAT Gateway for private egress

1. **Elastic IPs** → Allocate 1 EIP.
2. **NAT Gateways** → Create in **public-a**, Name: `nat-a`, EIP: the one you just allocated.
3. **Route table (private)**: create `rtb-private`, add route `0.0.0.0/0 → nat-a`, associate **private-a** and **private-b**.

> For prod-grade HA, use **2 NAT GWs** (one per AZ). For this workshop, **1 NAT** is enough.

📸 `/images/2-1-1-nat.png` (NAT GW ready)  
📸 `/images/2-1-1-rt-private.png` (Private RT with 0.0.0.0/0 → NAT)

---

## E) (Optional) VPC Endpoints for SSM (no internet needed to manage)

Create **Interface endpoints** in the VPC for:
- `com.amazonaws.<region>.ssm`
- `com.amazonaws.<region>.ssmmessages`
- `com.amazonaws.<region>.ec2messages`
- *(Optional)* `logs` and `s3` (Gateway endpoint) if you want fully private logging/artifacts.

Associate them to **private** subnets; SG allows HTTPS (443) from private subnets.

📸 `/images/2-1-1-ssm-endpoints.png` (SSM endpoints list)

---

## CLI (optional)

```bash
# Set your Region and AZ suffixes
REGION=ap-southeast-1
AZA=${REGION}a
AZB=${REGION}b

# 1) VPC
VPC_ID=$(aws ec2 create-vpc --cidr-block 10.0.0.0/16 \
  --query 'Vpc.VpcId' --output text --region $REGION)
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-hostnames --region $REGION
aws ec2 modify-vpc-attribute --vpc-id $VPC_ID --enable-dns-support   --region $REGION

# 2) Subnets
PUB_A=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.0.0/24 --availability-zone $AZA --query 'Subnet.SubnetId' --output text --region $REGION)
PUB_B=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.1.0/24 --availability-zone $AZB --query 'Subnet.SubnetId' --output text --region $REGION)
PRV_A=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.10.0/24 --availability-zone $AZA --query 'Subnet.SubnetId' --output text --region $REGION)
PRV_B=$(aws ec2 create-subnet --vpc-id $VPC_ID --cidr-block 10.0.11.0/24 --availability-zone $AZB --query 'Subnet.SubnetId' --output text --region $REGION)

aws ec2 modify-subnet-attribute --subnet-id $PUB_A --map-public-ip-on-launch --region $REGION
aws ec2 modify-subnet-attribute --subnet-id $PUB_B --map-public-ip-on-launch --region $REGION

# 3) IGW + Public RT
IGW=$(aws ec2 create-internet-gateway --query 'InternetGateway.InternetGatewayId' --output text --region $REGION)
aws ec2 attach-internet-gateway --internet-gateway-id $IGW --vpc-id $VPC_ID --region $REGION
RTB_PUB=$(aws ec2 create-route-table --vpc-id $VPC_ID --query 'RouteTable.RouteTableId' --output text --region $REGION)
aws ec2 create-route --route-table-id $RTB_PUB --destination-cidr-block 0.0.0.0/0 --gateway-id $IGW --region $REGION
aws ec2 associate-route-table --route-table-id $RTB_PUB --subnet-id $PUB_A --region $REGION
aws ec2 associate-route-table --route-table-id $RTB_PUB --subnet-id $PUB_B --region $REGION

# 4) NAT + Private RT
EIP_ALLOC=$(aws ec2 allocate-address --domain vpc --query 'AllocationId' --output text --region $REGION)
NAT=$(aws ec2 create-nat-gateway --subnet-id $PUB_A --allocation-id $EIP_ALLOC \
  --query 'NatGateway.NatGatewayId' --output text --region $REGION)
# Wait until NAT available (takes ~1-3 min)
aws ec2 wait nat-gateway-available --nat-gateway-ids $NAT --region $REGION

RTB_PRV=$(aws ec2 create-route-table --vpc-id $VPC_ID --query 'RouteTable.RouteTableId' --output text --region $REGION)
aws ec2 create-route --route-table-id $RTB_PRV --destination-cidr-block 0.0.0.0/0 --nat-gateway-id $NAT --region $REGION
aws ec2 associate-route-table --route-table-id $RTB_PRV --subnet-id $PRV_A --region $REGION
aws ec2 associate-route-table --route-table-id $RTB_PRV --subnet-id $PRV_B --region $REGION

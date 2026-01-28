# Lab 2.2 — Private Subnets & Default-Deny Compute

**Phase 2 — Secure Compute Foundations (STC Academy)**

---

## 🎯 Lab Objective

In this lab, you will deploy compute instances in **private subnets** with a **default-deny inbound posture**, while still allowing **controlled outbound access** using NAT.

The goal is to demonstrate that:

- Compute does not need public IPs to function
- Internet reachability can be **one-way and intentional**
- Exposure is a **routing decision**, not a compute requirement

---

## 🧠 Why This Lab Matters

A common anti-pattern in cloud environments is:

> “It needs updates, so it needs a public IP.”

That assumption is **wrong**.

Secure environments:

- Never expose compute directly
- Control egress explicitly
- Treat inbound access as an **identity problem**, not a network shortcut

This lab enforces that discipline.

---

## 🧱 Architecture Outcome

By the end of this lab, you will have:

- A NAT Gateway providing outbound internet access
- Private app subnets with **no inbound internet exposure**
- EC2 instances with **no public IPs**
- Verified outbound connectivity only

This is the **default-deny compute baseline** used in production environments.

---

## 📦 Prerequisites

- Completion of **Lab 2.1 — Secure VPC Layout**
- Existing VPC, subnets, and route tables
- IAM permissions for EC2, VPC, and NAT Gateways

---

## 🪜 Step-by-Step Implementation

### Step 1 — Create a NAT Gateway (Controlled Egress)

Navigate to:  
**VPC → NAT Gateways → Create NAT Gateway**

Configure:
```
| Setting | Value |
|------|------|
| Name | `stc-p2-nat-a` |
| Subnet | `stc-p2-public-a` |
| Elastic IP | Allocate new |
```
---

📌 NAT **must** live in a public subnet.

---

### Step 2 — Update Private App Route Table

Open route table:  
`stc-p2-rt-private-app`

Add route:
```
| Destination | Target |
|-----------|--------|
| 0.0.0.0/0 | NAT Gateway (`stc-p2-nat-a`) |
```
---
✅ Enables outbound access  
❌ Still no inbound exposure

---

### Step 3 — Create Security Groups (Intent-Driven)

#### 3.1 App Security Group (Private Compute)

Create a security group:
```
| Setting | Value |
|------|------|
| Name | `stc-p2-sg-app` |
| VPC | `stc-p2-vpc` |
```
---
**Inbound rules**
```
| Type | Source |
|----|----|
| SSH (22) | ❌ none |
```
**Outbound rules**
---
```
| Type | Destination |
|----|----|
| All traffic | `0.0.0.0/0` |
```
---
📌 Inbound is **default deny**.  
Outbound will be tightened later.

---

### Step 4 — Launch Private EC2 Instance

Navigate to:  
**EC2 → Launch Instance**

Configure:
```
| Setting | Value |
|------|------|
| Name | `stc-p2-app-a` |
| Subnet | `stc-p2-private-app-a` |
| Auto-assign public IP | **Disable** |
| Security Group | `stc-p2-sg-app` |
| IAM Role | none (for now) |
```
---
⚠️ If the instance has a public IP, **stop and fix it**.

---

### Step 5 — Validate Outbound-Only Connectivity

You will validate connectivity **without exposing the instance**.

At this stage:
- You **cannot SSH** — this is intentional.

Validation options:
- EC2 instance system logs
- (Optional) Temporary SSM access if already configured
- Full validation in **Lab 2.3**

**Expected behavior:**

- Instance can reach the internet via NAT
- No inbound access path exists

This is **correct and desired**.

---

## 🔐 Security Design Principles Applied

This lab enforces:

- Default-deny inbound compute
- One-way egress via NAT
- No public IPs on workloads
- Routing as a security control

These are baseline requirements in regulated environments.

---

## ✅ Validation Checklist

Confirm all of the following:

- [ ] NAT Gateway exists in a public subnet
- [ ] Private app route table points to NAT
- [ ] EC2 instance has **no public IP**
- [ ] No inbound SG rules allow internet access
- [ ] Compute functions without exposure

If any item fails, correct it before proceeding.

---

## 🧠 What You Just Built (Mental Model)

You proved that:

- Compute does not need to be reachable to be useful
- Internet access can be **asymmetric**
- Exposure is an **architectural decision**, not a default

This is the secure compute posture most teams never implement.

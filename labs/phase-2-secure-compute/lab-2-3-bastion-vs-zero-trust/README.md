# Lab 2.3 — Bastion Hosts vs Zero Trust Access

**Phase 2 — Secure Compute Foundations (STC Academy)**

---

## 🎯 Lab Objective

In this lab, you will compare two access models for private compute:

- Legacy **Bastion Host** access  
- Identity-native, **Zero Trust–ready access** using AWS Systems Manager (SSM)

The objective is not just to make access work, but to understand **why one model scales securely and the other does not**.

---

## 🧠 Why This Lab Matters

Bastion hosts exist because identity systems used to be weak.

Modern cloud platforms allow you to:

- Eliminate inbound access entirely
- Bind access to identity, posture, and audit
- Remove long-lived credentials

This lab shows the **transition path**, not just the destination.

---

## 🧱 Architecture Outcome

By the end of this lab, you will have:

- A functioning bastion host (for comparison)
- Secure access to private compute **without SSH**
- Identity-based access enforced via SSM
- A Zero Trust–ready compute environment

---

## 📦 Prerequisites

- Completion of **Lab 2.2 — Private Subnets & Default-Deny Compute**
- Private EC2 instance running (no public IP)
- NAT Gateway providing outbound access

---

## 🪜 Step-by-Step Implementation

## 🅰️ Model A — Bastion Host (Legacy Pattern)

⚠️ This model is implemented **only for comparison**.

---

### Step A1 — Create Bastion Security Group

Create a security group:
```
| Setting | Value |
|------|------|
| Name | `stc-p2-sg-bastion` |
| VPC | `stc-p2-vpc` |
```
---
**Inbound rules**
```
| Type | Source |
|----|----|
| SSH (22) | Your public IP only |
```
---
**Outbound rules**
```
| Type | Destination |
|----|----|
| All traffic | `0.0.0.0/0` |
```
---

### Step A2 — Launch Bastion Instance

Launch an EC2 instance with:
```
| Setting | Value |
|------|------|
| Name | `stc-p2-bastion-a` |
| Subnet | `stc-p2-public-a` |
| Auto-assign public IP | Enable |
| Security Group | `stc-p2-sg-bastion` |
```
---

### Step A3 — Allow Bastion → App Access

Modify the app security group (`stc-p2-sg-app`):
```
| Inbound | Source |
|------|------|
| SSH (22) | `stc-p2-sg-bastion` |
```
You may now SSH:

your laptop → bastion → private app instance


---

### Bastion Model Limitations

- Requires inbound SSH
- Requires key management
- Expands attack surface
- Weak audit guarantees
- Scales poorly

This is why bastions are **transitional**, not strategic.

---

## 🅱️ Model B — Zero Trust Access (Recommended)

This is the **preferred STC model**.

---

### Step B1 — Create IAM Role for SSM

Navigate to:  
**IAM → Roles → Create Role**

- Trusted entity: **EC2**
- Attach policy:
  - `AmazonSSMManagedInstanceCore`

**Role name:**
stc-p2-ec2-ssm-role


---

### Step B2 — Attach Role to Private EC2 Instance

- Select your private EC2 instance
- Modify IAM role
- Attach `stc-p2-ec2-ssm-role`

---

### Step B3 — Validate SSM Connectivity

Navigate to:  
**AWS Systems Manager → Fleet Manager**

Your instance should appear as **Managed**.

Start a session:

Connect → Session Manager


✅ No SSH  
✅ No inbound rules  
✅ No public IP  

---

## 🔐 Security Design Comparison
```
| Aspect | Bastion | Zero Trust (SSM) |
|----|----|----|
| Inbound access | Required | None |
| Public IPs | Required | None |
| Credential risk | High | Minimal |
| Audit logging | Weak | Strong |
| Identity-based | No | Yes |
| Zero Trust ready | ❌ | ✅ |
```
---

## 🧠 Key Takeaway

**Bastions solve reachability.**  
**Zero Trust solves authorization.**

Secure environments eliminate the former and invest in the latter.

---

## ✅ Validation Checklist

Confirm all of the following:

- [ ] Bastion access works (for comparison)
- [ ] Private EC2 accessible via SSM
- [ ] No inbound SSH required for SSM
- [ ] App security group can remove SSH entirely
- [ ] Bastion can be decommissioned safely

If you can remove the bastion — **you’ve won**.

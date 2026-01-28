# Secure Compute Foundations (AWS)

## Overview

**Secure Compute Foundations (AWS)** defines the baseline security architecture required to safely run workloads on Amazon Web Services.  
It focuses on **identity-first execution**, **least-privilege compute roles**, and **strong workload isolation**, rather than perimeter-only controls.

This foundation is intentionally minimal, opinionated, and production-aligned.

---

## Why Secure Compute Matters

In AWS, compute is where **identity, permissions, and data intersect**.

Most cloud breaches originate from:
- Over-privileged instance or task roles
- Insecure instance metadata access
- Weak isolation between workloads
- Implicit trust between services or accounts

Secure compute foundations prevent compute resources from becoming **privilege escalation or lateral movement paths**.

---

## Core Principles

### Identity Is the Perimeter
- Every workload runs with an explicit IAM role
- No embedded credentials
- No shared roles across unrelated services

### Least Privilege by Default
- Permissions scoped to required actions only
- No wildcard policies
- Trust policies are explicit and auditable

### Strong Workload Isolation
- Private compute by default
- Separation across VPCs, subnets, and accounts
- A compromise must not propagate

### Explicit Trust Relationships
- All role assumptions are intentional
- Cross-account access is tightly scoped
- Nothing is trusted implicitly

---

## AWS Compute Surfaces

These principles apply consistently across:

- Amazon EC2
- Amazon ECS
- Amazon EKS
- AWS Lambda

Each service has a different execution model, but the same security expectations.

---

## Key Security Controls

- Per-workload IAM roles
- IMDSv2 enforcement (where applicable)
- Private networking by default
- Restricted outbound access
- Immutable infrastructure patterns
- CloudTrail and VPC-level telemetry enabled

---

## Threats Addressed

- Credential theft from compute
- Instance metadata exploitation
- Role chaining and privilege escalation
- Cross-account trust abuse
- Lateral movement between workloads

---

## Scope Boundaries

This foundation **does not** cover:
- Application-level security
- SIEM or detection tooling
- Compliance frameworks
- Automated remediation

Those layers depend on compute being secure first.

---

## Intended Audience

- Cloud Security Architects  
- Platform / Infrastructure Engineers  
- DevOps and SREs with security responsibility  
- Security Consultants  

---

## Status

**Foundational and stable.**  
This layer changes slowly and is designed to support higher-level security capabilities without rework.

---

## Next

Secure Compute Foundations is a prerequisite for:
- Zero Trust cloud architectures
- Identity attack-path analysis
- Advanced security reasoning
- Consulting-grade assessments

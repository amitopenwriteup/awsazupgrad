# FinVault AI — Azure Storage Hackathon Problem Statement

> **Track:** Financial Services · Azure Storage Challenge
> **Difficulty:** Intermediate–Advanced
> **Duration:** 8 hours
> **Organisation:** FinVault Bank (fictional)

---

## Overview

FinVault Bank processes **2.4 million transactions per day**. Its data science team runs daily fraud-detection model training, KYC document pipelines, and a regulatory reporting suite. The bank must comply with **RBI/SEBI data-retention mandates (7 years)** and pass external audits on a quarterly basis.

Your team will architect the **Azure Storage layer** that powers fraud detection, audit compliance, and real-time analytics — without compromising security or cost.

---

## Current Pain Points

| # | Problem |
|---|---------|
| 1 | Transaction logs and fraud-model training data sit on a **single VM disk** — no redundancy. |
| 2 | KYC documents (scans, PDFs) are stored in a shared folder **accessible by all staff** with no identity-based access control. |
| 3 | **No archival strategy** — 7-year retention data costs the same as hot production data. |
| 4 | External audit vendors are given **full storage account keys** to upload reports. |
| 5 | A faulty ETL job last quarter **overwrote 3 months of labelled fraud-detection samples**, with no recovery path. |

---

## Scenario

### Data Types in Play

- Raw transaction CSVs (ingested every 15 minutes)
- Labelled fraud-detection training sets
- KYC PDFs & identity scans
- Trained model weights & checkpoints
- Audit reports for external vendors
- Real-time inference logs
- Historical transaction archives (5–7 years old)

### Regulatory Constraints

- Customer PII must **never leave India** (data residency requirement)
- Audit data must be **immutable** once signed off
- External vendors may **only write to designated containers**
- All access must be **logged and attributable** to an individual identity

### SLA Requirements

- Fraud model training cannot be blocked by storage unavailability
- Model inference must read feature stores with **sub-5ms latency**
- Archived data must be **restorable within 24 hours** for regulatory requests

---

## What Participants Must Deliver

1. An **Azure Storage architecture diagram** mapping each data type to the right service and access tier.
2. A **redundancy strategy** that matches business criticality — not the same level for everything.
3. A **zero-trust security model**: no shared keys, scoped vendor access, RBAC for internal teams.
4. A **data protection plan**: soft delete, versioning, WORM for compliance data.
5. A **monitoring setup** that catches cost spikes and latency issues before they hit a trading window.

---

## Service Mapping Requirements

| Data Type | Suggested Service | Tier / Redundancy Hint | Status |
|-----------|------------------|----------------------|--------|
| Raw transaction CSVs | Blob Storage — Block Blob | Hot → Cool after 30 days | Required |
| Labelled fraud datasets | Blob Storage + versioning | ZRS · Hot | Required |
| KYC PDFs & scans | Blob Storage — WORM | GRS · Immutable | Required |
| Model weights & checkpoints | Blob Storage | GRS · Soft delete | Required |
| Shared notebooks & configs | Azure Files (SMB) | ZRS · Entra ID auth | Required |
| GPU training VM disks | Premium SSD Managed Disk | Snapshots before each run | Required |
| Inference job queue | Queue Storage | LRS (re-creatable) | Required |
| Dataset version metadata | Table Storage | LRS · Low cost | Optional |
| 7-year transaction archives | Blob — Archive tier | GRS · Lifecycle policy | Required |
| Real-time feature store | Premium Blob / Premium File | LRS · Sub-5ms read | Bonus |

> Participants are expected to **justify every service choice and redundancy level** in their final presentation.

---

## Participant Checklist

Work through each item and check it off as your team completes it.

- [ ] **Architecture diagram** — Map every data type to an Azure service with tiers labeled
- [ ] **Redundancy matrix** — Assign LRS / ZRS / GRS / RA-GRS per data type with justification
- [ ] **Disable Shared Key** — Enable Entra ID auth; switch all internal access to RBAC
- [ ] **SAS tokens for vendors** — Time-limited, container-scoped SAS for audit vendor uploads
- [ ] **WORM on KYC data** — Immutable storage policy on signed-off compliance containers
- [ ] **Versioning enabled** — Blob versioning on the labelled fraud datasets container
- [ ] **Lifecycle policy** — Auto-tier raw CSVs: Hot → Cool → Archive by age
- [ ] **Soft delete configured** — Retention window set on model weights and dataset containers
- [ ] **Monitoring setup** — Metrics, Log Analytics workspace, and latency/cost alerts
- [ ] **Cost estimate** — Monthly storage cost estimate across all tiers and redundancy levels

---

## Evaluation Rubric

| Criteria | Points | What Judges Look For |
|----------|--------|---------------------|
| Architecture design | 25 | Right service for each data type, justified access tiers, clear diagram |
| Redundancy strategy | 20 | Matched to criticality. Not one-size-fits-all. GRS/LRS decisions explained |
| Security model | 20 | Zero Shared Key. SAS for vendors. RBAC for staff. Firewall/VNet rules present |
| Data protection | 20 | Soft delete, versioning, WORM, point-in-time restore all addressed |
| Monitoring | 10 | Metrics, alerts, Log Analytics, billing guards configured |
| Bonus | 5 | Premium feature store, lifecycle automation script, or innovative cost-saving approach |
| **Total** | **100** | **Minimum passing score for deployment approval: 70** |

---

## 8-Hour Timeline

| Time | Milestone | Activity |
|------|-----------|----------|
| 0:00 | Kickoff | Read problem statement. Form teams. Assign roles (architect, security, DevOps, presenter). |
| 0:30 | Data mapping | Complete the service-to-data-type mapping table. Agree on redundancy tiers. |
| 2:00 | Architecture diagram | Draw the storage architecture. Label services, tiers, access paths, and replication. |
| 4:00 | Security & protection | Define SAS scope, RBAC assignments, WORM policies, soft-delete retention windows. |
| 6:00 | Monitoring & cost | Set up lifecycle rules, configure alerts, estimate monthly storage cost with tiering. |
| 7:00 | Presentation prep | Compile architecture doc, justification notes, and 5-minute pitch deck. |
| 8:00 | Final presentations | Each team presents (5 min + 3 min Q&A). Judges score on rubric above. |

---

## Reference: Azure Storage Services Quick Guide

### Storage Account Types
- **General Purpose v2 (GPv2)** — Supports Blob, File, Queue, Table, and Disk under one account.
- **Performance Standard** — For bulk datasets (cost-driven).
- **Performance Premium** — For low-latency workloads (e.g., feature stores during live inference).

### Redundancy Options
| Level | Full Name | Protection |
|-------|-----------|------------|
| LRS | Locally Redundant Storage | Single datacenter, 3 copies |
| ZRS | Zone Redundant Storage | 3 availability zones in one region |
| GRS | Geo-Redundant Storage | Primary + secondary region (read from primary only) |
| RA-GRS | Read-Access Geo-Redundant | GRS + read access from secondary region |

### Blob Access Tiers
| Tier | Use Case | Cost |
|------|----------|------|
| Hot | Actively used data | Highest storage, lowest access |
| Cool | Infrequently accessed (30+ days) | Lower storage, higher access |
| Archive | Rarely accessed, compliance data | Lowest storage, highest retrieval |

### Security Best Practices
- **Disable Shared Key** — Too permissive for routine use; grants full account access.
- **Shared Access Signatures (SAS)** — Time-limited, scoped tokens for external parties.
- **Azure AD RBAC** — Identity-based access for internal teams (Contributor / Reader roles).
- **VNet Integration** — Restrict critical containers to traffic from the training cluster's VNet only.

### Data Protection Tools
- **Soft delete** — Recover accidentally deleted blobs within a configurable retention window.
- **Blob versioning** — Every update keeps the prior version retrievable.
- **Immutable storage (WORM)** — Write Once, Read Many. Applied to compliance-critical datasets.
- **Point-in-time restore** — Roll back a container to a previous state after corruption.

---

## Hints & Tips for Participants

> **Hint 1:** Not every dataset needs GRS. Think about what is re-creatable vs. what would cost weeks to rebuild.

> **Hint 2:** Shared Key authorization is disabled account-wide in a zero-trust model. Plan your access paths before your security layer.

> **Hint 3:** Lifecycle management policies can automate Hot → Cool → Archive transitions. Consider writing one for transaction CSVs as part of your bonus work.

> **Hint 4:** Queue Storage is the right tool for decoupling the ingestion service from labelling workers — think about how a new transaction CSV triggers downstream processing.

> **Hint 5:** Azure Files with Entra ID / AD DS auth is the equivalent of a traditional NAS — without the hardware overhead. It mounts identically on Windows and Linux.

---

*This problem statement is a synthesized, illustrative challenge built around core Azure Storage concepts — Storage Accounts, Blob/File/Disk/Queue/Table services, redundancy options, access tiers, security models, data protection, and monitoring — applied to a financial services AI/ML data pipeline.*

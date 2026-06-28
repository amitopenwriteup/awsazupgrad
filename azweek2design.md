# FinVault AI — Problem Statement to Lab Mapping

This writeup explains which real-world pain points from the FinVault Bank problem statement the lab directly addresses, and exactly where in the lab each fix happens.

---

## Problem 1 — Transaction Logs on a Single VM Disk with No Redundancy

The problem statement describes transaction logs and fraud-model training data sitting on a single VM disk. One hardware failure and the data is gone. There is no replication, no failover, and no recovery path.

The lab fixes this in two places. First, when creating the core storage account in Part 1, you set redundancy to Geo-redundant storage (GRS). This immediately moves data off a single VM disk and into Azure-managed blob storage where three synchronous copies exist within the primary South India datacenter and an additional three copies are asynchronously replicated to a paired secondary region. A single disk failure, a rack failure, or even a full datacenter outage cannot cause data loss. Second, the lab places fraud-model training data in its own dedicated `fraud-datasets` container under this GRS account, separating it from raw transactional data and giving each its own access controls and protection policies.

The redundancy justification review in Part 10 goes one step further by explaining why not everything needs GRS. Queue messages for the ingestion pipeline are re-creatable and use LRS, keeping costs proportionate to actual risk.

---

## Problem 2 — KYC Documents in a Shared Folder Accessible by All Staff

The problem statement describes KYC PDFs and identity scans stored in a shared network folder with no identity-based access control. Any employee — regardless of role — can read, copy, or delete sensitive customer documents. This is both a security failure and a regulatory violation under RBI data privacy requirements.

The lab addresses this across three parts. In Part 1, shared key access is disabled on the storage account entirely. This means no one can construct a connection string and connect with full account-level permissions the way a shared folder allows. In Part 2, the `kyc-documents` container is created as a private container with no public access. In Part 7, RBAC is configured so that only users with explicit Entra ID role assignments — Storage Blob Data Contributor or Reader — can access blob data at all. A staff member without an assignment gets a 403 Forbidden response, not an open folder. Every access is tied to an individual identity and logged to the Log Analytics workspace configured in Part 9.

---

## Problem 3 — No Archival Strategy, Old Data Costs the Same as Hot Production Data

The problem statement notes that 7-year retention data — some of it years old and never accessed — is stored at the same cost as actively queried production data. There is no tiering, no lifecycle policy, and no cost differentiation. This is a direct operating cost problem on top of a compliance risk.

The lab addresses this entirely in Part 3. You configure a lifecycle management rule scoped to the `raw-transactions` container with three automatic transitions. Blobs move to Cool storage after 30 days, reducing storage cost by roughly half compared to Hot. They move to Archive storage after 365 days, reducing cost by over 90% compared to Hot. They are automatically deleted after 2557 days — exactly 7 years — satisfying the RBI retention mandate without manual intervention. Once set, this policy runs daily and requires no human oversight. The cost estimate discussion in Part 10 reinforces why the Archive tier exists specifically for compliance data that must be retained but will rarely or never be read.

---

## Problem 4 — External Audit Vendors Given Full Storage Account Keys

The problem statement describes audit vendors receiving full storage account keys to upload reports. A storage account key is equivalent to root access — it grants read, write, delete, and administrative permissions across every container in the account. If a vendor's system is compromised, or if they act maliciously, they have unrestricted access to transaction data, KYC documents, and model weights.

The lab eliminates this entirely. In Part 1, shared key access is disabled at the account level, making it impossible to hand out a key that works. In Part 6, you generate a container-scoped SAS token for the `audit-reports` container with only Write and Create permissions checked. The vendor can upload to that one container and do nothing else. The token has an explicit expiry date — set during generation — so access automatically terminates after the audit window. HTTPS-only is enforced, and you can optionally lock the token to the vendor's IP address. The vendor never sees credentials that touch any other part of the storage account.

---

## Problem 5 — Faulty ETL Job Overwrote 3 Months of Labelled Fraud-Detection Samples with No Recovery Path

This is described as an event that already happened — a concrete, costly data loss incident. A broken ETL job overwrote labelled training data that took months to accumulate, with no way to recover it. Model retraining was blocked until the data could be reconstructed manually.

The lab directly addresses this in two parts. In Part 1, blob versioning is enabled at the storage account level. From that point forward, every write to a blob automatically preserves the previous version as a recoverable snapshot. In Part 5, you verify this hands-on by uploading a file named `training-labels.csv`, then overwriting it, and then navigating to the Versions tab to confirm both versions exist and the original is fully recoverable. A real ETL overwrite of this kind would now be remediated in minutes — navigate to the affected blob, open the Versions tab, click the version from before the bad job ran, and restore it.

Soft delete in Part 8 extends this protection further. Even if a deletion is triggered — accidental or otherwise — the blob remains recoverable for 30 days. The combination of versioning and soft delete means the lab creates two independent safety nets against the exact incident the problem statement describes.

---

## Summary Table

| Problem from Statement | Root Cause | Lab Part That Fixes It |
|---|---|---|
| Transaction logs on a single VM disk | No redundancy | Part 1 — GRS on storage account |
| KYC documents open to all staff | No identity-based access control | Parts 1, 2, 7 — Shared key disabled, private containers, RBAC |
| No archival strategy, uniform storage cost | No lifecycle policy | Part 3 — Lifecycle tiering and 7-year delete |
| Vendors given full account keys | Overprivileged external access | Parts 1, 6 — Shared key disabled, scoped SAS token |
| ETL overwrite destroyed training data | No versioning or recovery | Parts 1, 5, 8 — Blob versioning, recovery verified, soft delete |

---

Every lab task maps back to a documented failure or risk in the problem statement. Nothing in the lab is theoretical — each configuration step exists because a real gap in the current architecture makes it necessary.

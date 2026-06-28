# FinVault AI — Azure Storage Hands-On Lab

**Financial Services · Azure Storage · 40-Minute Workshop**

You are a cloud architect at FinVault Bank. Today you will configure the Azure Storage layer that powers the bank's fraud-detection pipeline, secures KYC documents, and satisfies RBI/SEBI 7-year retention mandates. Every step below is something you do in the Azure Portal — no scripts, no automation, no shortcuts.

---

## What You Will Build

By the end of this lab you will have:

- A properly tiered storage account with separate containers per data type
- Lifecycle management that automatically moves transaction logs through Hot, Cool, and Archive
- WORM immutable storage locking down signed-off KYC documents
- Blob versioning protecting labelled fraud-detection training sets
- Vendor access restricted to time-limited SAS tokens — no shared keys
- RBAC assignments for internal teams
- A Log Analytics workspace receiving storage diagnostics

---

## Prerequisites

- An active Azure subscription with Contributor rights
- Access to the Azure Portal at portal.azure.com
- A browser and about 40 minutes of focused time

---

## Lab Setup — Create Your Resource Group

Everything in this lab lives inside one resource group so you can delete everything cleanly at the end.

Go to portal.azure.com. In the top search bar type **Resource groups** and select it. Click **Create**. Fill in:

- Subscription: your active subscription
- Resource group name: `finvault-rg`
- Region: **South India** (required — PII must not leave India)

Click **Review + create**, then **Create**. Wait for the deployment to finish, then click **Go to resource group**.

---

## Part 1 — Create the Core Storage Account

In your `finvault-rg` resource group, click **Create** at the top. Search for **Storage account** and click **Create**.

### Basics tab

- Storage account name: `finvaultprod` followed by 4 random digits, e.g. `finvaultprod4821` (must be globally unique, lowercase, no hyphens)
- Region: **South India**
- Performance: **Standard**
- Redundancy: **Geo-redundant storage (GRS)**

Click **Next: Advanced**.

### Advanced tab

Locate the section **Blob storage**. Enable:

- Enable blob soft delete — set retention to **30 days**
- Enable container soft delete — set retention to **7 days**
- Enable blob versioning — tick the checkbox

Locate **Security**. Make sure:

- **Require secure transfer for REST API operations** is checked
- **Enable storage account key access** — UNCHECK this. This is the critical zero-trust move. Shared keys grant full account access to whoever holds them. By disabling them, all access must go through Azure Entra ID or scoped SAS tokens.

Click **Next: Networking**.

### Networking tab

- Connectivity method: **Disable public access and use private access**

Wait — for this lab, since you likely do not have a VNet pre-configured, switch to **Enabled from selected virtual networks and IP addresses** and add your current client IP using the **Add your client IP address** link. This lets you work through the portal today while still blocking all other public traffic.

Click **Review**, then **Create**. Wait for deployment, then click **Go to resource**.

---

## Part 2 — Create Containers for Each Data Type

You are inside your storage account. In the left menu, under **Data storage**, click **Containers**. You will create one container per logical data domain. Click **+ Container** for each of the following:

**Container 1**

- Name: `raw-transactions`
- Public access level: **Private**
- Click Create

**Container 2**

- Name: `fraud-datasets`
- Public access level: **Private**
- Click Create

**Container 3**

- Name: `kyc-documents`
- Public access level: **Private**
- Click Create

**Container 4**

- Name: `model-weights`
- Public access level: **Private**
- Click Create

**Container 5**

- Name: `audit-reports`
- Public access level: **Private**
- Click Create

You should now see five containers listed. All are private, accessible only through authenticated requests.

---

## Part 3 — Lifecycle Management for Transaction Logs

Raw transaction CSVs come in every 15 minutes. They are hot for the first 30 days, then rarely accessed, then archived. You will now teach Azure to move them automatically.

In the left menu, under **Data management**, click **Lifecycle management**. Click **Add a rule**.

### Rule details

- Rule name: `transaction-tiering`
- Rule scope: **Limit blobs with filters**
- Blob type: **Block blobs**
- Blob subtype: **Base blobs**

Click **Next**.

### Base blobs tab

You need three transitions. Set them up as follows:

Under **Base blob was last modified more than (days ago)**:

- Row 1: Move to **Cool storage** after **30** days
- Click **+ Add condition** to add a second row
- Row 2: Move to **Archive storage** after **365** days
- Click **+ Add condition** to add a third row
- Row 3: Delete the blob after **2557** days (that is 7 years, satisfying the RBI retention mandate)

Click **Next**.

### Filters tab

- Blob prefix (optional): `raw-transactions/`

This scopes the rule to only the transaction container.

Click **Next**, then **Add**. The rule is now active and will run once daily.

---

## Part 4 — WORM Immutable Storage on KYC Documents

KYC documents must be immutable once signed off. No one — not even storage admins — can delete or overwrite them within the retention window. This satisfies RBI audit requirements.

In the container list, click on **kyc-documents**. In the left menu for this container, click **Access policy**.

Under **Immutable blob storage**, click **+ Add policy**.

- Policy type: **Time-based retention**
- Set retention period: **2557** days (7 years)

Click **Save**. You will see the policy listed with the status **Unlocked**.

Now click the policy row, then click **Lock policy**. Read the warning — once locked, this policy cannot be shortened or deleted for the duration. Click **OK** to confirm.

The status changes to **Locked**. Any blob uploaded to `kyc-documents` is now protected: it cannot be deleted or overwritten until its retention period expires, even by a subscription owner.

---

## Part 5 — Verify Blob Versioning on Fraud Datasets

Blob versioning is already on for the whole account, but verify it is working correctly for the fraud datasets container. When the ETL job overwrote 3 months of labelled samples, there was no recovery path. Now there is.

Click on the **fraud-datasets** container. Click **Upload**. Upload any small text file from your local machine — even a .txt with a few words is fine. Name it `training-labels.csv`.

After upload, click the file name. You will see a **Versions** tab. Click it. Currently there is one version.

Go back, click **Upload** again, upload the same filename with different content. When prompted, choose to **Overwrite** the existing file.

Click on the file name again and click **Versions**. You now see two versions. Click the older version — it shows a full timestamp and a separate blob URL. This is your recovery path. The ETL overwrite scenario is now reversible.

---

## Part 6 — Scoped SAS Token for Audit Vendor

External audit vendors need to upload reports. They must not get storage account keys. They must not see any other containers. You will generate a container-scoped SAS token.

In your storage account, click on **Containers** and click on **audit-reports**.

In the left menu, click **Shared access tokens**.

Configure:

- Signing method: **Account key** (acceptable here since this is specifically for external scoped access, not internal RBAC)
- Permissions: tick only **Write** and **Create** — do not tick Read, Delete, or List
- Expiry: set to 24 hours from now (or the expected audit window duration)
- Allowed protocols: **HTTPS only**
- Allowed IP addresses: enter the vendor's IP address if known

Click **Generate SAS token and URL**. Copy the **Blob SAS URL** and the **SAS token** separately.

This is what you give the vendor. Notice what they cannot do with this token: they cannot read files from other containers, cannot delete anything, cannot access the storage account at all beyond writing to `audit-reports`. The token expires automatically.

---

## Part 7 — RBAC for Internal Teams

Internal data scientists and ML engineers must not use shared keys or SAS tokens. They authenticate through their Entra ID identity and receive only the permissions their role requires.

In the left menu of your storage account, click **Access control (IAM)**. Click **+ Add**, then **Add role assignment**.

### Assign Storage Blob Data Contributor to the ML team

- Role tab: search for and select **Storage Blob Data Contributor**
- Click **Next**
- Members tab: click **+ Select members**
- Search for a user or group in your Entra ID tenant (your own account works for this exercise)
- Select them and click **Select**
- Click **Review + assign** twice

This role gives read and write access to blob data without granting control-plane access. The user can read and write blobs but cannot regenerate keys, change network rules, or delete the account.

Go back to **Access control (IAM)** and click **+ Add role assignment** again.

### Assign Storage Blob Data Reader to read-only analysts

- Role: **Storage Blob Data Reader**
- Assign to another user or group representing your reporting analysts
- Complete the assignment

Data scientists get Contributor. Reporting analysts get Reader. Neither group has account keys or can escalate their permissions without a control-plane role.

---

## Part 8 — Soft Delete Verification for Model Weights

Model weights and checkpoints are expensive to retrain. Soft delete gives you a 30-day window to recover accidental deletions.

Click on the **model-weights** container. Click **Upload** and upload any file — name it `model-v1.pkl`.

After upload, select the file's checkbox and click **Delete** at the top. Confirm deletion. The file disappears from the default view.

At the top of the container view, find the **Show deleted blobs** toggle and enable it. Your `model-v1.pkl` reappears, shown with a strikethrough or marked as deleted. Click the file, then click **Undelete** at the top. The file is restored to the active container.

Soft delete is now verified. The 30-day retention window configured in Part 1 gives you a month to catch any accidental deletions before they become permanent.

---

## Part 9 — Monitoring with Log Analytics

You need to know when costs are spiking, when latency hits problematic thresholds, and who accessed what for audit attribution.

In the top search bar, type **Log Analytics workspaces** and select it. Click **Create**.

- Subscription: your subscription
- Resource group: `finvault-rg`
- Name: `finvault-logs`
- Region: **South India**

Click **Review + create**, then **Create**. Wait for deployment.

Now go back to your storage account `finvaultprod####`. In the left menu, under **Monitoring**, click **Diagnostic settings**. You will see entries for **blob**, **file**, **queue**, and **table**. Click **blob**.

Click **+ Add diagnostic setting**.

- Diagnostic setting name: `blob-to-logs`
- Under Logs, check: **StorageRead**, **StorageWrite**, **StorageDelete**
- Under Metrics, check: **Transaction**
- Destination: tick **Send to Log Analytics workspace** and select `finvault-logs`

Click **Save**.

Every read, write, and delete on blob storage is now logged to your workspace. You can query for all deletes in the last 24 hours, all access from a specific identity, or all operations on the `kyc-documents` container.

### Create a Cost Alert

In the top search bar type **Cost Management** and open it. Click **Cost alerts** in the left menu, then **+ Add**.

- Alert type: **Budget**
- Scope: your subscription or `finvault-rg`
- Budget name: `storage-budget`
- Amount: set a monthly limit appropriate to your lab (e.g., 500 INR or $10 USD)
- Alert conditions: alert at 80% and 100% of budget
- Alert recipients: your email address

Click **Create**. You now have a billing guard that emails you before a runaway lifecycle policy or misconfigured replication burns through budget.

---

## Part 10 — Redundancy Justification Review

Before wrapping up, review the redundancy decisions you have made and why they are correct.

The core `finvaultprod####` account is set to GRS. This protects KYC documents, model weights, and fraud datasets — none of which are easily recreatable. A datacenter failure in South India does not result in data loss.

The Queue Storage service used for ingestion job queues does not need GRS because queue messages are transient. If the queue is lost, the ingestion service re-reads from the source CSV landing zone. LRS is sufficient and cheaper.

Table Storage holding dataset version metadata is also LRS — metadata is lightweight and can be reconstructed from the blob version history you verified in Part 5.

The real-time feature store (bonus scope) would use a Premium Blob storage account, separate from this GPv2 account, with LRS and sub-5ms read latency. Premium performance is local by nature; GRS replication adds latency that defeats the purpose.

---

## Lab Verification Checklist

Go through each item and confirm you have completed it.

- Core GPv2 storage account created in South India with GRS
- Shared Key access disabled on the storage account
- Five containers created: raw-transactions, fraud-datasets, kyc-documents, model-weights, audit-reports
- Lifecycle policy applied to move transaction CSVs: Hot at 0 days, Cool at 30 days, Archive at 365 days, Delete at 2557 days
- WORM immutable policy locked on kyc-documents with 2557-day retention
- Blob versioning confirmed working on fraud-datasets by uploading and overwriting a file
- SAS token generated for audit vendor: Write and Create only, HTTPS only, time-limited, no shared key used
- RBAC: Storage Blob Data Contributor assigned to ML team, Storage Blob Data Reader assigned to analysts
- Soft delete confirmed working on model-weights: file deleted and successfully undeleted
- Log Analytics workspace created and blob diagnostics streaming to it
- Cost budget alert configured

---

## Common Mistakes to Avoid

Locking the WORM policy before testing your retention period value. Once locked, the period cannot be shortened. Use Unlocked mode for testing; only lock in production.

Generating a SAS token from the account key after disabling shared key access. The portal will warn you or block the generation. In that scenario, use a User Delegation SAS instead, which derives from an Entra ID identity.

Setting GRS on inference job queues. Queue messages are re-creatable. GRS doubles the cost for zero operational benefit here.

Forgetting to scope the lifecycle filter prefix to a specific container. Without a prefix filter, the rule applies to every blob in the account and can archive active model weights prematurely.

---

## Cleanup

When you are done and want to stop incurring charges, go to **Resource groups**, select `finvault-rg`, click **Delete resource group**, type the resource group name to confirm, and click **Delete**. All storage accounts, the Log Analytics workspace, and the alert will be removed.

---

## Key Concepts Covered

This lab covered the following Azure Storage concepts in a real financial services context:

GPv2 storage accounts unify Blob, File, Queue, and Table under one account. Blob access tiers — Hot, Cool, Archive — let you match cost to access frequency. GRS replicates data to a secondary region; LRS keeps three copies in one datacenter; ZRS spans availability zones in one region. Lifecycle management automates tier transitions on a schedule. Immutable storage with WORM locks blobs against modification or deletion for a defined period. Blob versioning keeps every prior state of a blob recoverable. Soft delete adds a recycle bin for accidental deletions. Disabling shared key access forces all callers to authenticate via Entra ID or use scoped SAS tokens. RBAC roles — Storage Blob Data Contributor and Reader — grant data-plane access tied to individual identities. Diagnostic settings plus Log Analytics provide queryable, attributable access logs for audit compliance.

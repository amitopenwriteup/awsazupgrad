# Case Study: Storing AI Data at Scale with AWS Storage Services

### How a growing AI company architected its data pipeline using EC2 Instance Store, EBS, EFS, and S3

---

## 1. Background

**Company:** NimbusML Labs (fictional composite, representative of a mid-size NLP/recommendation-engine startup)
**Challenge:** NimbusML trains large language and recommendation models on a constantly growing mix of scraped text, user interaction logs, labeled fine-tuning sets, and model checkpoints. Their early setup — everything dumped onto a single EC2 instance's local disk — broke down as soon as multiple training jobs needed to share data and instances started being recycled, taking data with them.

This case study walks through how NimbusML mapped each part of its AI data lifecycle onto the right AWS storage service: **EC2 Instance Store, Amazon EBS, Amazon EFS, and Amazon S3**.

---

## 2. The Core Decision: Matching Storage to Data Lifetime

The first lesson NimbusML learned the hard way: **not all storage is meant to outlive the compute it's attached to.**

| Storage Type | Persistence | Scope | Best AI Use |
|---|---|---|---|
| **EC2 Instance Store** | Ephemeral — lost on stop/terminate | Single instance | Scratch space, cache, temp shuffle buffers during training |
| **EBS** | Persistent, independent of instance lifecycle | Single instance (mostly) | Boot volumes, active training datasets, databases |
| **EFS** | Persistent | Shared across thousands of EC2s | Shared notebooks, common feature stores, multi-worker training data |
| **S3** | Persistent, virtually unlimited | Accessed via API/HTTP | Raw datasets, model checkpoints, archives, data lake |

This single table became the team's reference whenever someone asked "where should this data live?"

---

## 3. Stage 1 — High-Speed Scratch Space: EC2 Instance Store

During training, GPU instances constantly shuffle, decompress, and tokenize batches of data. Writing this churn to network storage wasted IOPS budget, so NimbusML used **EC2 Instance Store** — ephemeral, block-level storage physically attached to the host — for:

- Decompressed batches mid-training
- Local tokenizer caches
- Temporary shuffle buffers for large dataset epochs

The trade-off was understood and accepted: **instance store data is lost the moment the instance stops or terminates.** Nothing irreplaceable was ever written here — only data that could be regenerated from S3 in minutes.

---

## 4. Stage 2 — Persistent, Instance-Attached Storage: Amazon EBS

For everything that needed to survive an instance restart but didn't need to be shared across machines, NimbusML used **Amazon EBS** — block storage that's independent of the instance lifecycle, replicated within its Availability Zone, and can be detached/reattached as needed.

They selected EBS volume types deliberately, based on each workload's I/O profile:

| Volume Type | Used For | Why |
|---|---|---|
| **gp3** | General-purpose dev/test training boxes | Balanced cost/performance, predictable baseline IOPS |
| **io2** | Feature-store database backing the recommendation model | Provisioned IOPS for latency-sensitive lookups |
| **st1** | Bulk log processing nodes | High throughput for sequential reads over large log files |
| **sc1** | Cold archival volumes for old experiment outputs | Lowest cost for rarely accessed data |

For their highest-availability training cluster, they used **EBS Multi-Attach** on an io2 volume so that a small clustered metadata service could be reached from multiple EC2 instances simultaneously — a setup normally reserved for HA applications, repurposed here for distributed training coordination.

---

## 5. Stage 3 — Shared Workspace Across Many Instances: Amazon EFS

As the data science team grew, multiple training jobs running on different EC2 instances needed access to the **same** feature sets and notebooks at the same time — something EBS (largely single-instance) couldn't do well.

**Amazon EFS** — a fully managed NFS file system that scales automatically and supports thousands of concurrent EC2 connections — became the shared workspace:

- A common directory of curated training features, mounted identically across every training instance in the fleet.
- Shared Jupyter notebook storage so any data scientist's environment could be recreated on a new instance instantly.
- A staging area where multiple preprocessing workers wrote partial outputs in parallel before consolidation.

The team's rule of thumb, drawn directly from the **EBS vs EFS** distinction: *if more than one EC2 instance needs to read or write the same files at the same time, it belongs in EFS, not EBS.*

---

## 6. Stage 4 — The Data Lake and Long-Term Home: Amazon S3

The bulk of NimbusML's AI data — raw scraped text, labeled datasets, model checkpoints, evaluation results — lives in **Amazon S3**, chosen for its internet-scale object storage, key-value access model, and **99.999999999% (11 nines) durability**.

### Organizing the data lake
- Buckets per project/team, with **bucket policies** (JSON-based access control) restricting access by IP range and enforcing server-side encryption on every upload.
- **S3 Access Points** were introduced once the data lake grew to serve multiple internal teams (the NLP team, the recommendations team, an external research partner) — each got its own access point with a scoped policy, avoiding a single sprawling bucket policy that's hard to audit. This mirrors a regulated-industry pattern: shared datasets, application-specific access control.

### Protecting against loss and corruption
- **S3 Versioning** was enabled on every bucket holding labeled training data — a botched relabeling script overwriting a curated dataset is recoverable rather than catastrophic.
- **Cross-Region Replication (CRR)** keeps a synchronized copy of production model checkpoints in a second region, so a regional outage can't take down the only copy of a trained model.
- **Same-Region Replication (SRR)** mirrors raw ingestion data into a separate "data science sandbox" bucket within the same region, so experimentation never risks the canonical copy.

### Managing cost as data ages
NimbusML automated tiering with **S3 Lifecycle Rules**, moving data through storage classes as it aged out of active use:

| Storage Class | Used For |
|---|---|
| **S3 Standard** | Current month's active training datasets |
| **S3 Standard-IA** | Last quarter's datasets, still occasionally reused |
| **S3 One Zone-IA** | Non-critical intermediate artifacts, reproducible from source |
| **S3 Glacier Instant Retrieval** | Older model checkpoints kept "just in case" |
| **S3 Glacier Flexible Retrieval** | Long-term backups of full training corpora |
| **S3 Glacier Deep Archive** | Compliance-driven retention of historical training data, rarely touched |
| **S3 Express One Zone** | The hot feature cache feeding real-time inference, where millisecond access matters most |

A lifecycle rule moving data **Standard → IA → Glacier** runs automatically once a dataset hasn't been touched in a defined window, with no manual intervention required.

### Sharing data securely without handing out credentials
- **S3 Pre-Signed URLs** let NimbusML give an external annotation vendor a time-limited link to upload labeled data directly into a specific S3 prefix — no AWS credentials required, expiry capped well under the 7-day SDK maximum.
- For internal automation, **S3 Event Notifications** trigger a Lambda function the moment a new raw file lands in the ingestion bucket, kicking off preprocessing without any polling.

### Operating at scale
- **S3 Batch Operations** applied new compliance tags across tens of millions of objects in one job when a new data-retention policy went into effect — far faster than scripting it object-by-object.
- **S3 Storage Lens** gave the team org-wide visibility into usage and cost across every account and region, surfacing buckets where data had quietly drifted into the wrong (expensive) storage class.
- **Multipart upload and parallelism** kept multi-terabyte dataset uploads from a single slow connection from becoming a bottleneck.

---

## 7. Putting It Together: A Single Training Run, End to End

1. A scheduled job pulls newly scraped web text into an **S3** ingestion bucket; an **S3 Event Notification** triggers a Lambda-based cleaning step.
2. Cleaned text lands in a curated **S3** bucket, versioned and lifecycle-managed.
3. A fleet of EC2 training instances mounts the curated dataset via **EFS** so every worker sees the same files.
4. Each instance uses its local **EC2 Instance Store** for token-shuffling scratch space during the epoch.
5. Model checkpoints are periodically written to an **EBS io2** volume for fast local resumption, and asynchronously pushed to **S3** (replicated cross-region) for durability.
6. Once training completes, the final model artifact and logs move into S3's lifecycle pipeline, eventually settling into **Glacier** for long-term retention.

---

## 8. Outcome

| Before | After |
|---|---|
| Single EC2 local disk; data lost on every restart | Lifecycle-aware storage spanning Instance Store → EBS → EFS → S3 |
| No shared access between training jobs | EFS shared file system across the entire training fleet |
| One bucket, one broad policy | S3 Access Points giving each team/partner scoped, auditable access |
| Manual, ad-hoc archiving | Automated S3 Lifecycle Rules tiering data by access pattern |
| External vendors required AWS credentials to contribute data | Pre-signed URLs for credential-free, time-boxed uploads |
| No visibility into storage spend | S3 Storage Lens surfacing cost optimization opportunities org-wide |

---

## 9. Key Takeaways for AI Teams Storing Data on AWS

1. **Treat ephemeral storage as ephemeral** — Instance Store is fast and free, but only for data you can afford to lose on every instance cycle.
2. **EBS for one machine, EFS for many** — the moment more than one instance needs the same files concurrently, move to EFS.
3. **S3 is the long-term home for AI data** — durability, scale, and the richest set of lifecycle, security, and access-control tools make it the natural data-lake foundation.
4. **Automate tiering, don't manage it by hand** — Lifecycle Rules quietly turn aging datasets into a cost optimization rather than a recurring chore.
5. **Prefer scoped, temporary access over shared credentials** — pre-signed URLs and access points let you collaborate with vendors and other teams without widening your blast radius.

---

*This case study is a synthesized, illustrative example built around core AWS storage concepts (EC2 Instance Store, EBS volume types, EFS, S3 storage classes, versioning, replication, lifecycle rules, access points, and pre-signed URLs) applied to a typical AI/ML data pipeline.*

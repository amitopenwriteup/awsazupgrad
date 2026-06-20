# AWS Week 1 – Study Guide
### EC2, ECS, EKS, Lambda & Load Balancers

---

## Part 1: 20 Multiple Choice Questions (MCQs)

---

**Q1.** What does Amazon EC2 stand for, and what is its primary purpose?

- A. Elastic Compute Cloud – provides resizable virtual server capacity in the AWS Cloud
- B. Elastic Container Cloud – runs Docker containers without managing infrastructure
- C. Enterprise Compute Cluster – provides bare-metal server hosting
- D. Elastic Cache Cloud – provides in-memory caching for databases

> ✅ **Answer: A** – EC2 (Elastic Compute Cloud) lets you launch and manage virtual servers with configurable compute, network, and security settings.

---

**Q2.** Which EC2 instance family would be the best choice for a memory-intensive in-memory database like Redis?

- A. Compute Optimized (C-family)
- B. Storage Optimized (I-family)
- C. Memory Optimized (R/X-family)
- D. General Purpose (M-family)

> ✅ **Answer: C** – Memory Optimized instances are designed for workloads that process large datasets in RAM.

---

**Q3.** A company runs a 24/7 production website and has committed to AWS for 3 years. Which purchasing option gives the highest discount?

- A. On-Demand Instances
- B. Spot Instances
- C. Standard Reserved Instances
- D. Savings Plans

> ✅ **Answer: C** – Standard Reserved Instances with a 3-year term offer the deepest discounts (up to ~72%) for steady, predictable workloads.

---

**Q4.** Which EC2 purchasing option can be interrupted by AWS and is suited for fault-tolerant, flexible batch jobs?

- A. On-Demand Instances
- B. Spot Instances
- C. Reserved Instances
- D. Savings Plans

> ✅ **Answer: B** – Spot Instances use spare EC2 capacity at up to ~90% discount but can be reclaimed by AWS with a 2-minute warning.

---

**Q5.** Security Groups in AWS operate at which level?

- A. Subnet level
- B. VPC level
- C. Instance level
- D. Region level

> ✅ **Answer: C** – Security Groups act as virtual firewalls at the EC2 instance level, controlling inbound and outbound traffic.

---

**Q6.** By default, what does a newly created Security Group allow for inbound and outbound traffic?

- A. Allows all inbound; denies all outbound
- B. Denies all inbound; allows all outbound
- C. Allows all inbound and outbound
- D. Denies all inbound and outbound

> ✅ **Answer: B** – New Security Groups deny all inbound traffic and allow all outbound traffic by default.

---

**Q7.** You need a fixed, static public IP for your EC2 instance that persists through stop/start cycles. Which should you use?

- A. Private IP
- B. Public IP
- C. Elastic IP
- D. NAT IP

> ✅ **Answer: C** – Elastic IPs are static public IPv4 addresses that remain associated with your account and don't change when an instance is stopped and restarted.

---

**Q8.** A high-frequency trading application requires ultra-low latency between EC2 instances. Which placement group type should be used?

- A. Spread Placement Group
- B. Partition Placement Group
- C. Cluster Placement Group
- D. Random Placement Group

> ✅ **Answer: C** – Cluster Placement Groups place instances close together within a single Availability Zone to achieve maximum network throughput and minimum latency.

---

**Q9.** You need to deploy a Hadoop cluster of 500+ nodes where failure of a single rack should impact only a small subset of nodes. Which placement group is most appropriate?

- A. Cluster
- B. Spread
- C. Partition
- D. None – use Auto Scaling instead

> ✅ **Answer: C** – Partition Placement Groups divide instances across logical partitions (mapped to separate hardware racks), so a rack failure only affects one partition.

---

**Q10.** What is the primary difference between horizontal and vertical scaling in AWS?

- A. Horizontal scaling increases instance size; vertical scaling adds more instances
- B. Horizontal scaling adds more instances; vertical scaling increases instance size
- C. Both refer to adding more EC2 instances
- D. Horizontal scaling applies to databases only

> ✅ **Answer: B** – Horizontal scaling (scale out/in) adds or removes instances; vertical scaling (scale up/down) increases or decreases the size of an existing instance.

---

**Q11.** Which AWS load balancer operates at Layer 7 (HTTP/HTTPS) and supports path-based routing?

- A. Network Load Balancer (NLB)
- B. Classic Load Balancer (CLB)
- C. Gateway Load Balancer (GLB)
- D. Application Load Balancer (ALB)

> ✅ **Answer: D** – ALB operates at Layer 7, enabling content-based routing rules including path-based and host-based routing.

---

**Q12.** Which load balancer is best suited for handling millions of TCP/UDP requests per second with ultra-low latency and supports static IP addresses?

- A. Application Load Balancer
- B. Network Load Balancer
- C. Gateway Load Balancer
- D. Classic Load Balancer

> ✅ **Answer: B** – NLB operates at Layer 4 (TCP/UDP) and is designed for extreme performance with static IP support.

---

**Q13.** A team wants to run containerized microservices on ECS but does NOT want to manage or patch the underlying EC2 servers. Which launch type should they use?

- A. ECS EC2 Launch Type
- B. ECS Fargate Launch Type
- C. ECS Spot Launch Type
- D. ECS Reserved Launch Type

> ✅ **Answer: B** – Fargate is a serverless compute engine for containers. AWS manages the underlying infrastructure, and you only define CPU/memory requirements.

---

**Q14.** How does ECS with Auto Scaling know when to scale out a service?

- A. By monitoring the number of ALB requests only
- B. Using AWS Application Auto Scaling policies (target tracking, step scaling, scheduled)
- C. By manually triggering scaling from the console
- D. Auto Scaling is not supported for ECS

> ✅ **Answer: B** – AWS Application Auto Scaling supports ECS services with target tracking (CPU/memory), step scaling, and scheduled scaling policies.

---

**Q15.** Which of the following best describes Amazon EKS?

- A. A proprietary AWS container runtime
- B. A managed Kubernetes service that runs open-source Kubernetes on AWS
- C. A serverless function execution service
- D. A container image registry

> ✅ **Answer: B** – EKS is a managed Kubernetes service. AWS manages the control plane while you manage worker nodes or use Fargate for serverless pods.

---

**Q16.** What is a key advantage of using AWS Lambda over EC2 for event-driven workloads?

- A. Lambda supports more instance types
- B. Lambda provides dedicated server capacity
- C. Lambda runs code without provisioning servers, scaling automatically per event
- D. Lambda is cheaper only for long-running jobs

> ✅ **Answer: C** – Lambda is serverless; you pay only for compute time consumed, and it scales automatically in response to triggers.

---

**Q17.** What happens when a Lambda function reaches its concurrency limit?

- A. AWS automatically increases the limit
- B. Lambda queues the requests indefinitely
- C. New invocations are throttled and receive a 429 error
- D. The function is terminated and restarted

> ✅ **Answer: C** – When concurrency limits are reached, Lambda throttles new requests with HTTP 429 (Too Many Requests). You can request a limit increase or use reserved concurrency.

---

**Q18.** A developer wants to limit Lambda function concurrency to prevent it from consuming all available account concurrency. What feature should they use?

- A. Provisioned Concurrency
- B. Reserved Concurrency
- C. Burst Concurrency
- D. Throttle Policy

> ✅ **Answer: B** – Reserved Concurrency allocates a specific portion of the account concurrency pool to a function and acts as both a guarantee and a cap.

---

**Q19.** An e-commerce application needs to route `/api/orders` to an Orders microservice and `/api/products` to a Products microservice, both hosted on ECS, behind a single ALB. Which ALB feature enables this?

- A. Host-based routing
- B. IP-based routing
- C. Path-based routing
- D. Header-based routing

> ✅ **Answer: C** – ALB path-based routing evaluates the URL path and forwards traffic to different target groups based on path patterns.

---

**Q20.** What is the function of an EC2 Auto Scaling Group (ASG) integrated with a Load Balancer?

- A. It statically assigns IPs to instances
- B. It automatically registers new instances as targets and removes unhealthy ones
- C. It converts EC2 instances into Lambda functions
- D. It monitors S3 bucket activity

> ✅ **Answer: B** – ASG integrates with Load Balancers so newly launched instances are automatically registered as healthy targets, and terminated/failed ones are deregistered.

---

## Part 2: 10 Use Case Based Scenarios

---

### Scenario 1: E-Commerce Flash Sale (EC2 + ASG + ALB)

**Context:** A fashion retail company launches a flash sale every Friday at 6 PM IST. During the sale, traffic spikes to 50x normal load for about 2 hours, then drops sharply.

**Question:** How would you architect a cost-effective, highly available solution that can handle the spike without over-provisioning?

**Solution:**
- Deploy EC2 instances in an **Auto Scaling Group (ASG)** across multiple Availability Zones.
- Use a **Scheduled Scaling Policy** to pre-warm instances 15 minutes before 6 PM every Friday.
- Place an **Application Load Balancer (ALB)** in front to distribute traffic evenly.
- Use **On-Demand Instances** as a baseline, and augment with **Spot Instances** (via a mixed instance policy in ASG) during the surge for additional cost savings.
- After the sale ends, ASG scale-in policies reduce instance count automatically.

**Key Services:** EC2, ASG, ALB, CloudWatch, Spot Instances

---

### Scenario 2: Nightly Batch Data Processing (EC2 Spot + Reserved)

**Context:** A logistics company processes shipping data from 2 AM–5 AM daily. The job is fault-tolerant and can be restarted from checkpoints if interrupted.

**Question:** What is the most cost-effective EC2 purchasing strategy?

**Solution:**
- Use **Spot Instances** for the batch processing workload since they offer up to 90% savings and the job is fault-tolerant (can resume from checkpoints on interruption).
- Configure **Spot Instance interruption handlers** to checkpoint progress to S3.
- Use a **Spot Fleet** with multiple instance types and AZs to minimize interruption probability.
- If some portion of the job must always run (e.g., a coordinator node), use a **Reserved Instance** or **Savings Plan** for that node.

**Key Services:** EC2 Spot Fleet, S3 (checkpointing), Auto Scaling, CloudWatch Events

---

### Scenario 3: Multi-Tenant SaaS Application (ALB Path-Based Routing + ECS)

**Context:** A SaaS platform has three services: Authentication (`/auth`), Billing (`/billing`), and Analytics (`/analytics`). Each needs independent scaling and deployment.

**Question:** How do you expose all three via a single domain and load balancer?

**Solution:**
- Deploy each service as a separate **ECS Fargate** task/service.
- Create three separate **Target Groups** — one per service.
- Configure a single **ALB** with **path-based routing rules**:
  - `/auth/*` → Auth Target Group
  - `/billing/*` → Billing Target Group
  - `/analytics/*` → Analytics Target Group
- Each ECS service has independent **Application Auto Scaling** policies.

**Key Services:** ALB, ECS Fargate, Application Auto Scaling, Target Groups

---

### Scenario 4: Stateful Financial Application (Elastic IP + Private IP)

**Context:** A banking application connects to an external payment gateway that whitelists IPs. The EC2 instance running the payment processor may be stopped/restarted for maintenance.

**Question:** How do you ensure the external gateway always sees the same IP?

**Solution:**
- Allocate an **Elastic IP** and associate it with the payment processor EC2 instance.
- The Elastic IP remains the same even after stop/start cycles, satisfying the gateway's IP whitelist requirement.
- Internal services communicate via **Private IPs** within the VPC for security and performance.
- Apply a **Security Group** that allows outbound traffic only to the gateway's IP range, following the principle of least privilege.

**Key Services:** Elastic IP, Security Groups, VPC, EC2

---

### Scenario 5: Fraud Detection at Scale (Partition Placement + Storage Optimized EC2)

**Context:** A bank runs a Hadoop-based fraud detection engine on 300 EC2 nodes. Regulatory requirements demand that a single hardware failure must not impact more than 20% of nodes.

**Question:** How do you design the cluster placement for fault isolation?

**Solution:**
- Use a **Partition Placement Group** with 5 partitions (each holding ~60 nodes).
- Each partition maps to separate underlying hardware racks in AWS.
- If one rack fails, only that partition (~60 nodes, or 20%) is affected; the other 4 partitions continue processing.
- Use **Storage Optimized (I3/D3) instances** for high sequential read/write throughput required by Hadoop.
- Pair with **EC2 Auto Scaling** to automatically replace failed nodes.

**Key Services:** EC2 Partition Placement Group, Storage Optimized Instances, ASG

---

### Scenario 6: Microservices Migration (ECS Fargate + EKS)

**Context:** A fintech startup has 20 microservices. Their DevOps team is experienced with Kubernetes, and they want to run containers on AWS without re-learning a new orchestration system.

**Question:** ECS Fargate or EKS — which is the right choice, and why?

**Solution:**
- Since the team is already **standardized on Kubernetes**, **Amazon EKS** is the right choice for portability and familiar tooling (kubectl, Helm, etc.).
- EKS manages the **Kubernetes control plane** (updates, patching, HA across AZs); the team manages worker nodes.
- For teams that want zero infrastructure management, EKS + **Fargate** profiles can be used for serverless pod execution.
- Integrate with **IAM** for fine-grained access control and **CloudWatch Container Insights** for observability.

**Key Services:** EKS, Fargate (for pods), IAM, CloudWatch, ALB Ingress Controller

---

### Scenario 7: Serverless Event Processing (Lambda + DynamoDB Streams)

**Context:** An online marketplace inserts thousands of new orders per minute into DynamoDB. Every new order must trigger real-time validation (duplicate check, fraud score) without managing servers.

**Question:** How do you architect this event-driven pipeline?

**Solution:**
- Enable **DynamoDB Streams** on the orders table — this captures all insert/update/delete events as a stream.
- Configure an **AWS Lambda** function as the stream consumer (event source mapping).
- The Lambda function runs the validation logic (duplicate detection, fraud scoring) for each new record.
- If fraud is detected, publish an alert to **Amazon SNS** or write to a separate DynamoDB table.
- No servers to manage; Lambda scales automatically with the stream throughput.

**Key Services:** DynamoDB Streams, AWS Lambda, SNS, CloudWatch (monitoring)

---

### Scenario 8: High-Traffic Gaming Backend (NLB + Cluster Placement Group)

**Context:** A multiplayer online game requires real-time communication between game servers and clients. The architecture needs sub-millisecond latency, static IP addresses for UDP traffic, and handles 1M+ concurrent connections.

**Question:** Which load balancer and EC2 configuration would you recommend?

**Solution:**
- Use a **Network Load Balancer (NLB)** — it operates at Layer 4 (TCP/UDP), provides static IP addresses per AZ, and handles extreme throughput with ultra-low latency.
- Deploy game server EC2 instances in a **Cluster Placement Group** within the same AZ to achieve the lowest inter-node network latency for game state synchronization.
- Use **Compute Optimized (C-family)** EC2 instances for high CPU performance.
- Configure **ASG** to scale game server capacity based on active player count (CloudWatch metric).

**Key Services:** NLB, Cluster Placement Group, EC2 (C-family), ASG, CloudWatch

---

### Scenario 9: Unpredictable ML Inference Workload (ECS Fargate + Auto Scaling)

**Context:** An AI startup runs an image recognition API. Requests are bursty — sometimes idle, sometimes hundreds per second. The team wants per-second billing, no infrastructure management, and automatic scaling.

**Question:** Design the container infrastructure for this workload.

**Solution:**
- Deploy the ML inference service as an **ECS Fargate** task — serverless containers, no EC2 management, per-second billing.
- Place the ECS service behind an **ALB** for HTTPS termination and load distribution.
- Configure **AWS Application Auto Scaling** with a target tracking policy based on **ALB RequestCountPerTarget** to scale ECS tasks up/down automatically.
- Use **ECR (Elastic Container Registry)** to store and version the ML inference Docker image.
- CloudWatch alarms notify the team of scaling events and error rates.

**Key Services:** ECS Fargate, ALB, Application Auto Scaling, ECR, CloudWatch

---

### Scenario 10: Lambda Throttling Under Burst Load (Concurrency Management)

**Context:** A media company uses Lambda to process video upload events from S3. During a viral video campaign, thousands of uploads happen simultaneously. Some critical processing functions start getting throttled, impacting user experience.

**Question:** How do you prevent critical functions from being throttled while protecting non-critical functions from consuming all concurrency?

**Solution:**
- Identify the **critical Lambda function** (e.g., video metadata processing) and assign it **Reserved Concurrency** — this guarantees a fixed number of concurrent executions from the account pool.
- For non-critical functions (e.g., thumbnail generation), set a **Reserved Concurrency cap** to prevent them from consuming all available concurrency.
- Use **Provisioned Concurrency** for the critical function to pre-warm Lambda execution environments and eliminate cold starts during burst.
- Set up **CloudWatch Alarms** on the `Throttles` metric to alert when non-reserved functions are being throttled.
- Consider switching to **SQS + Lambda** event source mapping for burst absorption — SQS queues the events and Lambda processes them at a controlled rate.

**Key Services:** AWS Lambda (Reserved & Provisioned Concurrency), SQS, CloudWatch, S3

---

## Part 3: Leveraging LLMs to Design an AI Data Center (AI DC)

---

### Overview

An AI Data Center (AI DC) is a purpose-built facility optimized for training, fine-tuning, and serving large-scale AI/ML models. Designing one involves complex, interdependent decisions across infrastructure, networking, power, cooling, security, and cost. LLMs can accelerate and enhance virtually every phase of this design process.

---

### 1. Requirements Gathering & Architecture Planning

**How LLMs Help:**
- Parse and synthesize stakeholder requirements (throughput, latency SLAs, model sizes, compliance constraints) from unstructured documents.
- Generate draft architecture blueprints in plain language or structured formats (JSON/YAML).
- Perform trade-off analysis: GPU cluster vs TPU, on-prem vs cloud vs hybrid, air cooling vs liquid cooling.

**Example Prompt:**
> "We need to train a 70B parameter LLM with a 3-week timeline, budget of $2M, with data residency in India. What GPU cluster architecture would you recommend on AWS?"

---

### 2. Infrastructure Sizing & Capacity Planning

**How LLMs Help:**
- Estimate compute (GPU/CPU hours), memory, and storage requirements given model parameters and dataset sizes.
- Calculate FLOP requirements for training runs using standard formulas (Chinchilla scaling laws).
- Suggest EC2 instance types (e.g., p4d.24xlarge, p5.48xlarge, Trn1) or on-prem GPU servers (H100, A100) based on workload.

**Example:**
An LLM can be prompted to compare:
- AWS p4d.24xlarge (8x A100 40GB) vs p5.48xlarge (8x H100 80GB) for a given training job
- Total cost, training time, and memory constraints

---

### 3. Network Topology Design

**How LLMs Help:**
- Design high-bandwidth, low-latency network fabrics for GPU interconnects (NVLink, InfiniBand, EFA on AWS).
- Generate VPC architecture diagrams in text or Terraform/CDK code.
- Recommend placement strategies (Cluster Placement Groups on EC2, UltraClusters) for minimizing inter-GPU communication latency.

**Example:**
> "Design a VPC topology for a 1,024 GPU training cluster on AWS with EFA-enabled networking, private subnets, and a bastion host for admin access."

---

### 4. Power & Cooling Architecture

**How LLMs Help:**
- Estimate Power Usage Effectiveness (PUE) for different cooling strategies (air, liquid, immersion).
- Recommend UPS/generator sizing based on rack density and redundancy requirements.
- Draft RFPs for power infrastructure vendors.
- Summarize best practices for high-density GPU rack deployment (40kW+ per rack).

---

### 5. Storage Architecture

**How LLMs Help:**
- Recommend storage tiers: NVMe SSDs for hot data (checkpoints, active datasets), object storage (S3/Lustre) for cold datasets.
- Design data pipeline architectures: ingestion → preprocessing → training data → model artifact storage.
- Generate IaC (Terraform/CloudFormation) for FSx for Lustre (high-throughput parallel file system used in AI DC workloads on AWS).

---

### 6. MLOps & Orchestration Layer

**How LLMs Help:**
- Design Kubernetes-based (EKS) or Slurm-based job scheduling architectures for multi-tenant GPU clusters.
- Generate Helm charts, Kubernetes manifests, or Slurm job scripts for distributed training (PyTorch DDP, DeepSpeed, Megatron-LM).
- Recommend MLOps tooling: MLflow, Weights & Biases, SageMaker Pipelines, Kubeflow.
- Draft CI/CD pipelines for model training, evaluation, and deployment.

---

### 7. Security & Compliance

**How LLMs Help:**
- Generate IAM policy documents for least-privilege access to GPU instances, S3 datasets, and model registries.
- Draft security architecture documents aligned with compliance frameworks (SOC 2, ISO 27001, DPDP Act for India).
- Identify attack vectors specific to AI DC environments (model exfiltration, training data poisoning) and recommend mitigations.
- Automate security review of IaC templates (detect open security groups, unencrypted storage).

---

### 8. Cost Optimization

**How LLMs Help:**
- Compare cost models: Reserved EC2 GPU instances vs Spot Instances vs SageMaker Training vs on-prem CapEx.
- Generate ROI models and TCO spreadsheets based on utilization assumptions.
- Suggest spot instance interruption strategies for fault-tolerant distributed training.
- Recommend cost allocation tagging strategies for chargeback to internal ML teams.

---

### 9. Monitoring & Observability

**How LLMs Help:**
- Design observability stacks: GPU utilization (DCGM), job queue depths (Prometheus), cost metrics (AWS Cost Explorer).
- Generate CloudWatch dashboards, alarms, and runbooks as code.
- Draft anomaly detection logic for GPU thermal events, network congestion, and storage I/O bottlenecks.

---

### 10. Documentation & Knowledge Management

**How LLMs Help:**
- Auto-generate architecture decision records (ADRs), runbooks, and SOPs from technical discussions.
- Convert infrastructure-as-code into human-readable architecture documentation.
- Build an internal chatbot (RAG-based) so engineers can query the AI DC design knowledge base in natural language.
- Summarize incident post-mortems and extract action items.

---

### LLM-Powered AI DC Design — Summary Table

| Design Dimension | LLM Use Case | Output |
|---|---|---|
| Requirements | Parse docs, generate trade-offs | Architecture brief |
| Compute Sizing | FLOP estimation, instance comparison | Sizing spreadsheet |
| Networking | VPC/EFA topology, IaC generation | Terraform/CDK code |
| Power & Cooling | PUE estimation, vendor RFP drafting | Design specs |
| Storage | Tier recommendations, Lustre IaC | IaC templates |
| MLOps | K8s manifests, Slurm scripts, pipelines | Job scripts |
| Security | IAM policies, compliance mapping | Policy documents |
| Cost | TCO models, Spot strategy | ROI analysis |
| Monitoring | Dashboards, runbooks, alarms | CloudWatch configs |
| Documentation | ADRs, SOPs, internal chatbot | Markdown/Wiki |

---

### Key Takeaway

LLMs don't replace cloud architects or infrastructure engineers — they **multiply their productivity**. By combining domain knowledge (AWS services, HPC networking, ML frameworks) with the generative and summarization capabilities of LLMs, teams can compress weeks of design work into days, reduce errors through automated review, and continuously improve their AI DC architecture through natural language iteration.

---

*Document prepared from AWS Week 1 – PCP Cloud Computing and DevOps content.*

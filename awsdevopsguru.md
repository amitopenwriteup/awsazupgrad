# Amazon DevOps Guru — Beginner Workshop
### ML-powered cloud operations to improve application availability

This is a hands-on, step-by-step workshop for someone who has never used Amazon DevOps Guru before. By the end, you'll have it watching real resources in your AWS account and you'll know how to read its insights.

**Time needed:** ~30–45 minutes
**Cost:** DevOps Guru charges based on resources analyzed (it has a free tier for the first 7 days of analysis on a resource). Remember to clean up at the end if you don't want ongoing charges.

---

## What is DevOps Guru? (60-second version)

DevOps Guru watches your AWS resources (CloudWatch metrics, AWS Config, CloudTrail events, etc.), learns what "normal" looks like, and flags abnormal patterns as **Insights** — before they cause an outage. You don't write any rules; the ML does the pattern-learning.

- **Reactive insight** → something is already going wrong
- **Proactive insight** → something looks like it's *about to* go wrong

---

## Prerequisites

1. An AWS account ([sign up here](https://aws.amazon.com/) if you don't have one)
2. Permissions to use IAM, SNS, and DevOps Guru (an Admin or PowerUser role is easiest while learning)
3. At least one running resource to analyze — e.g. an EC2 instance, Lambda function, or RDS database. If you have nothing running, this workshop will still work but you won't see real insights right away (it can take a few hours to a day for the first insights to appear once analysis is active).

---

## Step 1: Sign in to the AWS Console

1. Go to **https://console.aws.amazon.com/**
2. Sign in with your IAM user (not root, if possible)
3. In the top-right, confirm you're in the **AWS Region** where your resources live (e.g. `us-east-1`). DevOps Guru only analyzes resources in the region you enable it in.

---

## Step 2: Create an SNS Topic for Notifications (optional but recommended)

This lets DevOps Guru email/text you when it finds something.

1. In the search bar at the top, type **SNS** and open the **Simple Notification Service** console
2. In the left sidebar, click **Topics**
3. Click **Create topic**
4. Choose type **Standard**
5. Name it something like `devops-guru-alerts`
6. Click **Create topic**
7. On the topic page, click **Create subscription**
8. Protocol: **Email**, Endpoint: your email address
9. Click **Create subscription**
10. Check your inbox and click **Confirm subscription** in the email AWS sends you

> You can skip this step and add a topic later from inside DevOps Guru.

---

## Step 3: Open the DevOps Guru Console

1. In the top search bar, type **DevOps Guru**
2. Click **Amazon DevOps Guru**
3. You'll land on the DevOps Guru welcome/dashboard page

---

## Step 4: Enable DevOps Guru

1. Click the orange **Get started** (or **Enable DevOps Guru**) button
2. **Choose what to analyze** — you'll see options like:
   - *Analyze all resources in the current account/region* (easiest for a first try)
   - *Analyze only resources in CloudFormation stacks*
   - *Analyze only resources with specific tags*

   For this workshop, choose **"Analyze all AWS resources in my account"** in the current region so you don't have to tag anything manually.

3. **Notifications** — select the SNS topic you created in Step 2 (or create one here)
4. **Optional integrations** — you can leave these unchecked for now:
   - AWS Systems Manager OpsItems
   - AWS CodeGuru Profiler integration
5. Click **Enable**

You'll be returned to the DevOps Guru dashboard, which now shows a status like **"Analyzing"**. Initial analysis/baselining can take a few hours to a few days depending on resource count.

---

## Step 5: Explore the Dashboard While It Learns

While DevOps Guru builds its baseline, look around:

1. **Dashboard** (left nav) — shows a summary: number of resources analyzed, open reactive insights, open proactive insights
2. **Insights** (left nav) — this is empty for now; insights will appear here once anomalies are detected
3. **Settings** (left nav) — review/adjust:
   - Resource coverage (which resources/regions are analyzed)
   - Notification channel (SNS topic)
   - Cost — DevOps Guru shows estimated cost based on resources analyzed

---

## Step 6: (Optional) Generate a Test Insight

If you want to *see* an insight without waiting for a real issue, AWS provides a CloudFormation template that intentionally creates a faulty Lambda + DynamoDB setup so DevOps Guru has something abnormal to detect.

1. Search **CloudFormation** in the console search bar
2. Click **Create stack** → **With new resources**
3. Choose **Template is ready** → **Amazon S3 URL**
4. Use AWS's public sample template for DevOps Guru (search "DevOps Guru CloudFormation sample" in the AWS Samples GitHub if you want the exact link, since AWS periodically updates the URL)
5. Follow the wizard, give the stack a name, and click **Create stack**
6. Wait for the stack status to show **CREATE_COMPLETE**
7. Trigger some load against the sample app (the template's README usually includes a script for this)
8. Come back to DevOps Guru → **Insights** after 10–15 minutes and refresh — you should start seeing a reactive insight appear

> This step is optional. If you'd rather just observe your real resources over the next day, skip straight to Step 7.

---

## Step 7: Read an Insight

Once an insight appears (real or from the test stack):

1. Go to **Insights** in the left nav
2. Click on the insight name to open it
3. Review the tabs:
   - **Aggregated metrics** — graphs showing the anomalous metric(s) and the time window
   - **Graphed anomalies** — visual overlay of "normal" vs "actual" behavior
   - **Recommendations** — DevOps Guru's suggested fix or investigation steps
   - **Relevant events** — CloudTrail events (deployments, config changes) that line up with the anomaly's start time, helping you correlate "what changed" with "what broke"

4. Note the **Severity** (High / Medium / Low) shown at the top — use this to triage

---

## Step 8: Take Action

Depending on what the insight tells you, you might:

- Roll back a recent deployment shown in **Relevant events**
- Scale a resource (e.g. increase Lambda memory, RDS instance class)
- Open the linked **OpsItem** (if you enabled Systems Manager integration) to track remediation
- Click **Provide insight feedback** at the bottom — this helps DevOps Guru's ML get better over time for your account

---

## Step 9: Clean Up (Important!)

To avoid ongoing charges after the workshop:

1. **Stop the test CloudFormation stack** (if you created one in Step 6):
   - Go to **CloudFormation** → select your stack → **Delete**
2. **Turn off DevOps Guru analysis** (if you don't want to keep paying for analysis):
   - Go to **DevOps Guru** → **Settings** → **Manage AWS resources to analyze**
   - Choose **None** as your resource coverage, or remove specific resources/tags
3. **Delete the SNS topic** (optional):
   - Go to **SNS** → **Topics** → select your topic → **Delete**

---

## Quick Reference: Navigation Cheatsheet

| Where you want to go | Path |
|---|---|
| Enable DevOps Guru | Console search → "DevOps Guru" → **Get started** |
| Change which resources are analyzed | DevOps Guru → **Settings** → **Manage AWS resources to analyze** |
| View open insights | DevOps Guru → **Insights** |
| Change notification topic | DevOps Guru → **Settings** → **Notifications** |
| Stop being charged | Settings → set coverage to **None**, or delete unused stacks/resources |

---

## What's Next

- Try connecting DevOps Guru insights to **Systems Manager OpsCenter** so issues become trackable tickets automatically
- Explore **DevOps Guru for RDS**, a specialized mode for database performance anomalies (needs RDS Performance Insights turned on)
- Set up **multi-account aggregation** if you manage several AWS accounts under AWS Organizations, so you can see insights across all of them in one dashboard

For the most current setup options (the console UI does change over time), check AWS's official guide: https://docs.aws.amazon.com/devops-guru/latest/userguide/setting-up.html

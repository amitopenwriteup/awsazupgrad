# Lab: Working with Amazon DynamoDB

**Duration:** 45 minutes
**Level:** Beginner
**Topic area:** AWS Databases (Week 3 - Amazon DynamoDB)

## Objectives

By the end of this lab, you will be able to:

- Create a DynamoDB table with a partition key and sort key
- Add, view, and edit items using the console
- Query and scan a table
- Enable DynamoDB Streams
- Enable Point-in-Time Recovery (PITR) for backups
- Understand the difference between On-Demand and Provisioned capacity modes
- Clean up all created resources

## Prerequisites

- An AWS account with console access
- IAM permissions for DynamoDB (AmazonDynamoDBFullAccess or equivalent)
- A web browser

## Time Breakdown

| Step | Task | Time |
|------|------|------|
| 1 | Sign in and open DynamoDB console | 2 min |
| 2 | Create a table | 5 min |
| 3 | Add items to the table | 8 min |
| 4 | Query and scan the table | 8 min |
| 5 | Enable DynamoDB Streams | 5 min |
| 6 | Enable Point-in-Time Recovery | 5 min |
| 7 | Compare capacity modes | 7 min |
| 8 | Clean up resources | 5 min |

Total: 45 minutes

---

## Step 1: Sign in and Open the DynamoDB Console (2 minutes)

1. Sign in to the AWS Management Console.
2. In the search bar at the top, type **DynamoDB** and select it from the results.
3. Confirm you are in your intended AWS Region (shown in the top right corner). Use the same region for the entire lab.

---

## Step 2: Create a Table (5 minutes)

1. In the DynamoDB console, select **Tables** from the left navigation pane.
2. Click **Create table**.
3. Enter the following details:
   - Table name: `StudentActivity`
   - Partition key: `StudentID` (String)
   - Sort key: `ActivityDate` (String)
4. Under **Table settings**, choose **Default settings**.
5. Click **Create table**.
6. Wait for the table status to change from **Creating** to **Active** before continuing.

---

## Step 3: Add Items to the Table (8 minutes)

1. Click on the `StudentActivity` table name to open it.
2. Select the **Explore table items** tab.
3. Click **Create item**.
4. Add the following attributes and values:
   - StudentID: `S001`
   - ActivityDate: `2026-07-01`
   - ActivityType: `Login`
   - DurationMinutes: `15`
5. Click **Create item** to save.
6. Repeat the process to add two more items:
   - StudentID: `S001`, ActivityDate: `2026-07-02`, ActivityType: `Assignment`, DurationMinutes: `45`
   - StudentID: `S002`, ActivityDate: `2026-07-01`, ActivityType: `Login`, DurationMinutes: `10`
7. Confirm all three items appear in the **Explore table items** view.

---

## Step 4: Query and Scan the Table (8 minutes)

1. In the **Explore table items** tab, select **Query**.
2. Under Partition key, enter `S001` and click **Run**.
3. Observe that only items belonging to `S001` are returned, sorted by `ActivityDate`.
4. Now select **Scan** instead of Query.
5. Click **Run** and observe that all items in the table are returned regardless of partition key.
6. Note the difference:
   - Query: retrieves items matching a specific partition key (and optionally a sort key condition). Efficient and low cost.
   - Scan: reads every item in the table. Useful for full-table analysis but more expensive and slower on large tables.

---

## Step 5: Enable DynamoDB Streams (5 minutes)

1. Open the `StudentActivity` table.
2. Select the **Exports and streams** tab.
3. Under **DynamoDB stream details**, click **Turn on**.
4. Choose the view type: **New and old images**.
5. Click **Turn on stream**.
6. Confirm the stream status now shows as **Enabled**.
7. Note: In a production setup, this stream could trigger an AWS Lambda function to process item-level changes in real time (for example, sending a notification whenever a student logs an activity).

---

## Step 6: Enable Point-in-Time Recovery (5 minutes)

1. Open the `StudentActivity` table.
2. Select the **Backups** tab.
3. Under **Point-in-time recovery (PITR)**, click **Edit**.
4. Turn the toggle **On**.
5. Click **Save changes**.
6. Confirm the status now shows PITR as enabled.
7. Note: PITR allows you to restore the table to any point in the last 35 days, which supports compliance and disaster recovery requirements.

---

## Step 7: Compare Capacity Modes (7 minutes)

1. Open the `StudentActivity` table.
2. Select the **Additional settings** tab.
3. Locate the **Read/write capacity settings** section and click **Edit**.
4. Review the two available modes:
   - **Provisioned**: You define Read Capacity Units (RCUs) and Write Capacity Units (WCUs) in advance. Best for predictable, steady traffic and lower cost when usage is consistent.
   - **On-demand**: DynamoDB automatically scales to match traffic and you pay per request. Best for unpredictable or spiky workloads.
5. Select **On-demand** if it is not already selected, and click **Save changes**.
6. Discuss with your group (or note down) which mode would suit the `StudentActivity` table if the number of daily active students varies significantly day to day.

---

## Step 8: Clean Up Resources (5 minutes)

To avoid ongoing charges, remove the resources created in this lab.

1. Go back to **Tables** in the left navigation pane.
2. Select the `StudentActivity` table.
3. Click **Delete**.
4. Type `delete` in the confirmation box when prompted.
5. Click **Delete table**.
6. Confirm the table no longer appears in your table list.

---

## Key Takeaways

- DynamoDB is a fully managed, serverless NoSQL database that scales automatically to handle high request volumes.
- A table requires a partition key, and optionally a sort key, to organize and retrieve items efficiently.
- Query operations are more efficient than Scan operations because they target a specific partition key.
- DynamoDB Streams capture item-level changes and can trigger downstream processing such as AWS Lambda functions.
- Point-in-Time Recovery provides continuous backups for up to 35 days, supporting disaster recovery and compliance needs.
- On-Demand capacity mode suits unpredictable workloads, while Provisioned mode suits steady, predictable workloads at lower cost.

## Optional Extension (if time allows)

- Create a simple AWS Lambda function subscribed to the DynamoDB stream to log each change to Amazon CloudWatch Logs.
- Explore DynamoDB Accelerator (DAX) documentation to understand how it reduces read latency from milliseconds to microseconds for read-heavy workloads.

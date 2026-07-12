# Lab 3: Assign an RBAC Role and Configure a Conditional Access Policy
**Duration:** 30 minutes | **Part of:** LAB 2 (30-min slot) | **Module:** RBAC + Conditional Access

## Objective
- Part A: Assign the **Contributor** RBAC role to a user at a resource group scope.
- Part B: Create a **Conditional Access** policy that requires MFA for a target group.

## Prerequisites
- Completion of **Lab 1** and **Lab 2** (user "Priya Sharma" and group "Finance-Team" must exist)
- Access to the **Azure Portal** (https://portal.azure.com) with **User Access Administrator** or **Owner** role on a resource group
- Access to the **Microsoft Entra admin center** with **Conditional Access Administrator** or **Global Administrator** role
- Azure AD Premium P1 license (required for Conditional Access) on the tenant
- A test resource group (create one named `rg-finance-lab` if none exists)

---

## Part A: Assign an RBAC Role (15 min)

### Scenario
Priya Sharma needs to manage (start/stop/configure) resources in the Finance department's resource group, but should **not** be able to delete the resource group or manage billing.

### Step 1: Navigate to the Resource Group
1. Sign in to **https://portal.azure.com**.
2. Search for and select **Resource groups**.
3. Select (or create) `rg-finance-lab`.

### Step 2: Open Access Control (IAM)
1. In the left menu of the resource group, select **Access control (IAM)**.
2. Select **+ Add** > **Add role assignment**.

### Step 3: Select the Role
1. On the **Role** tab, search for and select **Contributor**.
2. Click **Next**.

### Step 4: Assign to the User
1. On the **Members** tab, keep **Assign access to: User, group, or service principal**.
2. Click **+ Select members**.
3. Search for **Priya Sharma** and select her.
4. Click **Select**, then **Next**.

### Step 5: Review and Assign
1. Review the summary: Role = **Contributor**, Scope = `rg-finance-lab`, Member = Priya Sharma.
2. Click **Review + assign** (twice to confirm).

### Step 6: Validate the Assignment
1. Go back to **Access control (IAM)** > **Role assignments** tab.
2. Confirm **Priya Sharma — Contributor** appears scoped to `rg-finance-lab`.

**Validation Checklist — Part A**
- [ ] Priya Sharma has the **Contributor** role
- [ ] The role is scoped to the resource group only (not subscription-wide)
- [ ] Role assignment is visible under **Access control (IAM)** > **Role assignments**

---

## Part B: Configure a Conditional Access Policy (15 min)

### Scenario
Your security team requires that all members of the **Finance-Team** group use MFA whenever they sign in, to protect sensitive financial data.

### Step 1: Navigate to Conditional Access
1. Go to **https://entra.microsoft.com**.
2. In the left navigation pane, select **Protection** > **Conditional Access**.
3. Select **+ Create new policy**.

### Step 2: Name the Policy
1. **Name:** `Require MFA - Finance Team`

### Step 3: Configure Assignments — Users
1. Under **Users**, select **Include** > **Select users and groups**.
2. Check **Users and groups**, then search for and select **Finance-Team**.
3. Click **Select**.

### Step 4: Configure Assignments — Target Resources
1. Under **Target resources**, select **Include** > **All cloud apps** (or select specific apps for a more scoped demo).

### Step 5: Configure Access Controls
1. Under **Access controls** > **Grant**, select **Grant access**.
2. Check **Require multifactor authentication**.
3. Click **Select**.

### Step 6: Enable the Policy Safely
1. Under **Enable policy**, select **Report-only** (recommended in a shared/training tenant to avoid locking anyone out).
2. Click **Create**.

> ⚠️ **Trainer note:** In a real production rollout, always test in **Report-only** mode first and review sign-in logs before switching to **On**. Never set to **On** in a shared lab tenant without confirming no one gets locked out.

### Step 7: Validate the Policy
1. Go to **Conditional Access** > **Policies**, confirm the new policy is listed with state **Report-only**.
2. (Optional, if time allows) Go to **Protection** > **Conditional Access** > **Insights and reporting**, and show where sign-in results against this policy would appear.

---

## Validation Checklist — Part B
- [ ] Policy `Require MFA - Finance Team` exists
- [ ] Assigned users/groups = **Finance-Team**
- [ ] Grant control = **Require multifactor authentication**
- [ ] Policy state = **Report-only** (or **On**, only if explicitly instructed in production)

## Common Issues
| Issue | Fix |
|---|---|
| Conditional Access option not visible | Tenant lacks Azure AD Premium P1/P2 — check licensing |
| Can't find "Finance-Team" while assigning policy | Confirm Lab 2 was completed and group name matches exactly |
| Worried about being locked out during testing | Always start new policies in **Report-only** mode |
| Role assignment "Next" button greyed out | Ensure a role is actually selected on the Role tab before proceeding |

## Clean-up
- Set the Conditional Access policy to **Disabled** or delete it after the session if the tenant will be reused.
- Optionally remove the Contributor role assignment and delete `rg-finance-lab`, `Priya Sharma`, and `Finance-Team` if the tenant is shared across multiple training batches.

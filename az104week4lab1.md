# Lab 1: Create and License an Azure AD (Entra ID) User
**Duration:** 15 minutes | **Part of:** LAB 1 (30-min slot) | **Module:** Azure AD Users

## Objective
Create a new cloud-only user in Azure AD (Entra ID), configure key attributes, and assign a license — simulating onboarding a new employee.

## Prerequisites
- Access to the [Microsoft Entra admin center](https://entra.microsoft.com) or [Azure Portal](https://portal.azure.com)
- A role of at least **User Administrator** (Global Administrator also works)
- An available license (e.g., Microsoft 365 or Azure AD Premium P1/P2) in the tenant

## Scenario
Your organization has hired a new employee, **Priya Sharma**, joining the Finance department. You need to create her digital identity so she can sign in and access company resources on her first day.

---

## Steps

### Step 1: Sign in to the Admin Center
1. Go to **https://entra.microsoft.com**.
2. Sign in with an account that has User Administrator or Global Administrator privileges.

### Step 2: Navigate to Users
1. In the left navigation pane, select **Identity** > **Users** > **All users**.
2. Select **+ New user** > **Create new user**.

### Step 3: Configure Basic User Details
1. Under **Identity**, fill in:
   - **User principal name:** `priya.sharma@<yourtenant>.onmicrosoft.com`
   - **Display name:** `Priya Sharma`
   - **First name:** `Priya`
   - **Last name:** `Sharma`
2. Under **Password**, choose **Auto-generate password** (note it down — it's shown only once) or set a custom initial password.

### Step 4: Set Properties
1. Select the **Properties** tab.
2. Under **Job information**, set:
   - **Job title:** `Financial Analyst`
   - **Department:** `Finance`
3. Under **Settings**, confirm **Usage location** is set (e.g., `India` or your country) — this is **required** before a license can be assigned.

### Step 5: Assign a License
1. Select the **Assignments** tab (or go back to the user after creation > **Licenses**).
2. Select **+ Assign license**.
3. Choose an available license (e.g., **Microsoft 365 E3** or **Azure AD Premium P1**).
4. Click **Select**, then **Review + create**.

### Step 6: Create the User
1. Review all tabs (Basics, Properties, Assignments).
2. Click **Create**.
3. Confirm the user **Priya Sharma** now appears in **All users**.

### Step 7 (Optional — if time allows): Enable SSPR for the User
1. Go to **Identity** > **Users** > **All users** > **Password reset** > **Properties**.
2. Under **Self service password reset enabled**, select **Selected** or **All**, and add Priya's group/user if scoped.
3. Save the configuration.

---

## Validation Checklist
- [ ] User "Priya Sharma" exists under **All users**
- [ ] UPN follows the `firstname.lastname@domain` convention
- [ ] Department and Job title are set under Properties
- [ ] A license shows as **Assigned** under the user's Licenses blade
- [ ] Usage location is set (required for licensing)

## Common Issues
| Issue | Fix |
|---|---|
| Can't assign a license — option is greyed out | Set the **Usage location** first under user Properties |
| "Insufficient privileges" error | Confirm you're signed in with User Administrator or Global Administrator role |
| User not visible immediately in list | Refresh the page — Azure AD directory replication can take a few seconds |

## Clean-up (if reusing tenant for multiple sessions)
- Do **not** delete the user yet — it will be reused in **Lab 3** for RBAC role assignment and Conditional Access.

# Lab 2: Create a Security Group and Add Members
**Duration:** 15 minutes | **Part of:** LAB 1 (30-min slot) | **Module:** Groups in Azure AD

## Objective
Create an Azure AD Security Group and add the user created in Lab 1 as a member — simulating how organizations manage access at scale.

## Prerequisites
- Completion of **Lab 1** (user "Priya Sharma" must exist)
- Access to the [Microsoft Entra admin center](https://entra.microsoft.com)
- A role of at least **Groups Administrator** or **User Administrator**

## Scenario
Rather than granting file-share and app access to each employee individually, your organization uses **Security Groups**. You will create a "Finance-Team" group and add Priya Sharma to it.

---

## Steps

### Step 1: Navigate to Groups
1. Sign in to **https://entra.microsoft.com**.
2. In the left navigation pane, select **Identity** > **Groups** > **All groups**.
3. Select **+ New group**.

### Step 2: Configure Group Basics
1. **Group type:** Select **Security**.
2. **Group name:** `Finance-Team`
3. **Group description:** `Security group for Finance department access`
4. **Membership type:** Select **Assigned** (leave as Dynamic User only if you want to demonstrate dynamic rules — see Step 5 optional).

### Step 3: Add Members
1. Under **Members**, click **No members selected**.
2. Search for **Priya Sharma** and select her.
3. Click **Select**.

### Step 4: Create the Group
1. Review the group settings.
2. Click **Create**.
3. Confirm **Finance-Team** appears under **All groups**.

### Step 5 (Optional — dynamic membership demo, if time allows)
1. Create a second group named `Finance-Team-Dynamic`.
2. Set **Membership type** to **Dynamic User**.
3. Click **Add dynamic query**, and set a rule:
   - Property: `department`
   - Operator: `Equals`
   - Value: `Finance`
4. Save the rule and create the group.
5. Note: dynamic membership evaluation can take a few minutes to populate.

### Step 6: Verify Group Membership
1. Open **Finance-Team** from **All groups**.
2. Select **Members**.
3. Confirm **Priya Sharma** is listed.

---

## Validation Checklist
- [ ] Group "Finance-Team" exists with type **Security**
- [ ] Priya Sharma appears under the group's **Members**
- [ ] (Optional) Dynamic group rule correctly targets `department eq Finance`

## Common Issues
| Issue | Fix |
|---|---|
| Can't select "Dynamic User" membership type | Requires Azure AD Premium P1/P2 license on the tenant |
| Member not found while searching | Confirm the user was fully created in Lab 1 and directory sync has completed |
| Dynamic group shows 0 members after a few minutes | Recheck the rule syntax and confirm the user's `department` attribute is set to `Finance` |

## Clean-up (if reusing tenant for multiple sessions)
- Keep **Finance-Team** and **Priya Sharma** — both are reused in **Lab 3**.

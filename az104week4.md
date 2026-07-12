# AZ-104 Session 4 — Speaker Script
## Manage Identities in Azure (Entra ID)
**How to use this doc:** Read each slide's script almost verbatim, or use it as a close guide. Cues like *[Pause]*, *[Click]*, *[Demo]*, *[Ask]* tell you when to advance, pause for questions, or switch to the portal. Timings match the Trainer Flow document.

---

## Slide 1 — Title: AZ-104 Training, Session 4: Manage Identities in Azure (Entra ID)
**Time: 0:00–0:05 (5 min)**

> "Welcome back, everyone. This is Session 4 of our AZ-104 series, and today we're talking about one of the most heavily tested — and honestly one of the most practical — parts of the whole exam: **managing identities in Azure**, using what Microsoft now calls **Entra ID**, but what most of us still call Azure Active Directory, or Azure AD.

> Before we dive in, I want to give you one scenario to keep in your head for the next two hours. Imagine it's Monday morning. A new employee — let's call her Priya — joins your company. By 9 AM, she needs to log in, access her email, get into the right Finance team files, and do all of that securely. Nobody wants to spend three days provisioning one person's access.

> By the end of today, you'll know exactly how to do that — create her identity, put her in the right group, give her exactly the right level of access, and protect her sign-in with MFA. That's the thread that ties everything together today.

> We've got two hands-on labs built into this session, about 30 minutes each, so you'll actually build this scenario yourself, not just watch me click through slides. Let's get started."

*[Pause 5 seconds, check chat/room for readiness, then advance]*

---

## Slide 2 — Introduction to Azure AD (Entra ID)
**Time: 0:05–0:10 (5 min)**

> "So what actually is Azure AD — or Entra ID? At its core, it's Microsoft's **cloud-based identity and access management platform**. Think of it as the bouncer and the ID-checker for everything in your Microsoft cloud ecosystem — Azure resources, Microsoft 365, and thousands of SaaS apps that plug into it.

> It does four big jobs for us:
> First, **authentication** — proving you are who you say you are.
> Second, **authorization** — deciding what you're allowed to do once you're in.
> Third, **single sign-on**, or SSO — sign in once, get access to everything you're entitled to, without re-entering credentials for every single app.
> And fourth, **multi-factor authentication** — an extra layer of proof beyond just a password.

> Here's a use case that makes this real: imagine a company migrating off an old on-premises identity system. Their employees currently need separate logins for email, for Salesforce, for their HR system, for Azure itself. With Entra ID as the central identity provider, that same employee logs in **once**, and SSO carries that authentication across every connected app. That's not just convenience — it's fewer passwords floating around, and fewer attack surfaces for a hacker to target.

> Everything else we talk about today — users, groups, roles, MFA, conditional access — all of it sits on top of this one core service."

*[Click to next slide]*

---

## Slide 3 — Identity Types in Azure AD
**Time: 0:10–0:15 (5 min)**

> "Now, not every identity in Azure AD comes from the same place. There are three types you need to know cold for the exam.

> **Number one: cloud-only identities.** These are created and managed directly inside Azure AD — no on-premises system involved at all. Think of a startup that was born in the cloud; they never had a physical Active Directory server.

> **Number two: synchronized identities.** This is for organizations that already have an on-premises Active Directory and want to extend it into the cloud. They use a tool called **Azure AD Connect** to sync those existing accounts up to Entra ID. This is very common in large, established enterprises that aren't going to rip out 15 years of on-prem infrastructure overnight.

> **Number three: guest accounts, also called B2B — business-to-business.** This is for external collaboration. Say you need to give an outside auditor or a vendor access to one specific SharePoint site for two weeks. You don't want to create a full internal employee account for them — you invite them as a **guest**, and you can scope exactly what they can see.

> *[Ask the room]* Quick question for you — if a hospital wants their radiologist, who works for a partner imaging company, to see specific patient scan folders for a joint project, which identity type is that? *[Pause for answers]* Right — that's a guest, B2B account.

> Knowing which of these three types fits a scenario is a very common exam pattern — they'll describe the business situation and ask you to pick the identity type."

*[Click to next slide]*

---

## Slide 4 — Azure AD Users
**Time: 0:15–0:20 (5 min)**

> "Let's get into actually creating users, because this is what we'll do together in a few minutes in Lab 1.

> You can create users three ways: through the **Azure Portal** — or specifically the Entra admin center — through **Azure CLI**, or through **PowerShell**. In the real world, if you're onboarding one person, you'll use the portal. If you're onboarding 500 people from a CSV export from HR, you're scripting it with PowerShell or CLI.

> A few key attributes every user has: the **username**, the **UPN** — or User Principal Name, which looks like an email address, e.g. `priya.sharma@company.com` — and their **profile** information, like job title and department.

> One feature worth calling out specifically: **Self-Service Password Reset**, or **SSPR**. This lets a user reset their own forgotten password — through a registered phone number or alternate email — without ever calling the help desk. If you've ever worked an IT help desk, you know password resets are one of the single biggest ticket categories. SSPR can cut that dramatically.

> In a minute, we're going to create a real user — Priya Sharma — assign her some attributes, and give her a license. That's exactly what Lab 1 covers.

> *[Demo — optional, 2 min]* Let me show you live in the portal what this looks like before you try it yourselves..."

*[Switch to portal for live demo if time allows, then click to next slide]*

---

## Slide 5 — Groups in Azure AD
**Time: 0:20–0:25 (5 min)**

> "Now, imagine you had to manage access one user at a time for every single resource. Two hundred employees, each individually granted access to the Finance file share — that's a nightmare to maintain, and it's incredibly easy to make mistakes. This is where **groups** come in.

> There are two main group types: **Security Groups**, which are purely for access control — deciding who gets into what — and **Microsoft 365 Groups**, which are more about collaboration, giving a team a shared mailbox, calendar, and Teams space.

> And there are two ways to populate a group: **Assigned membership**, where you manually add and remove people, and **Dynamic membership**, where Azure AD automatically adds or removes users or devices based on a rule — like 'anyone whose department attribute equals Finance.'

> Here's the use case: instead of granting file-share access to 200 individual people, you grant it once to a group called **Finance-Team**. Now, if you set that group up as **dynamic**, any new hire whose department is set to Finance gets added automatically — no manual step required, no chance of forgetting to add someone.

> This is exactly what we're building in the second half of Lab 1 — we'll create a Finance-Team security group and add Priya to it."

*[Click to next slide — transition into Lab 1]*

---

## 🧪 LAB 1 — Create Users & Security Group
**Time: 0:25–0:55 (30 min)**

> "Alright, this is where you take the wheel. Over the next 30 minutes, you're going to do two things using **Lab Doc 1** and **Lab Doc 2**:

> First — in the next 15 minutes — create a new Azure AD user, just like Priya Sharma from our earlier example, set her job title and department, and assign her a license.

> Then — in the following 15 minutes — create a security group called Finance-Team, and add that user as a member.

> Open Lab Doc 1 now. Work at your own pace — I'll be walking around the room, or available in chat, if you hit any snag. If you get stuck on the license assignment step, the most common issue is forgetting to set the **Usage location** first — that's called out in the troubleshooting table in the doc.

> You've got 30 minutes. Go ahead and get started."

*[Circulate / monitor chat for 30 minutes. With ~2 minutes left, give a time warning: "Two minutes left — make sure your group shows the user as a member before we regroup."]*

**Debrief (last 2 minutes of the slot):**
> "Let's pause there. Can someone share what UPN format you used for your user? ... Good. And can someone confirm — when you opened your Finance-Team group's Members list, did you see your user listed? ... Great, that confirms everyone's on the same page. Let's move on."

---

## Slide 6 — Roles & Role-Based Access Control (RBAC)
**Time: 0:55–1:00 (5 min)**

> "Now that we have identities and groups, the next question is: what can they actually **do**? That's governed by **RBAC — Role-Based Access Control**.

> RBAC gives you fine-grained control over permissions. Azure ships with built-in roles — the big four you must know are: **Owner** (full control, including who else has access), **Contributor** (can manage everything except who has access), **Reader** (view-only), and **User Access Administrator** (can manage access for others, but can't touch the resources themselves).

> Critically, roles can be assigned at **three different scopes**: subscription level, resource group level, or individual resource level. And scope matters enormously.

> Here's the use case: a junior admin needs to restart virtual machines in the Dev resource group. If you make them **Owner at the subscription level**, they could now delete resource groups, change billing, touch production — way too much power. Instead, you assign them **Contributor**, scoped only to that one Dev resource group. They get exactly what they need, nothing more. This idea — the **principle of least privilege** — comes up constantly on the exam and in real security audits.

> This is exactly what we'll practice in Lab 3 in a bit — assigning Contributor to our user at a resource-group scope."

*[Click to next slide]*

---

## Slide 7 — Administrative Units
**Time: 1:00–1:03 (3 min)**

> "Quick one here — **Administrative Units**. Think of these as logical containers that let you delegate admin responsibility over a **subset** of your users or devices, rather than the whole tenant.

> Use case: picture a university with separate IT teams for the Engineering faculty and the Business faculty. You don't want the Engineering IT admin to be able to touch Business faculty student accounts, and vice versa. You create an Administrative Unit per faculty, and scope each admin's role to their own unit. Same idea as scoped RBAC, but specifically for identity management delegation rather than Azure resources.

> This is a smaller, less-detailed exam topic, but it does show up — mainly as a 'what is this used for' style question."

*[Click to next slide]*

---

## Slide 8 — Azure AD Authentication Methods
**Time: 1:03–1:07 (4 min)**

> "Let's talk about **how** Azure AD actually verifies a password when you're in a hybrid environment — meaning you've got both on-prem AD and Azure AD.

> Three methods: **Password Hash Synchronization** — the on-prem password hash gets synced up to Azure AD, and Azure AD checks against that. **Pass-through Authentication** — the password itself never leaves on-premises; Azure AD passes the authentication request back to an on-prem agent to validate it. And **Federation with ADFS** — a separate on-premises federation server handles authentication entirely, and Azure AD just trusts its response.

> Use case: a bank with strict regulatory requirements might not be allowed to have password hashes — even hashed ones — leave their own datacenter. For them, Pass-through Authentication or full ADFS federation is the compliant choice, versus Password Hash Sync.

> Underneath all of this, the actual protocols doing the work are **OAuth 2.0**, **OpenID Connect**, and **SAML** — you don't need to code them, but know that these are the industry-standard protocols Azure AD speaks."

*[Click to next slide]*

---

## Slide 9 — Multi-Factor Authentication (MFA)
**Time: 1:07–1:12 (5 min)**

> "Now, one of the single most important security concepts in this whole course: **Multi-Factor Authentication**, or MFA.

> The idea is simple — a password alone is 'something you know,' and that can be stolen or guessed. MFA adds a second factor: 'something you have' or 'something you are.' Methods include the **Microsoft Authenticator app**, **SMS text codes**, **phone call verification**, and **hardware tokens** like FIDO2 security keys.

> Use case: imagine your company just had a phishing incident where an admin's password was stolen. The security team's response is to require that **all admin accounts** use the Authenticator app specifically — not SMS, because SMS can be intercepted via SIM-swapping attacks — for every single sign-in, no exceptions.

> **Exam tip, and I want you to really lock this in:** MFA can be enabled two different ways — **per-user MFA**, which is an older, simpler on/off switch for a specific user, or through a **Conditional Access policy**, which is the modern, flexible, condition-based way to require it. The exam loves to test whether you know the difference, and which one is being described in a scenario."

*[Click to next slide]*

---

## Slide 10 — Conditional Access Policies
**Time: 1:12–1:17 (5 min)**

> "This leads us straight into **Conditional Access** — think of it as an **if-this-then-that** engine for sign-ins.

> The **conditions** it can evaluate include: who the user is, what device they're using, which app they're trying to reach, their location, and even a calculated **sign-in risk level**. The **controls** it can apply include: require MFA, block access entirely, or allow access but with restrictions — like blocking file downloads on an unmanaged device.

> Use case: Your Admins group should be able to sign in from the corporate office without much friction, but if the exact same admin tries to sign in from a country you've never done business in, you might want to **block it outright** — or at minimum require MFA plus a compliant, managed device. One single Conditional Access policy, layered conditions, can express all of that.

> In Lab 2, coming up shortly, we'll build a real Conditional Access policy that requires MFA for our Finance-Team group — the exact scenario you'd use to protect a sensitive department."

*[Click to next slide — transition into Lab 2]*

---

## 🧪 LAB 2 — Assign RBAC Role & Configure Conditional Access
**Time: 1:17–1:47 (30 min)**

> "Time for our second lab, using **Lab Doc 3**. This one has two parts, both building on the user and group you already created.

> **Part A, 15 minutes:** you'll assign the Contributor role to your user, scoped specifically to a resource group — not the whole subscription. This is the least-privilege pattern we talked about earlier.

> **Part B, 15 minutes:** you'll create a Conditional Access policy that requires MFA for anyone in your Finance-Team group.

> One important safety note before you start: when you get to the 'Enable policy' step, set it to **Report-only**, not On. In a shared training tenant, an 'On' policy could lock people out mid-lab. Report-only lets you see exactly what *would* happen without actually enforcing it — that's genuinely how real Azure admins test policies in production too, so you're learning a real best practice, not just a lab shortcut.

> Go ahead and open Lab Doc 3. Thirty minutes, starting now."

*[Circulate / monitor for 30 minutes. Time warning at 2 minutes remaining: "Two minutes left — confirm your Conditional Access policy shows up under Policies with state Report-only."]*

**Debrief (last 2 minutes of the slot):**
> "Let's check in. Show of hands — who successfully sees their Contributor role assignment under Access control on the resource group? ... Good. And who has their Conditional Access policy listed in Report-only mode? ... Excellent. In a real environment, your next step after a few days in Report-only would be reviewing the sign-in logs — which is actually our very next topic."

---

## Slide 11 — Identity Protection
**Time: 1:47–1:51 (4 min)**

> "Let's talk about how Azure AD proactively catches trouble before it becomes a breach — this is **Identity Protection**.

> It uses Microsoft's threat intelligence to detect **risky sign-ins** — like a login from an impossible travel location, or from a known malicious IP — and **risky users** — accounts showing signs of compromise. It can also trigger **automated remediation**, and it gives you risk event reports so you can investigate patterns over time.

> Use case: say Priya's credentials show up in a public data breach dump — this happens more often than people think, since breach data gets recirculated online. Identity Protection can automatically flag her account as a 'risky user' and **force a password reset** the next time she tries to sign in, cutting off a potential attacker before they can do damage — all without a human having to manually notice and intervene."

*[Click to next slide]*

---

## Slide 12 — Monitoring & Troubleshooting
**Time: 1:51–1:55 (4 min)**

> "Last technical topic before we wrap up: how do you actually **see** what's happening in your identity environment?

> **Sign-in logs** track every authentication attempt — who signed in, from where, on what device, and whether MFA was satisfied. **Audit logs** track administrative changes — who created a user, who added someone to a group, who changed a Conditional Access policy. **Access Reviews** let you periodically ask 'does this person still need this access?' — and **Azure Monitor integration** lets you build alerts on top of all of this data.

> Use case: a Finance manager needs to certify every quarter that everyone still in the Finance-Team group actually still needs to be there. Instead of manually emailing a spreadsheet around, you set up an **Access Review** that automatically notifies the manager, collects their approve/deny decisions, and removes anyone who doesn't respond within 30 days. This is a very real compliance pattern — auditors ask for exactly this kind of evidence."

*[Click to next slide]*

---

## Slide 13 — Summary & Exam Tips
**Time: 1:55–2:00 (5 min)**

> "Let's bring it all the way back to where we started. Remember Priya, our new hire on Monday morning?

> By now, you know exactly how to handle her onboarding end to end: create her **user** account, put her in the right **group** so she inherits access automatically, assign her the right **RBAC role** at the right scope so she has exactly the permissions she needs — no more, no less — and protect her sign-in with **MFA** through a **Conditional Access** policy. And if anything ever looks suspicious, **Identity Protection** and your **logs** have you covered.

> To summarize the must-knows for the exam:
> - Azure AD, or Entra ID, is the core identity foundation everything else sits on.
> - Users, Groups, and Roles together form your access foundation — know the identity types and know your built-in roles.
> - MFA and Conditional Access are two of the most heavily tested topics in this entire exam — know the difference between per-user MFA and Conditional Access-based MFA cold.
> - Logs and monitoring — sign-in logs versus audit logs — come up constantly, usually as 'which log would you check' scenario questions.

> Great work today, everyone — you didn't just hear about this, you actually built a working example of it yourselves in the labs. Next session, we move into **Governance and Compliance**, which builds directly on everything we covered today.

> Any final questions before we close out?"

*[Open floor for Q&A, then end session]*

---

## Quick Reference — Timing Recap

| Slide/Segment | Time | Duration |
|---|---|---|
| Slide 1 – Welcome | 0:00–0:05 | 5 min |
| Slide 2 – Intro to Azure AD | 0:05–0:10 | 5 min |
| Slide 3 – Identity Types | 0:10–0:15 | 5 min |
| Slide 4 – Users | 0:15–0:20 | 5 min |
| Slide 5 – Groups | 0:20–0:25 | 5 min |
| **Lab 1** | 0:25–0:55 | 30 min |
| Slide 6 – RBAC | 0:55–1:00 | 5 min |
| Slide 7 – Admin Units | 1:00–1:03 | 3 min |
| Slide 8 – Auth Methods | 1:03–1:07 | 4 min |
| Slide 9 – MFA | 1:07–1:12 | 5 min |
| Slide 10 – Conditional Access | 1:12–1:17 | 5 min |
| **Lab 2** | 1:17–1:47 | 30 min |
| Slide 11 – Identity Protection | 1:47–1:51 | 4 min |
| Slide 12 – Monitoring | 1:51–1:55 | 4 min |
| Slide 13 – Summary | 1:55–2:00 | 5 min |

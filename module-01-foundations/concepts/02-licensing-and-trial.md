# 02 — Licensing & Trial Setup

## The Core Licensing Model

Power Pages licences are based on **users who access your site per month** — not developers who build it. There are two user types:

### Authenticated Users
People who **log in** to your portal (via Microsoft Entra ID, Google, LinkedIn, etc.).

- Counted as unique users per site per month
- If the same person logs in 20 times in a month, they count as 1
- Capacity packs: **100 users / site / month**

### Anonymous Users
People who **browse without logging in** (tracked by browser cookie).

- If the same person visits 20 times from the same browser, they count as 1
- A new browser or cleared cookies = a new anonymous user
- Capacity packs: **500 users / site / month**
- Sign-in, registration, and error pages (404) are NOT counted

---

## Pricing Options

### Subscription (Pre-Paid Capacity Packs)

Best for: predictable user volumes, lower per-user cost at scale.

| User Type | Pack Size | Price | Per-User Cost |
|-----------|-----------|-------|---------------|
| Authenticated | 100 users/site/month | **$200/month** | $2.00 per user |
| Authenticated (10,000+ users) | Volume tier 2 | Lower | < $2.00 |
| Authenticated (100,000+ users) | Volume tier 3 | Lowest | << $2.00 |
| Anonymous | 500 users/site/month | **$75/month** | $0.15 per user |
| Anonymous (10,000+ users) | Volume tier 2 | Lower | < $0.15 |

> Unused capacity does NOT roll over month to month.
> Minimum: 25 authenticated users or 200 anonymous users per site.

### Pay-As-You-Go (Azure Subscription)

Best for: unpredictable or seasonal traffic, getting started without upfront commitment.

| User Type | Price |
|-----------|-------|
| Authenticated | **$4.00 per unique user / site / month** |
| Anonymous | **$0.30 per unique user / site / month** |

> You cannot mix subscription and pay-as-you-go in the same environment.

### Storage Included

Each licence pack accrues storage to your tenant:

| Licence | Database | File | Log |
|---------|----------|------|-----|
| Authenticated (100-user pack) | +2 GB | +16 GB | +1 GB |
| Anonymous (500-user pack) | +0.5 GB | +4 GB | — |

---

## What Is Already Included in Your Existing Licences

### Dynamics 365 Enterprise or Premium Licences

If your organisation has any Dynamics 365 Enterprise or Premium licence (Sales Enterprise, Customer Service Enterprise, Field Service, etc.), you already have **Power Pages use rights included**:
- Build and run unlimited Power Pages sites in the same environment as the D365 app
- You still need to purchase user capacity (authenticated or anonymous packs) for external users
- Internal employees with D365 licences do NOT consume Power Pages user capacity

### Power Apps Premium (per-user plan)

- Includes use rights to build and run Power Pages sites in environments you manage
- Still need external user capacity packs

### What Is NOT Included

- Dynamics 365 Professional or Team Members licences — no Power Pages use rights
- Microsoft 365 licences — no Power Pages use rights
- Power Apps per-app plan (retired January 2026 for new customers)

---

## The 30-Day Trial

The free trial gives you full Power Pages functionality for 30 days — no credit card required.

**What's included in the trial:**
- 1 Power Pages site
- Dataverse database with 1 GB storage
- All features including Copilot, authentication, all templates
- 10 authenticated users capacity (for testing)

**How to start the trial:**

1. Go to **https://make.powerpages.microsoft.com**
2. Sign in with a Microsoft account (personal Microsoft accounts work)
3. If prompted to create a trial environment: click "Get started" → trial environment created automatically
4. You are in the Power Pages home — click **+ Create a site**

---

## Environment Requirements

Power Pages requires a **Dataverse environment**. Every Power Pages site is connected to exactly one Dataverse environment.

### Environment Types

| Type | Purpose | Power Pages Allowed |
|------|---------|-------------------|
| Default | Auto-created for every tenant | Yes, but not recommended for production |
| Developer | Free, for individual development | Yes (perfect for learning) |
| Sandbox | Testing and UAT | Yes |
| Production | Live deployment | Yes (required for production) |
| Trial | 30-day evaluation | Yes |

**For learning:** Use a Developer environment. It is free, full-featured, and does not expire.

**Create a Developer environment:**
1. Go to https://admin.powerplatform.microsoft.com
2. Environments → + New
3. Type: Developer
4. Region: pick yours
5. Name: "MyPowerPages-Dev"

---

## Licensing Calculation Examples

### Example 1: Small Supplier Portal

- 80 unique authenticated suppliers log in each month
- No anonymous browsing (all pages require login)

**Cost:** 1 pack of 100 authenticated users/month = **$200/month**

### Example 2: Public Knowledge Base + Authenticated Support

- 2,000 unique anonymous visitors browse the knowledge base each month
- 150 unique authenticated customers submit support tickets each month

**Cost:**
- Anonymous: 4 packs of 500 users = 4 × $75 = **$300/month**
- Authenticated: 2 packs of 100 users = 2 × $200 = **$400/month**
- **Total: $700/month**

### Example 3: Pay-As-You-Go for Seasonal Event Registration

- December only: 3,000 authenticated users register for annual conference
- Other 11 months: fewer than 50 users

**Pay-as-you-go cost for December:** 3,000 × $4 = **$12,000**
**Subscription cost for December only:** 30 packs × $200 = **$6,000**

In this case, subscription (if purchased just for December) is cheaper.

### Example 4: Included in D365

Organisation has Dynamics 365 Sales Enterprise for 50 sales reps. Customer portal with 800 authenticated customers per month.

**Cost:** 0 (site creation included in D365) + 8 packs of authenticated users = 8 × $200 = **$1,600/month** for the 800 external customers.

---

## Developer Licence for Makers

The person **building** the Power Pages site (you, the maker/developer) does NOT consume external user capacity. Makers need:
- A Power Apps Developer Plan (free) — for individual development
- Or any paid Power Apps / Dynamics 365 licence

**Get the free Developer Plan:** https://powerapps.microsoft.com/en-us/developerplan/

---

## Monitoring Licence Usage

In production, monitor your consumption to avoid running out:

Power Platform Admin Center → Environments → [your environment] → Resources → Capacity

This shows:
- Authenticated user capacity allocated vs consumed (daily and monthly)
- Anonymous user capacity allocated vs consumed
- Dataverse storage usage

Set alerts before you hit 80% of capacity to avoid service interruption.

---

## Next Step
→ [First Site Walkthrough](./03-first-site-walkthrough.md) — build your first Power Pages site

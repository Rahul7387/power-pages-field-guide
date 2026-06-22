# Module 04 — Authentication & Security

> Security is the most complex part of Power Pages. Get it wrong and users see data they should not. Understand it deeply and you can build enterprise-grade portals with fine-grained access control.

## The Security Model Overview

```
Layer 1: Page-level access
  (Who can see this PAGE — anonymous or authenticated users?)
  Controlled by: Page Access Control / Web Role

Layer 2: Table-level access (Table Permissions)
  (Who can READ/CREATE/UPDATE/DELETE records in this TABLE?)
  Controlled by: Table Permissions assigned to Web Roles

Layer 3: Row-level access (Row-based scopes)
  (Which specific ROWS can this user see/edit?)
  Controlled by: Scope within Table Permission (Global/Contact/Account/Self)

Layer 4: Column-level access
  (Can this user see/edit this specific COLUMN?)
  Controlled by: Dataverse Column Security Profiles
```

---

## Authentication Providers

Power Pages supports multiple identity providers. Users choose which to use when registering.

### Microsoft Entra ID (Formerly Azure AD)

**Best for:** Enterprise employees logging in with their work accounts.

```
Setup: Design Studio → Set up → Authentication → + New provider
Type: OpenID Connect
Client ID: [from Azure App registration]
Client Secret: [from Azure App registration]
Authority: https://login.microsoftonline.com/{tenant-id}
```

### Google

**Best for:** Consumer portals where users prefer Google login.

```
Type: Google
Client ID: [from Google Cloud Console → OAuth 2.0 credentials]
Client Secret: [from Google Cloud Console]
```

### LinkedIn

**Best for:** Professional portals, recruitment portals.

```
Type: LinkedIn
Client ID: [from LinkedIn Developer App]
Client Secret: [from LinkedIn Developer App]
```

### Facebook

**Best for:** Consumer/community portals.

```
Type: Facebook
App ID: [from Facebook Developers]
App Secret: [from Facebook Developers]
```

### Azure AD B2C

**Best for:** Large consumer-facing portals with millions of external users. Supports custom sign-up/sign-in flows, MFA, password reset, profile editing.

```
Type: OpenID Connect
Authority: https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/B2C_1_signupsignin
Client ID: [B2C App registration]
```

### Local (Username/Password)

Power Pages has a built-in local identity provider — users register with email + password, stored in Dataverse.

**Use for:** Simple portals where external identity providers are not needed.

**Limitations:** You manage password resets, email verification, and account lockout.

### Custom SAML / OpenID Connect

Connect to any SAML 2.0 or OpenID Connect-compliant identity provider:
- Okta
- Ping Identity
- Auth0
- ADFS
- Any enterprise SSO system

---

## Contact Records

Every authenticated portal user has a corresponding **Contact record** in Dataverse. This is the bridge between the identity provider and your business data.

```
When a user registers on your portal:
1. They authenticate via their identity provider (Entra ID, Google, etc.)
2. Power Pages looks for an existing Contact with that email
3. If found: links the login to that Contact
4. If not found: creates a new Contact record
5. The Contact ID is stored in the portal session

In Liquid templates: {{ currentuser.contactid }}
In table permissions: "Contact" scope uses this Contact ID
```

### Invitation-Based Registration

For controlled access — invite specific people:

1. Power Pages admin creates an **Invitation** record (in Dataverse)
2. System sends an invitation email with a unique code
3. User clicks the link, sets their password (or authenticates via provider)
4. Their Contact record is linked to the invitation data

**Use case:** Partner portal where only specific approved vendors should register.

---

## Web Roles

A Web Role is a security group for portal users. Users are assigned to web roles; web roles grant access to pages and data.

### Built-In Web Roles

| Role | Purpose |
|------|---------|
| Authenticated Users | All logged-in users automatically get this role |
| Anonymous Users | All non-logged-in visitors have this role |
| Administrators | Full access to all portal content |

### Custom Web Roles

Create roles for your business:

```
Supplier (all registered suppliers)
SupplierAdmin (senior suppliers who can manage their team)
PremiumCustomer (customers on premium tier — different data access)
BrokerAgent (insurance brokers — see commission data)
RegionalManager_Central (Central region managers only)
```

**Creating a web role:**
1. Design Studio → Security → Web roles → + New
2. Name: "Supplier"
3. Authenticated Users role: leave unchecked (manual assignment only)
4. Assign to users: Done via portal → Contact record → Web Roles subgrid

**Automated role assignment via Power Automate:**
When a user registers and their Company = "Supplier", a flow automatically assigns the Supplier web role.

---

## Table Permissions

Table permissions control what users can do to records in a Dataverse table.

### Permission Types (CRUD)

| Permission | What It Allows |
|-----------|---------------|
| Read | View records (required for List and read-only forms) |
| Create | Submit new records (required for Create forms) |
| Write | Edit existing records (required for Edit forms) |
| Delete | Delete records |
| Append | Add child records (for related records) |
| Append To | Be added as a child of another record |

### Access Scope

**This is the most critical setting** — it controls WHICH ROWS the user can access:

| Scope | Description | Example |
|-------|-------------|---------|
| **Global** | All records in the table | Admin can see all customer cases |
| **Contact** | Only records where a Lookup column = current user's Contact | User sees only their own cases |
| **Account** | Records where Contact's parent Account matches | All employees of a company see company's records |
| **Parent** | Records related to a parent record they have access to | See documents for a case they own |
| **Self** | Only the Contact record for the current user | Edit only your own profile |

### Real-World Example: Support Portal

```
Case table permissions:

Rule 1: Customers can READ their own cases
  Table: Case
  Web Role: Authenticated Users
  Permission: Read
  Scope: Contact (Lookup: Customer → Contact)
  
Rule 2: Customers can CREATE cases
  Table: Case
  Web Role: Authenticated Users
  Permission: Create
  Scope: Global (to create a new case — no existing record to scope yet)

Rule 3: Customers can WRITE their own open cases
  Table: Case
  Web Role: Authenticated Users
  Permission: Write
  Scope: Contact
  Condition: Status = Open (only editable when open, not after resolution)

Rule 4: Support Agents can READ all cases
  Table: Case
  Web Role: SupportAgent
  Permission: Read
  Scope: Global

Rule 5: Anonymous users CANNOT see cases
  (No table permission for Anonymous Users web role = no access)
```

---

## Page-Level Access Control

Control which web roles can visit which pages:

Design Studio → Pages panel → right-click a page → **Page permissions**:

```
/home          → Anonymous + Authenticated (everyone)
/products      → Anonymous + Authenticated (public catalogue)
/my-profile    → Authenticated Users only (must log in)
/admin         → Administrators only
/supplier-hub  → Authenticated Users + Supplier web role
```

---

## Column Security Profiles

Control which columns a user can read or update at the Dataverse level:

```
Salary column in Employee table:
  - HR Managers: Read + Update
  - Employees: Read own salary (via portal Web API + table permission)
  - Everyone else: No access (column-level restriction)
```

Configure: Power Apps → Tables → [table] → Columns → [column] → Enable column security → Create a Column Security Profile.

---

## Security Best Practices

1. **Start locked down** — no table permissions = no access. Add permissions explicitly.
2. **Use Contact scope by default** — users should see their own data, not everyone's.
3. **Test as each web role** — Design Studio → Set up → Security → Simulate user role.
4. **Anonymous Read is dangerous** — only grant it to truly public data.
5. **Audit your permissions regularly** — use the Security workspace in Design Studio.
6. **Never grant Global Write/Delete to Authenticated Users** — too broad.
7. **Use invitation codes for sensitive portals** — don't allow self-registration.

---

## Simulating Security in Design Studio

Design Studio → Security → Test a user role:
1. Select a web role
2. Click "Preview as this role"
3. Browse the site — see exactly what a user in that role sees
4. Verify they cannot access pages/data they should not

---

## POC Task: Secure a Supplier Portal

Set up:
1. Authentication: Enable Microsoft Entra ID (or local accounts for testing)
2. Web roles: Create "Supplier" and "SupplierAdmin" roles
3. Table permissions for Supplier table:
   - Authenticated Users: Create (Global scope) — to register
   - Supplier: Read + Write (Contact scope) — own record only
   - SupplierAdmin: Read + Write (Account scope) — all suppliers in their company
4. Test: log in as two different test accounts — verify each sees only their own data
5. Page permissions: /supplier-dashboard requires Supplier or SupplierAdmin role

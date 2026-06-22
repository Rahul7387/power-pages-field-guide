# 01 — What Is Microsoft Power Pages?

## The One-Line Answer

Power Pages is Microsoft's low-code platform for building **secure, data-driven websites for external users** — people outside your organisation who do not have Microsoft 365 accounts.

---

## The Bigger Picture: Where Power Pages Fits

```
Microsoft Power Platform
├── Power Apps         (internal apps — employees with M365)
├── Power Automate     (workflow automation)
├── Power BI           (analytics and reporting)
├── Copilot Studio     (chatbots and AI agents)
└── Power Pages        (external websites — customers, partners, public)
                        ↑
                   This is what we are learning
```

The key distinction: **Power Apps is for internal users. Power Pages is for external users.**

Your customers, partners, citizens, and vendors do not need Microsoft 365 accounts. They access your Power Pages portal through a web browser, just like any other website.

---

## The History

| Year | Event |
|------|-------|
| 2015 | Microsoft acquires Adxstudio — a Dynamics CRM portal product |
| 2017 | Rebranded as **Dynamics 365 Portals** |
| 2019 | Further evolved into **Power Apps Portals** as part of Power Platform |
| Oct 2022 | Rebranded and significantly enhanced as **Microsoft Power Pages** |
| 2023 | Standalone licensing introduced; Design Studio overhauled |
| 2024–2025 | Copilot AI features added; security features expanded |
| Mar 2026 | Power Pages plugin for **GitHub Copilot CLI and Claude Code** launched (public preview) — build entire sites with natural language |

The underlying technology — Dataverse binding, Liquid templates, web roles, table permissions — is a continuous evolution from the original Adxstudio product.

---

## What Can You Build?

### Customer Self-Service Portals
Customers log in to view their orders, invoices, cases, and account details — and take actions without calling your support team.

**Real example:** A bank's customer portal where clients see their accounts, download statements, apply for products, and raise support tickets — all backed by Dataverse.

### Partner/Supplier Portals
External partners access shared data, submit documents, view contracts, and collaborate — with each partner seeing only their own data (enforced by table permissions and web roles).

**Real example:** A manufacturing company's supplier portal where each vendor manages their own deliveries, invoices, and quality documentation.

### Government/Citizen Services
Public-sector organisations publish information and accept applications, permits, complaints, and registrations from citizens — sometimes anonymously, sometimes after authentication.

**Real example:** A municipality's permit application portal where residents submit and track building permit requests.

### Employee-Facing Portals (Extranet)
Some organisations use Power Pages for employees who are not in Microsoft 365 — field workers, contractors, or international staff on different identity providers.

### Community Portals
Public-facing knowledge bases, FAQs, forums, and event registrations — where anonymous browsing is allowed and authenticated users can contribute.

---

## Power Pages vs Alternatives

| Feature | Power Pages | SharePoint Online | Custom Web App | WordPress |
|---------|------------|-------------------|---------------|-----------|
| Dataverse integration | Native | Via connector | Manual | Via API |
| No-code building | ✅ | Partial | ❌ | Partial |
| External users | ✅ Native | Complex licensing | ✅ | ✅ |
| Role-based data security | ✅ Native | Limited | Manual | Plugin |
| Liquid templates | ✅ | ❌ | N/A | PHP |
| Power Automate integration | ✅ Native | ✅ | Manual | Plugin |
| Microsoft identity providers | ✅ Native | ✅ Native | Manual | Plugin |
| AI / Copilot Studio | ✅ Native (2026) | Limited | Manual | Third-party |
| Dev toolchain (VS Code, CLI) | ✅ | Limited | Full | Full |

**When NOT to use Power Pages:**
- Internal-only apps where all users have Microsoft 365 → use Power Apps (canvas or model-driven)
- Pure marketing websites with no data → use Power Automate + SharePoint or a CMS
- High-traffic e-commerce requiring custom infrastructure → use Azure Web Apps

---

## The Technology Stack

```
Browser (external user)
       │
       ▼
Power Pages Website
├── Design Studio pages (HTML + components)
├── Liquid templates (server-side rendering)
├── JavaScript + CSS (client-side behaviour)
│
├── Portal Web API (secure CRUD to Dataverse)
│
├── Dataverse (data layer)
│   ├── Tables and columns
│   ├── Table permissions (who can read/write what)
│   └── Web roles (what authenticated users can do)
│
├── Authentication
│   ├── Microsoft Entra ID (formerly Azure AD)
│   ├── Google, LinkedIn, Facebook
│   ├── Azure AD B2C (for large-scale consumer scenarios)
│   └── Custom SAML/OpenID Connect providers
│
├── Power Automate (background workflows)
├── Copilot Studio agents (AI chatbots, 2025+)
└── Content Delivery Network (CDN, built-in)
```

---

## Key Concepts to Know Before Starting

| Concept | What It Is |
|---------|-----------|
| **Environment** | A container in Power Platform (like a tenant-within-a-tenant). Each environment has its own Dataverse database. |
| **Dataverse** | Microsoft's managed relational database service. All Power Pages data lives here. |
| **Web Role** | A role assigned to authenticated portal users. Controls which pages and data they can access. |
| **Table Permission** | A security rule on a Dataverse table: who can create, read, update, delete records. |
| **Liquid** | A server-side templating language (inherited from Shopify's Liquid) used to render dynamic content. |
| **Portal Web API** | A REST API endpoint that JavaScript on your pages can call to read/write Dataverse data. |
| **Contact record** | Every authenticated portal user is represented as a Contact record in Dataverse. |
| **Design Studio** | The low-code visual editor for building pages (since 2022). |
| **PAC CLI** | Power Platform CLI — command-line tool for pro developers. |

---

## 2026 State of the Platform

In March 2026, Microsoft launched the public preview of the Power Pages plugin for GitHub Copilot CLI and Claude Code. Describe the site you want in natural language and the plugin handles everything — from project scaffolding and Dataverse setup to API integrations, permissions, and deployment. The plugin understands table permissions, web roles, site settings, authentication, and Web API patterns.

An Agent API for Power Pages enables site creators to build custom chat and other user experiences and integrate these seamlessly with their custom-built Microsoft Copilot Studio agents.

Key directions for 2025–2026:
- AI-powered site creation (describe in natural language → site is generated)
- Copilot Studio agents embedded directly in portals for conversational experiences
- Security and compliance agent for admins (monitoring for phishing, DDoS)
- Stronger authentication controls with Microsoft Entra External ID
- Integrated telemetry and monitoring inside the Power Pages admin experience

---

## Real-World Example

**Scenario: A regional insurance company**

Before Power Pages:
- Customers called the call centre to update their address, request documents, report claims
- Agents spent 60% of time on requests the customer could self-serve
- Partner brokers emailed Excel files for commission statements

After Power Pages:
- Customer portal: log in → view policies, download statements, update contact details, file claims
- Broker portal: log in → see own commission data (table permissions ensure they cannot see other brokers), submit new business, download certificates
- Anonymous FAQ: any visitor can browse knowledge base without logging in
- Copilot Studio agent: embedded chatbot handles "where is my claim?" queries automatically

**Architecture:**
```
Insurance Dataverse environment
├── Policy table (authenticated customers can read their own rows)
├── Claim table (authenticated customers can create + read their own rows)
├── Broker table (authenticated brokers can read their own rows)
└── Commission table (authenticated brokers can read related rows)

Power Pages site
├── /home (anonymous — marketing content)
├── /faq  (anonymous — knowledge base)
├── /my-policies (authenticated — filtered by Contact = logged-in user)
├── /file-claim  (authenticated — form to create a Claim record)
└── /broker/commissions (authenticated + Broker web role only)
```

---

## Next Steps

- [Licensing & Trial Setup](./02-licensing-and-trial.md) — understand what you need before starting
- [First Site Walkthrough](./03-first-site-walkthrough.md) — build your first site in 15 minutes

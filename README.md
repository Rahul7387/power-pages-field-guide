# 🌐 power-pages-field-guide

> The complete practitioner's reference for Microsoft Power Pages — every concept explained from first principles with real-world examples, working code, POC projects, and the latest 2026 features including AI-assisted site building with GitHub Copilot CLI and Claude Code.

---

## What Is Power Pages?

Microsoft Power Pages is a **low-code platform for building secure, data-driven external-facing websites and portals** — backed by Microsoft Dataverse. It is part of the Microsoft Power Platform family.

External users (customers, partners, citizens, vendors) can:
- Browse public content anonymously
- Register and authenticate (via Microsoft Entra ID, Google, LinkedIn, Facebook, and more)
- Submit forms that write directly to Dataverse tables
- View and manage their own records
- Interact with AI-powered chatbots (Copilot Studio agents)

**Formerly known as:** Power Apps Portals (rebranded October 2022)

**2026 milestone:** Build entire Power Pages sites using natural language via the Power Pages plugin for GitHub Copilot CLI and Claude Code (public preview, March 2026).

---

## Who This Is For

| Role | How to Use This Guide |
|------|--------------------|
| **Citizen Developer / Maker** | Follow modules 1–4 for no-code/low-code portal building |
| **Pro Developer** | Modules 5–6 for Liquid, JavaScript, Web API, VS Code integration |
| **Architect** | Modules 7–9 for AI, integrations, ALM, governance |
| **Anyone** | Module 10 for the capstone project — a board-ready customer portal |

---

## Learning Path

```
BEGINNER ─────────────────────────────────────────────── EXPERT

01. What is Power Pages?       06. JavaScript & Web API
02. Design Studio (no-code)    07. Copilot & AI (2026)
03. Dataverse & Data           08. Integrations (Power Automate,
04. Authentication & Security       Power BI, Teams, SharePoint)
05. Liquid Templates           09. DevOps & ALM
                               10. Capstone Project
```

---

## Modules

| # | Module | Level | Key Topics |
|---|--------|-------|-----------|
| [01](./module-01-foundations/) | **Foundations** | Beginner | What is Power Pages, licensing, trial setup, first site |
| [02](./module-02-design-studio/) | **Design Studio** | Beginner | Pages, sections, components, themes, forms, lists |
| [03](./module-03-dataverse-data/) | **Dataverse & Data** | Beginner–Intermediate | Tables, columns, relationships, forms, views |
| [04](./module-04-authentication-security/) | **Auth & Security** | Intermediate | Web roles, table permissions, identity providers, RLS |
| [05](./module-05-liquid-code/) | **Liquid Templates** | Intermediate | Liquid syntax, objects, tags, filters, custom templates |
| [06](./module-06-javascript-webapi/) | **JavaScript & Web API** | Advanced | Portal Web API, CRUD, VS Code, custom JS, CSS |
| [07](./module-07-copilot-ai/) | **Copilot & AI** | Advanced | Copilot Studio agents, AI-assisted building, Agent API |
| [08](./module-08-integrations/) | **Integrations** | Advanced | Power Automate, Power BI, Teams, SharePoint, third-party |
| [09](./module-09-devops-alm/) | **DevOps & ALM** | Expert | PAC CLI, GitHub Actions, Azure DevOps, environments |
| [10](./module-10-capstone-project/) | **Capstone Project** | All levels | Full customer self-service portal — end to end |

---

## Licensing (June 2026)

| Plan | Price | What You Get |
|------|-------|-------------|
| **Authenticated Users** (subscription) | $200 / 100 users / site / month | Login-required portal; each unique monthly user counted once |
| **Anonymous Users** (subscription) | $75 / 500 users / site / month | Public browsing; counted by browser cookie |
| **Authenticated** (pay-as-you-go) | $4 / unique user / site / month | Pay only for actual usage via Azure subscription |
| **Anonymous** (pay-as-you-go) | $0.30 / unique user / site / month | Pay only for actual usage |
| **Included in D365 Enterprise/Premium** | No extra cost | Unlimited sites in the same environment |
| **Trial** | Free 30 days | Full features for evaluation |

> **Rule of thumb:** If you have Dynamics 365 Enterprise or Power Apps Premium licences already, Power Pages may be included at no extra cost for sites in the same environment.

---

## Quick Start (Free Trial)

1. Go to **https://make.powerpages.microsoft.com**
2. Sign in with a Microsoft account (or create one free)
3. Click **+ Create a site** → choose a template → your site is live in minutes

No credit card required for the 30-day trial.

---

## Repository Structure

```
power-pages-field-guide/
├── README.md                                    ← You are here
├── module-01-foundations/
│   ├── README.md
│   └── concepts/
│       ├── 01-what-is-power-pages.md
│       ├── 02-licensing-and-trial.md
│       └── 03-first-site-walkthrough.md
├── module-02-design-studio/
│   ├── README.md
│   └── concepts/ (pages, sections, components, themes, forms, lists)
├── module-03-dataverse-data/
│   ├── README.md
│   └── concepts/ (tables, columns, relationships, data workspace)
├── module-04-authentication-security/
│   ├── README.md
│   └── concepts/ (web roles, table permissions, identity providers)
├── module-05-liquid-code/
│   ├── README.md
│   └── concepts/ (syntax, objects, filters, custom templates)
├── module-06-javascript-webapi/
│   ├── README.md
│   └── concepts/ (Web API, VS Code, custom JS, CSS)
├── module-07-copilot-ai/
│   ├── README.md
│   └── concepts/ (Copilot Studio, AI site builder, Agent API)
├── module-08-integrations/
│   ├── README.md
│   └── concepts/ (Power Automate, Power BI, Teams, SharePoint)
├── module-09-devops-alm/
│   ├── README.md
│   └── concepts/ (PAC CLI, GitHub Actions, ALM)
└── module-10-capstone-project/
    ├── README.md
    ├── dataverse-schema.md
    ├── site-build-guide.md
    ├── security-setup.md
    ├── integration-guide.md
    └── deployment-guide.md
```

---

## Technology Stack

```
Power Pages site
├── Dataverse          (data storage — tables, relationships, permissions)
├── Design Studio      (no-code/low-code visual builder)
├── Liquid             (server-side template language)
├── JavaScript + CSS   (client-side customization)
├── Portal Web API     (client-side Dataverse CRUD operations)
├── Power Automate     (background workflows and automation)
├── Copilot Studio     (embedded AI chatbot agents)
├── Microsoft Entra ID (authentication — formerly Azure AD)
└── VS Code / PAC CLI  (pro developer toolchain)
```

---

*Built for practitioners who understand Power Pages is not just a website builder — it is the external-facing layer of your Power Platform solution.*

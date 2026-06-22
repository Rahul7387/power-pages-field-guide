# Module 09 — DevOps & Application Lifecycle Management (ALM)

> Professional Power Pages development requires the same ALM discipline as traditional software: version control, environment promotion, automated testing, and CI/CD pipelines.

## Environment Strategy

Never build directly in production. Use a minimum 3-environment model:

```
Development (DEV)
  → Makers build and experiment freely
  → No sensitive production data
  → Daily / on-demand refresh from QA

Quality Assurance / UAT (QA)
  → Stable version for business testing
  → Representative data (anonymised)
  → Promoted via managed solution from DEV

Production (PROD)
  → Live user traffic
  → Only managed solutions deployed (no direct edits)
  → Promoted via managed solution from QA
```

---

## Power Platform CLI (PAC CLI)

The PAC CLI is the command-line tool for Power Platform developers. Essential for automation.

### Installation

```bash
# Using npm
npm install -g @microsoft/powerplatform-cli

# Verify
pac --version
```

### Authenticate

```bash
# Interactive login (browser pop-up)
pac auth create --url https://your-org.crm.dynamics.com

# Service Principal (for CI/CD pipelines — no browser)
pac auth create \
  --url https://your-org.crm.dynamics.com \
  --applicationId YOUR_APP_ID \
  --clientSecret YOUR_CLIENT_SECRET \
  --tenant YOUR_TENANT_ID

# List all authenticated profiles
pac auth list

# Switch profile
pac auth select --index 2
```

### Portal Site Operations

```bash
# List all Power Pages sites in the environment
pac pages list

# Download a site to local directory
pac pages download --path ./my-portal --webSiteId YOUR_SITE_ID

# Upload local changes back to portal
pac pages upload --path ./my-portal

# Create a new portal site
pac pages create --name "Supplier Portal" --templateName "Starter layout 1"
```

### Solution Management

```bash
# Export a solution (unmanaged — for dev)
pac solution export --name MySolution --path ./solutions/dev --managed false

# Export managed solution (for deployment to QA/PROD)
pac solution export --name MySolution --path ./solutions/prod --managed true

# Import a solution
pac solution import --path ./solutions/prod/MySolution_managed.zip

# Check solution status
pac solution check --path ./solutions/MySolution.zip
```

---

## Solutions: Packaging Your Portal

A **Solution** is a container that packages your portal and related components for transport between environments.

### What Goes in a Solution

```
MySolution/
├── Power Pages site
├── Dataverse tables (custom)
├── Dataverse forms and views
├── Business rules
├── Web roles
├── Table permissions
├── Site settings
├── Power Automate flows (cloud flows)
└── Connection references (connectors used by flows)
```

### Creating a Solution

1. Power Apps (make.powerapps.com) → Solutions → + New solution
2. Display name: "GlobalMart Supplier Portal"
3. Publisher: create or select your organisation's publisher
4. Add components: Add Existing → Portal → [your site]
5. Add all related tables, flows, etc.

### Managed vs Unmanaged Solutions

| | Unmanaged | Managed |
|--|-----------|---------|
| Where used | Development | QA + Production |
| Can edit components | Yes | No (locked) |
| Can delete individual components | Yes | Only by uninstalling entire solution |
| Layering | No | Yes (layered on top of managed layer) |
| Best practice | DEV environment only | All other environments |

---

## Git Version Control

Store your portal files in Git for change tracking, code review, and collaboration.

### Repository Structure

```
supplier-portal-repo/
├── .github/
│   └── workflows/
│       ├── deploy-qa.yml         (CI/CD: deploy to QA on PR merge)
│       └── deploy-prod.yml       (CI/CD: deploy to PROD on release)
├── portal-source/                (downloaded via pac pages download)
│   ├── web-pages/
│   ├── web-templates/
│   ├── content-snippets/
│   ├── web-files/
│   └── entity-lists/
├── solutions/
│   └── MySolution_1_0_0_0.zip   (exported solution)
├── dataverse-config/
│   ├── tables/                  (table schema JSON)
│   └── views/                   (view definitions)
└── docs/
    ├── architecture.md
    └── deployment-guide.md
```

### Workflow

```bash
# 1. Start a new feature
git checkout -b feature/supplier-document-upload

# 2. Download current portal state
pac pages download --path ./portal-source

# 3. Make your changes (edit files in VS Code)

# 4. Commit and push
git add ./portal-source
git commit -m "feat: add supplier document upload with virus scanning"
git push origin feature/supplier-document-upload

# 5. Create Pull Request → team reviews → merge to main

# 6. CI/CD pipeline automatically deploys to QA
```

---

## CI/CD with GitHub Actions

### Deploy to QA on PR Merge

```yaml
# .github/workflows/deploy-qa.yml
name: Deploy to QA

on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install PAC CLI
        run: npm install -g @microsoft/powerplatform-cli

      - name: Authenticate to QA environment
        run: |
          pac auth create \
            --url ${{ secrets.QA_ORG_URL }} \
            --applicationId ${{ secrets.APP_ID }} \
            --clientSecret ${{ secrets.CLIENT_SECRET }} \
            --tenant ${{ secrets.TENANT_ID }}

      - name: Upload portal to QA
        run: |
          pac pages upload \
            --path ./portal-source \
            --webSiteId ${{ secrets.QA_SITE_ID }}

      - name: Import solution to QA
        run: |
          pac solution import \
            --path ./solutions/MySolution_managed.zip \
            --activate-plugins
            
      - name: Notify Teams
        run: |
          curl -X POST ${{ secrets.TEAMS_WEBHOOK }} \
            -H 'Content-Type: application/json' \
            -d '{"text":"✅ Supplier Portal deployed to QA. Review at ${{ secrets.QA_PORTAL_URL }}"}'
```

### Deploy to Production on Release

```yaml
# .github/workflows/deploy-prod.yml
name: Deploy to Production

on:
  release:
    types: [published]

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production  # requires manual approval in GitHub
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Install PAC CLI
        run: npm install -g @microsoft/powerplatform-cli

      - name: Authenticate to PROD
        run: |
          pac auth create \
            --url ${{ secrets.PROD_ORG_URL }} \
            --applicationId ${{ secrets.APP_ID }} \
            --clientSecret ${{ secrets.CLIENT_SECRET }} \
            --tenant ${{ secrets.TENANT_ID }}

      - name: Deploy to Production
        run: |
          pac pages upload --path ./portal-source --webSiteId ${{ secrets.PROD_SITE_ID }}
          pac solution import --path ./solutions/MySolution_managed.zip --activate-plugins

      - name: Run smoke tests
        run: node ./tests/smoke-tests.js ${{ secrets.PROD_PORTAL_URL }}
```

---

## Solution Checker

Validate your solution for issues before deployment:

```bash
# Run Solution Checker
pac solution check --path ./solutions/MySolution.zip

# Output example:
# ✅ No critical issues found
# ⚠️  3 medium severity issues:
#     - Missing error handling in cloud flow (FlowName)
#     - Hardcoded URL in web template (products-template)
#     - Table permission too broad (Global scope on Case)
```

---

## ALM Best Practices

1. **Never edit production directly** — all changes via DEV → QA → PROD pipeline
2. **Use managed solutions in QA and PROD** — prevents uncontrolled changes
3. **Version your solutions** — increment Major.Minor.Build.Revision (1.0.0.0 → 1.1.0.0)
4. **Store connection references** — avoid hardcoded connection details
5. **Separate data from configuration** — reference data seeding scripts separate from solution
6. **Tag releases** — git tags match solution version numbers
7. **Automate testing** — Playwright or Cypress for UI tests, PAC for schema validation

---

## POC Task: Set Up a Full ALM Pipeline

1. Create 3 environments: Dev, QA, Prod (use trial environments for learning)
2. Install PAC CLI and authenticate to all three
3. Create a GitHub repository with the recommended structure
4. Download your portal to the repo: `pac pages download --path ./portal-source`
5. Create a GitHub Actions workflow that deploys to QA when you push to main
6. Make a change (e.g., update the home page heading), push to Git, and watch it deploy automatically

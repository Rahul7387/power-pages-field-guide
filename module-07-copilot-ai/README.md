# Module 07 — Copilot & AI (2025–2026)

> Power Pages has evolved from a form-and-list portal builder into an AI-first platform. In 2026, you can describe a site in natural language and have it built automatically.

## AI Capabilities Overview

| Capability | What It Does | Available Since |
|-----------|-------------|----------------|
| AI-assisted page creation | Describe a page, Copilot generates it | 2024 |
| AI text generation | Generate page content from a prompt | 2024 |
| AI form creation | Describe a form, Copilot creates it | 2024 |
| Copilot Studio agents | Embed conversational AI chatbots | 2024 |
| Agent API | Build custom chat UI connected to Copilot Studio | Mar 2026 |
| GitHub Copilot CLI plugin | Build entire sites with natural language | Mar 2026 (preview) |
| Claude Code plugin | Same — build sites using Claude Code | Mar 2026 (preview) |
| Security and compliance agent | Monitor for phishing, DDoS | Wave 2 2025 |
| AI-powered search | Semantic search across portal content | 2025 |

---

## 1. Copilot in Design Studio

### AI-Assisted Page Creation

Design Studio → Pages → + Page → **"Describe the page you want"**:

```
Prompt: "Create a supplier registration page with fields for company name, 
         trade licence number, contact person, email, phone, and country of 
         operation. Include a document upload section for trade licence and 
         tax certificate. Add a progress indicator for the multi-step form."

Copilot generates:
  → Multi-step form design
  → Suggested Dataverse table structure
  → Column configuration
  → Section layout with progress bar
```

Review the generated result → accept, modify, or regenerate.

### AI Text Generation

On any page, select a text component → **"Generate with Copilot"**:

```
Prompt: "Write a professional welcome message for a supplier portal. 
         The company is GlobalMart, a retail chain. Tone: professional 
         but approachable. Length: 2 paragraphs."
```

### AI Form Creation

Click + Component → Form → **"Describe the form you need"**:

```
Prompt: "Create a product return request form. Fields: order number, 
         product purchased, reason for return (dropdown: Defective/Wrong Item/
         Changed Mind/Other), additional details, preferred resolution 
         (Refund/Exchange/Repair), and an image upload for product condition."
```

---

## 2. Copilot Studio Agents (Embedded Chatbot)

Embed a conversational AI agent on any portal page — customers ask questions in natural language and get intelligent answers.

### Creating a Copilot Studio Agent

1. Go to **https://copilotstudio.microsoft.com**
2. + New copilot → Name: "GlobalMart Support Agent"
3. Configure:
   - **Topics:** Define conversation flows (e.g., "Check Order Status", "Request Return")
   - **Knowledge sources:** Connect to your Dataverse tables, SharePoint, websites
   - **Actions:** Call Power Automate flows, query Dataverse records

### Knowledge Source Configuration

```
Type: Dataverse
Tables to include:
  - Product (for product info questions)
  - FAQ (for knowledge base articles)
  - Case (to look up the user's own cases)

Example:
User: "What is the return policy for electronics?"
Agent: [searches FAQ table] "Electronics can be returned within 30 days in original packaging..."

User: "What's the status of my order #ORD-12345?"
Agent: [queries Order table filtered to current user] "Your order #ORD-12345 is Out for Delivery, expected by 5 PM today."
```

### Embedding the Agent on Your Portal

#### Method 1: Design Studio (No Code)

Design Studio → Pages → select page → + Component → **Copilot**

Configure:
- Bot ID: [from Copilot Studio settings]
- Position: floating chat button, inline, or full section
- Greeting: "Hi! I'm the GlobalMart assistant. How can I help you today?"
- Color: match brand theme

#### Method 2: Custom Embed Code (HTML)

```html
<!-- Paste this in a Custom HTML component or web template -->
<div id="agent-container"></div>

<script>
  // Embed code from Copilot Studio → Settings → Channels → Custom website
  window.Microsoft.Omnichannel.initialize('your-bot-id', {
    orgId: 'your-org-id',
    orgUrl: 'https://your-org.crm.dynamics.com',
    channelId: 'your-channel-id'
  });
</script>
```

#### Method 3: Agent API (March 2026)

The new Agent API gives full control over the chat UI:

```javascript
// Initialize a custom chat interface connected to your Copilot Studio agent
const agent = await PowerPages.AgentAPI.initialize({
  agentId: 'your-agent-id',
  container: document.getElementById('custom-chat'),
  theme: {
    primaryColor: '#2E75B6',
    fontFamily: 'Segoe UI'
  },
  context: {
    // Pass portal context to the agent
    userId: portalConfig.currentUserId,
    userName: portalConfig.currentUserName,
    pageId: portalConfig.pageId
  }
});

// Send a message programmatically
agent.sendMessage('What is the status of my latest order?');

// Listen for agent responses
agent.onResponse((response) => {
  console.log('Agent says:', response.text);
  // Custom rendering logic
});
```

### Passing User Context to the Agent

The agent can receive context about the logged-in user:

```liquid
<!-- In your page template, inject user context for the agent -->
<script>
  var copilotContext = {
    userId:    {{ currentuser.id | json }},
    userName:  {{ currentuser.fullname | json }},
    userEmail: {{ currentuser.email | json }},
    userRole:  {% if currentuser.roles contains 'Supplier' %}'Supplier'{% else %}'Customer'{% endif %}
  };
</script>
```

The agent can then personalize responses: "Hello Ahmed, I can see you have 3 open support cases. Which would you like to check on?"

---

## 3. GitHub Copilot CLI + Claude Code Plugin (March 2026 Preview)

Build entire Power Pages sites using natural language from your terminal.

### Setup

```bash
# Install the Power Pages plugin for GitHub Copilot CLI
gh extension install microsoft/gh-copilot-powerpages

# Or for Claude Code:
# Install Claude Code + configure the Power Pages skill
# Available on Claude Marketplace: search "Power Pages"
```

### The 9 Conversational Skills

The plugin provides 9 skills that cover the full site lifecycle:

| Skill Command | What It Does |
|--------------|-------------|
| `/create-site` | Scaffolds a new SPA site with your described pages and design |
| `/setup-datamodel` | Analyzes your site, proposes Dataverse schema + ER diagram |
| `/integrate-webapi` | Replaces mock data with production Web API code + typed clients |
| `/add-sample-data` | Populates Dataverse tables with realistic test records |
| `/setup-auth` | Generates sign-in/sign-out with Entra ID + web role definitions |
| `/create-webroles` | Generates role-based access control configuration |
| `/add-seo` | Generates sitemaps, meta tags, Open Graph markup |
| `/deploy` | Deploys the site to your Power Pages environment |
| `/validate` | Runs security and accessibility checks before deployment |

### Example Session

```
You: /create-site
     "I need a supplier onboarding portal for GlobalMart. 
      Pages: Home (public), Supplier Registration (public form), 
      My Dashboard (authenticated), Document Upload (authenticated),
      Application Status (authenticated). 
      Brand colors: navy blue #1F3864 and gold #FFC000."

Plugin: Creating site scaffold...
        ✓ Generating page structure
        ✓ Creating responsive layouts
        ✓ Applying brand theme
        ✓ Setting up navigation
        Site created. Preview at: http://localhost:3000

You: /setup-datamodel

Plugin: Analyzing site code...
        
        Proposed schema:
        ┌─────────────────────────────────────────┐
        │ Table: Supplier_Application              │
        │ Columns: CompanyName, LicenseNo,         │
        │          ContactName, Email, Phone,       │
        │          Country, Status, SubmittedDate   │
        └─────────────────────────────────────────┘
        ┌─────────────────────────────────────────┐
        │ Table: Supplier_Document                 │
        │ Columns: DocumentName, DocumentType,     │
        │          FileAttachment, ExpiryDate       │
        │ Lookup: → Supplier_Application           │
        └─────────────────────────────────────────┘
        
        Create these tables? (yes/no/modify)

You: yes

Plugin: ✓ Tables created in Dataverse
        ✓ Sample data added

You: /setup-auth
     "Use Microsoft Entra ID for authentication. 
      New registrants get the 'Supplier' web role."

Plugin: ✓ Entra ID authentication configured
        ✓ Supplier web role created
        ✓ Automatic role assignment flow created in Power Automate
        ✓ Table permissions configured (Contact scope for supplier data)

You: /deploy

Plugin: Deploying to Power Pages...
        ✓ All files uploaded
        ✓ Site published
        Live URL: https://your-org.powerappsportals.com
```

**What used to take 2 weeks of development now takes 2 hours.**

---

## 4. AI-Powered Search

Configure semantic search on your portal — finds conceptually relevant content, not just keyword matches.

```
Setup: Design Studio → Set up → Site settings → Search
       Enable: AI-powered search
       Index: all pages + selected Dataverse table columns

User types: "how do I get my money back?"
AI search finds: "Return & Refund Policy" page
                 "Refund Request" form
                 FAQ: "Refund Processing Time"
                 (even though none say "money back" literally)
```

---

## 5. Security and Compliance Agent

New in Wave 2 2025: an AI agent that monitors your portal for security threats.

```
Capabilities:
  - Monitors traffic patterns for DDoS attack signatures
  - Detects phishing attempts (fake login pages, suspicious redirects)
  - Identifies unusual data access patterns (potential scraping)
  - Alerts admin and can auto-block suspicious IPs
  - Web Application Firewall (WAF) rule suggestions

Setup: Power Pages admin center → Security → Compliance agent → Enable
```

---

## POC Task: AI-Powered Support Portal

1. Create a Copilot Studio agent with 3 topics:
   - "Check my case status" → queries Cases table for current user
   - "Submit a new case" → guides user through form fields conversationally
   - "Find product information" → searches Products table

2. Embed the agent on your portal's home page (floating button)

3. Pass user context (name, email, account type) to the agent

4. Test: ask "What is the status of my open cases?" — the agent should return real data from Dataverse

5. Bonus: use AI text generation in Design Studio to write the portal's FAQ content from a 5-bullet brief

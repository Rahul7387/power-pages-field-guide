# Module 08 — Integrations

> Power Pages does not work in isolation. It is most powerful when connected to Power Automate, Power BI, Teams, SharePoint, and third-party services.

## Integration Architecture

```
External User on Portal
        │
        ▼
Power Pages ◄──────────────────────────────────┐
        │                                       │
        ├──► Dataverse (primary data)           │
        │                                       │
        ├──► Power Automate (automation)        │
        │    ├── Email notifications             │
        │    ├── Approval workflows              │
        │    ├── SharePoint file operations     │
        │    ├── Teams notifications            │
        │    └── Third-party API calls          │
        │                                       │
        ├──► Power BI (embedded reports)        │
        │                                       │
        ├──► SharePoint (document storage)      │
        │                                       │
        ├──► Microsoft Teams                    │
        │                                       │
        └──► Copilot Studio (AI agents) ────────┘
```

---

## 1. Power Automate Integration

Power Automate is the glue between Power Pages and the rest of your business systems.

### Trigger: When a Form is Submitted

The most common pattern — a portal form submission triggers a workflow.

```
Trigger: When a row is added (Dataverse)
Table:   Supplier_Application
Condition: none (fire for all new applications)

Actions:
1. Send email to supplier: "Your application has been received. Reference: {ApplicationID}"
2. Send email to procurement team: "New supplier application from {CompanyName}"
3. Create a Task in Planner for document review
4. Post message in Teams: "New supplier: {CompanyName} — Region: {Country}"
5. Update Supplier_Application: Status = "Under Review"
```

### Approval Workflow

```
Trigger: When Supplier_Application status = "Submitted"

Actions:
1. Start an approval → Approver: procurement.manager@company.com
2. Wait for response

If Approved:
  3a. Update Status = "Approved"
  3b. Send congratulations email to supplier
  3c. Grant Supplier web role to their Contact record
  3d. Post "✅ New supplier approved" in Teams

If Rejected:
  3a. Update Status = "Rejected"
  3b. Send rejection email with reasons
  3c. Send notification to procurement manager
```

### Connecting Flow to Portal Form

Design Studio → select Form component → Cloud flows → + Add flow → select from your flows list.

The flow receives the record ID of the newly created Dataverse record as input.

---

## 2. SharePoint Integration

Use SharePoint for document management — files stored in SharePoint appear in your portal.

### Portal Files from SharePoint

```
Scenario: Legal team manages contract templates in SharePoint.
          Suppliers access them via the portal without IT involvement.

Architecture:
  SharePoint Library: /sites/GlobalMart/Contracts/Templates/
  Power Automate: daily sync → copies file metadata to Dataverse "Document" table
  Portal: List component shows Document table with download links
  
  OR: Direct SharePoint connector via Liquid:
  {{ sharepoint_file.url }}  -- reference SharePoint file directly
```

### Form Attachment → SharePoint

When users upload files via portal forms, redirect to SharePoint:

```
Power Automate flow:
Trigger: When a new SupplierDocument row is created in Dataverse

Actions:
1. Get file content from Dataverse (the uploaded file)
2. Create file in SharePoint: /sites/GlobalMart/SupplierDocuments/{SupplierID}/{FileName}
3. Update SupplierDocument row: SharePointURL = the new SharePoint URL
4. Delete the file from Dataverse (to avoid Dataverse storage limits)
```

### Accessing SharePoint Files in Portal

```liquid
{% assign doc_url = currentuser.cr123_supplierid.cr123_TradeicenseSharepointUrl %}
{% if doc_url %}
  <a href="{{ doc_url }}" target="_blank">View Trade Licence</a>
{% else %}
  <a href="/upload-document">Upload Trade Licence</a>
{% endif %}
```

---

## 3. Power BI Integration

Embed Power BI reports and dashboards inside portal pages so authenticated users see their own data visualised.

### Requirements

- Report published to Power BI Service
- Power BI Embedded capacity (A-SKU) for report embed without per-user Power BI licence
- OR: authenticated portal users also have Power BI Pro licences (simpler but more expensive)

### Design Studio Embed

1. + Component → Power BI
2. Enter:
   - Workspace ID: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
   - Report ID: `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`
   - Page: (optional, specific report page)
3. Authentication: Service Principal (requires Power BI Embedded) or User Identity

### Row-Level Security in Embedded Report

For each portal user to see only their own data in Power BI:

1. Configure RLS in Power BI Desktop: Role "PortalUser" with filter `[UserEmail] = USERNAME()`
2. When embedding, pass the portal user's email as the effective identity:

```javascript
// In your page custom HTML:
var models = window['powerbi-client'].models;

var embedConfig = {
  type: 'report',
  id: '{{ settings["PowerBI/ReportId"] }}',
  embedUrl: 'https://app.powerbi.com/reportEmbed',
  accessToken: '{{ settings["PowerBI/EmbedToken"] }}',  // from Azure Function
  tokenType: models.TokenType.Embed,
  settings: { filterPaneEnabled: false },
  permissions: models.Permissions.Read
};

powerbi.embed(document.getElementById('power-bi-container'), embedConfig);
```

---

## 4. Microsoft Teams Integration

### Post Portal Events to Teams

Power Automate: post a Teams message when something happens on the portal.

```
Flow: New Support Case Created → Post to Teams Channel

Message:
🎫 New support case #{CaseNumber}
Customer: {CustomerName}  ({CustomerEmail})
Category: {CaseCategory}
Priority: {Priority}
Description: {Description[:200]}...

[View in Portal Admin] | [Open in Dynamics]
```

### Teams Notification with Adaptive Card

```json
{
  "type": "AdaptiveCard",
  "body": [
    {
      "type": "TextBlock",
      "text": "🏭 New Supplier Application",
      "weight": "Bolder",
      "size": "Medium"
    },
    {
      "type": "FactSet",
      "facts": [
        {"title": "Company", "value": "@{triggerOutputs()['body/cr123_companyname']}"},
        {"title": "Country", "value": "@{triggerOutputs()['body/cr123_country']}"},
        {"title": "Submitted", "value": "@{formatDateTime(triggerOutputs()['body/createdon'], 'dd MMM yyyy HH:mm')}"}
      ]
    }
  ],
  "actions": [
    {
      "type": "Action.OpenUrl",
      "title": "Review Application",
      "url": "https://myportal.powerappsportals.com/admin/applications/@{triggerOutputs()['body/cr123_supplierid']}"
    }
  ]
}
```

### Embed Portal in Teams Tab

Add your Power Pages portal as a tab in a Teams channel:

Teams → Channel → + Add tab → Website → Enter your portal URL.

Users access the portal without leaving Teams. Portal authentication works via the user's Teams/Entra ID session.

---

## 5. Third-Party Integrations

### Payment Gateway (Stripe / PayPal)

```html
<!-- Stripe embedded on a portal payment page -->
<div id="stripe-payment-element"></div>
<button id="submit-payment">Pay SAR {{ amount }}</button>

<script src="https://js.stripe.com/v3/"></script>
<script>
  const stripe = Stripe('{{ settings["Stripe/PublicKey"] }}');
  
  // Create payment intent via Power Automate HTTP flow
  const response = await fetch('/_api/cr123_payment', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      '__RequestVerificationToken': getAntiForgeryToken()
    },
    body: JSON.stringify({
      cr123_amount: {{ amount }},
      cr123_currency: 'SAR',
      cr123_orderid: '{{ order_id }}'
    })
  });
  
  // Power Automate flow handles Stripe API call and returns clientSecret
</script>
```

### Google Maps / Azure Maps

```html
<!-- Embed an interactive store locator map -->
<div id="store-map" style="height: 500px;"></div>

<script>
async function loadStoreMap() {
  // Get stores from Web API
  const response = await fetch('/_api/cr123_store?$select=cr123_name,cr123_latitude,cr123_longitude,cr123_address&$filter=cr123_isopen eq true');
  const data = await response.json();
  
  // Initialize Google Maps
  const map = new google.maps.Map(document.getElementById('store-map'), {
    center: { lat: 24.7136, lng: 46.6753 },  // Riyadh
    zoom: 5
  });
  
  // Add a marker for each store
  data.value.forEach(store => {
    new google.maps.Marker({
      position: { lat: store.cr123_latitude, lng: store.cr123_longitude },
      map,
      title: store.cr123_name
    });
  });
}
</script>
<script async src="https://maps.googleapis.com/maps/api/js?key={{ settings['GoogleMaps/ApiKey'] }}&callback=loadStoreMap"></script>
```

### Email Marketing (Mailchimp / HubSpot)

```javascript
// On newsletter signup form submit — add to Mailchimp list via Power Automate
document.getElementById('newsletter-form').addEventListener('submit', async (e) => {
  e.preventDefault();
  
  const email = document.getElementById('newsletter-email').value;
  
  // Power Automate flow handles the Mailchimp API call
  await fetch('/_api/cr123_newsletter_subscriber', {
    method: 'POST',
    headers: { 
      'Content-Type': 'application/json',
      '__RequestVerificationToken': getAntiForgeryToken() 
    },
    body: JSON.stringify({ cr123_email: email, cr123_source: 'Portal Footer' })
  });
  
  // Power Automate triggered by new row → adds to Mailchimp → sends welcome email
  showSuccessMessage('You\'re subscribed! Check your inbox.');
});
```

---

## POC Task: Full Integration Demo

Build a Supplier Portal with these integrations:

1. **Power Automate:** On new Supplier Application → email applicant + email procurement team + post Teams message
2. **Power BI:** Embed a "My Performance Dashboard" page showing the supplier's order history and payment status (for authenticated suppliers only)
3. **SharePoint:** Uploaded documents go to SharePoint library (not Dataverse storage)
4. **Teams:** Create a Teams tab for the internal procurement team to review applications without leaving Teams

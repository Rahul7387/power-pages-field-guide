# 04 — Forms In Depth

## Form Types

### Basic Form
A single-page form — creates or edits one record.

**Best for:** Contact us, support ticket, product enquiry, simple registration.

### Multi-Step Form (formerly Web Form / Wizard)
A multi-page form — each page shows a subset of fields, users navigate with Next/Back. One record built across multiple steps.

**Best for:** Complex onboarding, multi-stage applications, long questionnaires.

---

## Adding a Form to a Page

1. Click **+** on the canvas → **Form**
2. Select or create a **form** (connected to a Dataverse table)
3. Configure:
   - **Table:** which Dataverse table (e.g., Contact, Case, lead_application)
   - **Form name:** which column set and layout
   - **Mode:** Create new record / Edit existing record / Read-only view
   - **On success:** message, redirect, reopen

---

## Form Field Configuration

Click a field in the form editor to configure:

| Setting | Options |
|---------|---------|
| Label | Custom display label |
| Required | Mark as mandatory |
| Validation | Pattern matching, min/max, custom regex |
| Error message | Custom validation error text |
| Placeholder | Helper text inside empty field |
| Pre-fill | Set default value (static or from query string) |
| Conditional visibility | Show/hide based on another field's value |

---

## Pre-Filling Form Fields

Pre-fill from URL query string parameters:

```
URL: /apply?source=google&campaign=summer2024

Form field "Source": mapped to query string key "source"
Form field "Campaign": mapped to query string key "campaign"
```

**In form field settings:** Default value → From query string → key name.

Pre-fill for authenticated users (from their Contact record):

```liquid
-- In a custom form template, use entityform_metadata:
{{ currentuser.contactid }}  -- the logged-in user's Contact ID
{{ currentuser.fullname }}   -- their full name
```

---

## Multi-Step Form Setup

1. Design Studio → Data workspace → Forms → + New form
2. Choose **Multi-step form**
3. Add steps (pages):
   - Step 1: Personal Information (First Name, Last Name, Email, Phone)
   - Step 2: Application Details (Position, Experience, Cover Note)
   - Step 3: Document Upload (CV attachment field)
   - Step 4: Review & Submit (read-only summary)

4. Configure navigation between steps:
   - Each step has a **Next** button (validate current step before advancing)
   - Optional **Back** button
   - Optional **Save draft** (saves partial record to Dataverse)

5. **Session management:** Partially completed forms are saved to Dataverse as Draft records. Users can return and continue later (requires authentication for session tracking).

---

## Form Submission Actions

After a successful submission:

| Action | Use Case |
|--------|---------|
| Show success message | Simple confirmation |
| Redirect to a page | Thank you page with further instructions |
| Redirect to record | Show the created/edited record immediately |
| Open another form | Chain forms (e.g., submit enquiry → then book a call) |

### Triggering Power Automate on Form Submit

Add a **cloud flow** to the form:

1. In Design Studio: select the form → settings → Cloud flows
2. Choose an existing flow or create new
3. The flow is triggered when the form is submitted
4. The flow receives the record ID of the newly created Dataverse record

**Example flow:** When a new enquiry form is submitted → Send an email to the sales team with the enquiry details → Create a task in Planner → Send a confirmation email to the customer.

---

## File Upload Fields

Add a file attachment column to your Dataverse table (File or Image data type), then add it to the form. Users can:
- Drag and drop files
- Click to browse and select
- Upload multiple files (if configured)

**Max file size:** Configured in Dataverse column settings (default 32 MB).
**Allowed extensions:** Configurable in portal settings.

---

## Captcha (Bot Protection)

Enable reCAPTCHA on any form to prevent spam submissions:

Design Studio → Form → Settings → Captcha → Enable

This requires a Google reCAPTCHA v2 or v3 site key (free from Google).

---

## POC Task

Build a **Job Application Form** (multi-step):
- Step 1: Personal details (Name, Email, Phone, LinkedIn URL)
- Step 2: Position details (Role applied for, Experience level, Start date)
- Step 3: CV upload (File field)
- Step 4: Confirmation message with case reference number

Trigger a Power Automate flow on submit: send email to HR team + send confirmation email to applicant.

# 03 — First Site Walkthrough: Build in 15 Minutes

## What We Build

A simple **Company Information Portal** with:
- Home page with hero banner and company description
- Services page with cards
- Contact Us page with a form (writes to Dataverse)
- Anonymous access (no login required)

This demonstrates the full no-code Design Studio experience.

---

## Step 1 — Open Power Pages

1. Navigate to **https://make.powerpages.microsoft.com**
2. Sign in with your Microsoft account
3. Select your environment (top-right environment selector)
4. You see the Power Pages home screen with featured templates

---

## Step 2 — Create a New Site

1. Click **+ Create a site** (top right or centre of screen)
2. The Template Gallery opens — browse through:
   - **Starter layout** — blank canvas, most flexible
   - **Conference** — event sites with registration
   - **Community** — forums and Q&A
   - **Program registration** — intake forms
   - **Frequently Asked Questions** — knowledge base
   - **Project management** — task tracking

3. For this walkthrough: select **Starter layout 1** (simple, clean)
4. Click **Choose this template**
5. Enter:
   - **Site name:** My Company Portal
   - **Web address:** `mycompanyportal` (becomes part of the URL: mycompanyportal.powerappsportals.com)
6. Click **Done**

> Power Pages creates your Dataverse tables, configures security settings, and provisions the site. This takes 2–5 minutes.

---

## Step 3 — The Design Studio Interface

Once the site is created, Design Studio opens automatically. Let's orient:

```
┌─────────────────────────────────────────────────────────────────────┐
│  TOOLBAR                                                             │
│  [Pages] [Styling] [Data] [Set up] [Security] […]   [Preview] [Sync]│
├────────┬────────────────────────────────────────────────────────────┤
│ PAGES  │                                                            │
│ PANEL  │              CANVAS (live preview of your site)            │
│ ─────  │                                                            │
│ Home   │  [Click any element to select and edit]                   │
│ …      │                                                            │
└────────┴────────────────────────────────────────────────────────────┘
```

**Left panel tabs:**
- **Pages** — your site's page tree (add, rename, delete pages)
- **Styling** — brand colors, fonts, theme
- **Data** — Dataverse tables connected to your site
- **Set up** — site settings, authentication, SEO
- **Security** — web roles and table permissions

---

## Step 4 — Edit the Home Page

The canvas shows your home page. Click any element to edit:

### Edit the Hero Banner

1. Click the large hero section at the top
2. A toolbar appears: **Edit text** / **Change image** / **Add section**
3. Click **Edit text** — an inline text editor appears
4. Change the heading: "Welcome to My Company Portal"
5. Change the subheading: "Your one-stop destination for our services and support"
6. Click outside to deselect

### Add a Text Section

1. Scroll down on the canvas
2. Click the **+** icon between sections
3. Choose **Section** → **Text**
4. A two-column text section appears
5. Edit: "About Us" / "We provide world-class services to our clients since 2010..."

### Add an Image

1. Click the **+** and choose **Image**
2. Upload an image or use a stock photo URL
3. Set alt text for accessibility

---

## Step 5 — Add a New Page (Services)

1. In the left Pages panel: click **+ Page**
2. Name: "Services"
3. URL: `/services` (auto-filled)
4. Layout: choose a blank layout or a pre-built one
5. Click **Add**

The new page opens in the canvas. Add a header section and three service cards:

1. Click **+ Section** → **Card Gallery**
2. A 3-card layout appears
3. Click each card to edit:
   - Card 1: Icon + "Consulting" + description
   - Card 2: Icon + "Support" + description
   - Card 3: Icon + "Training" + description

---

## Step 6 — Add a Contact Form Page

The most powerful feature: a form that writes data directly to Dataverse.

1. Add new page: "Contact Us" at `/contact`
2. On the canvas: click **+** → **Form**
3. The Form wizard opens:
   - **Table:** Select `Contact` (the built-in Contacts table)
   - **Form:** Choose `Contact us` or create a new form
   - **Columns to show:** First Name, Last Name, Email, Message (add a multi-line text column)
4. Click **Done**

A form is now on your page. When anonymous visitors submit it:
- A new Contact record is created in Dataverse
- You can view responses in Power Apps / Dataverse / a model-driven app
- You can trigger a Power Automate flow to send notifications

### Customise the Form

Click the form in the canvas → **Edit form**:
- Reorder fields by dragging
- Set field labels
- Mark required fields with asterisk
- Add validation messages
- Set the success message: "Thank you! We'll be in touch within 24 hours."

---

## Step 7 — Style Your Site

Click the **Styling** tab in the left panel:

- **Colors:** Set primary, secondary, background, text colors
- **Font:** Choose from Google Fonts library
- **Logo:** Upload your company logo (appears in navigation)
- **Favicon:** Upload a small icon for browser tabs

**Quick theme:** At the top of Styling, choose a preset color palette:
- Professional Blue, Energetic Orange, Natural Green, etc.
- All colors update across all pages instantly

---

## Step 8 — Set Up Navigation

The navigation menu is managed from the Pages panel:

1. In Pages panel: drag pages to reorder them
2. Check/uncheck **Show in navigation** for each page
3. Right-click a page → **Create child page** for dropdown menus
4. The navigation automatically reflects page order and hierarchy

---

## Step 9 — Preview Your Site

Click **Preview** (top right) → the site opens in a new browser tab.

Test:
- Navigate between pages
- Submit the contact form
- Check mobile responsiveness (resize browser)

---

## Step 10 — Sync and Go Live

1. Click **Sync** (top right) — publishes all unsaved changes
2. Your site is live at: `https://mycompanyportal.powerappsportals.com`
3. Share this URL with anyone — no login required for anonymous access

---

## Step 11 — View Form Submissions

1. Go to **https://make.powerapps.com**
2. Select your environment
3. Dataverse → Tables → Contact
4. Click **Data** tab — see all form submissions

Or: visit https://make.powerpages.microsoft.com → your site → **Data** tab → Contact table.

---

## What You Just Built

```
Your Power Pages Site
├── /home      — Hero + About section (anonymous)
├── /services  — Service cards (anonymous)
└── /contact   — Form → Dataverse Contact table (anonymous submission)

Dataverse
└── Contact table
    └── New row created for each form submission
```

---

## Common Beginner Mistakes

| Mistake | What Happens | Fix |
|---------|-------------|-----|
| Forgetting to Sync | Changes not visible to visitors | Click Sync before sharing URL |
| Wrong environment selected | Site connects to wrong Dataverse | Check environment top-right of make.powerpages.microsoft.com |
| Form not saving | Table permission not set | See Module 04 — by default, anonymous users cannot create records |
| URL already taken | Site creation fails | Try a more unique web address |
| Editing live site directly | Risky in production | Use separate dev/staging environments |

---

## POC Project

Build the **Public Information Portal** described in the Module 01 README — add one more page with a FAQ accordion, customise the theme with your brand colors, and verify form submissions appear in Dataverse.

---

## Next Step
→ [Module 02 — Design Studio](../../module-02-design-studio/README.md) — go deep on every Design Studio component

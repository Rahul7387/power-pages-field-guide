# 01 — Pages, URL Structure & Sections

## Page Types

Power Pages supports several page types:

### Standard Web Page
A regular page with sections and components — the most common type.

```
URL: /products
Parent: Home (determines breadcrumb hierarchy)
Access: Anonymous or Authenticated (controlled by web role)
Template: Custom (Design Studio) or custom Liquid template
```

### Landing Page
A full-width, no-navigation page — ideal for campaign-specific URLs, login redirects.

### Redirect Page
Permanently or temporarily redirects a URL. Useful when renaming pages to preserve SEO.

### Web File
Binary files served via the portal — PDFs, images, downloadable documents.

---

## URL Structure

Page trees map directly to URL paths:

```
Site root: https://myportal.powerappsportals.com/

Home               → /
Services           → /services/
  → Consulting     → /services/consulting/
  → Support        → /services/support/
Products           → /products/
Contact            → /contact/
```

**Custom domains:** Configure `portal.mycompany.com` in the Power Pages admin center. Requires CNAME DNS record; SSL certificate is automatically managed.

---

## Section Layouts

Each section has a column layout:

| Layout | Best For |
|--------|---------|
| Full width (1-col) | Hero banners, announcements |
| 2-column 50/50 | Text + image pairs |
| 2-column 60/40 | Content + sidebar |
| 3-column equal | Feature cards, service tiles |
| 4-column | Stats row, icon grid |

**Section settings:**
- Background: solid color, gradient, or image
- Padding: top/bottom spacing
- Width: full-width or contained (max-width with margins)
- Visibility: show/hide based on auth state

---

## POC Task
Create a site with hierarchical navigation: Home → About → Services (with child pages: Consulting, Support, Training) → Contact.

Configure each page's section layout and verify the dropdown navigation appears correctly.

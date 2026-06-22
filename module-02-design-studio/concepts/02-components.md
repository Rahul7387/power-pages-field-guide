# 02 — Components Reference

## All Available Components

| Component | Purpose | Auth Required |
|-----------|---------|--------------|
| Text | Rich text, headings, paragraphs | No |
| Image | Photos, illustrations, logos | No |
| Button | CTA, navigation, download | No |
| Card Gallery | Feature tiles, team grids, product tiles | No |
| List (Data Table) | Dataverse records in a searchable table | Table permission |
| Form | Create/edit Dataverse records | Table permission |
| Power BI | Embed Power BI reports | Yes (Power BI licence) |
| IFrame | Embed external web content | No |
| Custom HTML | Raw HTML for advanced layouts | No |
| Video | YouTube, Vimeo, uploaded file | No |
| Accordion | FAQ, collapsible sections | No |
| Steps | Process flows, numbered guides | No |
| Spacer / Divider | Layout spacing | No |

## List Component — Key Configuration

```
Table:      Choose a Dataverse table (e.g., Products)
View:       Choose which Dataverse view (columns + filter)
Search:     Enable search bar (searches all visible columns)
Filter:     Allow users to filter by specific columns
Pagination: 10/25/50 records per page
Actions:    View detail, Edit, Create new record
```

**Access control:** List automatically filters to records the user has Read permission for. Anonymous users cannot see lists unless you explicitly grant anonymous read access on the table permission.

## Form Component — Quick Reference

```
Table:   Which Dataverse table to write to
Form:    Which form definition to use
Mode:    Insert (new record), Edit (existing), Read-only
On Success: Show message, redirect to page, reopen blank
```

## Power BI Embed

```
Requirements:
  - Report published to Power BI Service
  - Workspace ID + Report ID
  - Power BI Embedded capacity OR each user has Pro licence
  
Note: Anonymous users CANNOT view embedded Power BI reports.
      Power BI always requires authentication.
```

## Custom HTML Example

```html
<!-- Announcement banner -->
<div class="announcement-banner">
  <span>📢</span>
  <strong>New Feature:</strong> Track your orders in real-time — 
  <a href="/order-tracking">Try it now</a>
</div>

<style>
.announcement-banner {
  background: #1F3864;
  color: #fff;
  padding: 12px 24px;
  border-radius: 6px;
  display: flex;
  gap: 8px;
  align-items: center;
}
.announcement-banner a { color: #FFC000; }
</style>
```

## POC Task

Build a Services page with:
1. Hero section (full-width, background image, heading + button)
2. 3-column Card Gallery (Consulting | Support | Training cards)
3. Accordion FAQ section (5 questions)
4. CTA section: "Ready to start?" + Button → Contact form

# 05 — Lists & Data Views

## What a List Is

A **List** (also called a Data Grid) displays records from a Dataverse table in a searchable, filterable, paginated table on your portal page.

Critically: the list **respects table permissions**. Each user sees only the records they have permission to see.

---

## Adding a List to a Page

1. Click **+** → **List**
2. Select: table, view (filter + columns), settings
3. Configure:

```
Table:         my_product   (the Dataverse table)
View:          Active Products (Dataverse view — sets columns and row filter)
Page size:     10 records per page
Search:        Enabled
Empty message: "No products found matching your search"
Create button: "Submit New Product" → opens a form
```

---

## List Actions

Configure what users can do from the list:

| Action | Description |
|--------|------------|
| View | Click a row → opens a detail page (read-only or edit form) |
| Edit | Opens the record in an edit form |
| Delete | Deletes the record (requires Delete permission) |
| Create new | Button to open a new record form |
| Download | Export the list as CSV |
| Custom action | Run a Power Automate flow for selected records |

---

## Filtering and Search

### Search Bar
Power Pages adds a full-text search bar that queries all visible columns. Searches are case-insensitive partial matches.

### Filter Facets
Add predefined filter options users can click to filter the list:

```
Category filter:    [ ] Electronics  [ ] Furniture  [ ] Office Supplies
Status filter:      [ ] Active  [ ] Discontinued  [ ] Coming Soon
Price range:        SAR _____ to _____
```

Configure: List settings → Filters → Add facet per column.

### OData Filter in URL
Lists can be pre-filtered via URL parameters:

```
/products?$filter=category eq 'Electronics'

This shows only Electronics products when someone lands on that URL.
Useful for: category landing pages, product type navigation.
```

---

## Pagination

Configure: 10, 25, 50, or 100 records per page.

For very large tables (10,000+ records), use:
- **Server-side pagination** (default) — only loads the current page of records
- Combined with search and filters to keep any single result set manageable

---

## Column Configuration

For each column in the list:
- Display name (not the Dataverse column name)
- Sort: enable/disable
- Width: fixed or auto
- Type: text, number, date, image, URL, boolean (shown as Yes/No badge)

---

## POC Task

Build a **Product Catalogue** list page:
- Table: Products (custom Dataverse table)
- View: "Active Products — Catalogue View" (Name, Category, Price, Image)
- Search: enabled
- Filter facets: Category, Price Range
- Click → opens a Product Detail page (read-only form)
- Create button visible only to users with the "Supplier" web role

This is the List component at its most useful.

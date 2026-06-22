# Module 03 — Dataverse & Data

> Power Pages is only as good as the data behind it. Dataverse is the database — understanding it is non-negotiable.

## What Is Dataverse?

Microsoft Dataverse is a managed, cloud-based relational database that is the data backbone of the entire Power Platform. It is not a traditional SQL database — it has:

- Built-in security at the row and column level
- Relationships between tables with automatic cascading
- Business rules and validation at the data layer
- Audit logging, change tracking, and version history
- REST API (OData + Web API)
- Integration with all Power Platform products

Every Power Pages site connects to exactly one Dataverse environment.

---

## The Data Workspace

Design Studio → **Data** tab opens the Data Workspace — a simplified Dataverse table editor that lives inside Power Pages.

Here you can:
- Create new tables
- Add columns
- View and edit data rows
- Manage relationships
- Configure forms and views used by your portal

---

## Tables

A **Table** in Dataverse is like a table in a relational database — rows and columns.

### Standard Tables (Built-In)

These tables exist in every Dataverse environment and integrate with Power Platform:

| Table | What It Stores | Portal Use |
|-------|--------------|-----------|
| **Contact** | External portal users (after registration) | Every authenticated portal user = 1 Contact row |
| **Account** | Organisations/companies | Parent of Contacts (B2B scenarios) |
| **Case** | Support tickets | Customer support portals |
| **Lead** | Potential customers | Lead capture portals |
| **Opportunity** | Sales opportunities | Partner portals |
| **Product** | Product catalogue | E-commerce/catalogue portals |
| **Appointment** | Scheduled meetings | Booking portals |
| **Email** | Email messages | Case communication history |

### Custom Tables

Create any table your business needs:

```
Example: Loan Application table
Columns:
  ApplicationID (autonumber, primary key)
  ApplicantName (text, 100 chars, required)
  LoanAmount (currency, required)
  LoanPurpose (choice: Home/Vehicle/Business/Education)
  EmploymentStatus (choice: Employed/Self-Employed/Retired)
  MonthlyIncome (currency)
  SubmittedDate (date)
  Status (choice: Draft/Submitted/Under Review/Approved/Rejected)
  Remarks (multiline text)
  Applicant (lookup → Contact)  -- links to the portal user who submitted
```

---

## Column Types

| Type | Description | Example |
|------|-------------|---------|
| Text (single line) | Up to 4,000 characters | Name, email, city |
| Text (multi-line) | Long text, rich text | Description, notes |
| Choice (picklist) | Dropdown with predefined values | Status, category |
| Choices (multi-select) | Multiple values from a list | Skills, tags |
| Number (whole) | Integer | Quantity, age |
| Number (decimal) | Decimal number | Weight, rating |
| Currency | Money with currency symbol | Price, salary |
| Date and Time | Date + optional time | OrderDate, BirthDate |
| Date Only | Date without time | DeliveryDate |
| Lookup | Foreign key to another table | CustomerID → Contact |
| File | Binary file attachment | Document, image |
| Image | Image file | Product photo |
| Yes/No (boolean) | True/False | IsActive, IsApproved |
| Email | Email with mailto link | contact@company.com |
| Phone | Phone number | +966-50-xxx-xxxx |
| URL | Web address | https://... |
| Autonumber | Auto-incrementing identifier | CASE-0001, REQ-0042 |

---

## Relationships

### One-to-Many (Parent → Child)

The most common relationship. One parent row can have many child rows.

```
Contact (1) ──── (*) Case
One customer can have many support cases.

Account (1) ──── (*) Contact
One company can have many contacts/employees.

Application (1) ──── (*) ApplicationDocument
One application can have many attached documents.
```

**In Dataverse:** Add a Lookup column to the child table pointing to the parent.

### Many-to-Many (N:N)

A row in table A can relate to many rows in table B, and vice versa.

```
Student (*) ──── (*) Course
One student can enrol in many courses; one course has many students.
```

**In Dataverse:** Dataverse creates an intermediary junction table automatically.

### Self-Referential

A table with a lookup to itself — for hierarchies:

```
Employee → Manager (also an Employee)
Category → Parent Category (also a Category)
```

---

## Dataverse Views

A **View** is a saved query on a Dataverse table — defines which rows and columns to show.

When you add a List component to a portal page, you select a View to determine what the user sees.

```
Table: Product

Views:
├── "Active Products"        rows: IsActive = Yes    columns: Name, Category, Price
├── "Discontinued Products"  rows: IsActive = No     columns: Name, Discontinued Date
├── "Electronics Catalogue"  rows: Category = Electronics  columns: Name, Image, Price, SKU
└── "My Products" (owner)    rows: CreatedBy = Current User  columns: All
```

Create views in:
- Power Apps (make.powerapps.com → Tables → [table] → Views)
- Or directly in the Data Workspace in Design Studio

---

## Dataverse Forms

A **Form** defines which columns to show when creating/editing a record — and their layout, sections, and tabs.

One table can have multiple forms for different purposes:

```
Contact table forms:
├── "Portal Registration Form"  (Name, Email, Company — simple)
├── "Full Profile Form"         (all fields — for authenticated users to update profile)
└── "Admin View Form"           (internal use — all fields including internal notes)
```

The portal uses the form you configure in the Form component on your page.

---

## Business Rules

Business rules enforce data logic at the Dataverse level — they apply whether data is entered via the portal, Power Apps, API, or directly:

```
Rule: "If LoanAmount > 500,000 then require EmploymentVerification = Yes"
Rule: "If Status = 'Approved' then set ApprovalDate = Today()"
Rule: "If MonthlyIncome < 5,000 then show error: 'Minimum income requirement not met'"
```

Configure: Power Apps → Tables → [table] → Business Rules → + New Rule

---

## Connecting Your Data to the Portal

### Step 1: Create the table (in Data Workspace or Power Apps)
### Step 2: Add columns (appropriate types)
### Step 3: Create at least one View (for List components)
### Step 4: Create at least one Form (for Form components)
### Step 5: Set Table Permissions (see Module 04)
### Step 6: Add List/Form components to your portal pages

---

## Real-World Example: Supplier Registration System

```
Tables needed:
├── Supplier (custom)
│   ├── CompanyName (text, required)
│   ├── TradeLicenseNumber (text, required, unique)
│   ├── ContactName (text)
│   ├── Email (email, required)
│   ├── Phone (phone)
│   ├── Country (choice: SA/AE/KW/QA/BH/OM)
│   ├── Category (choices: Electronics/Food/Clothing/Other)
│   ├── Status (choice: Draft/Pending/Approved/Rejected)
│   ├── SubmittedDate (date, auto-set)
│   └── ContactRecord (lookup → Contact)  ← links to portal user
│
├── SupplierDocument (custom)
│   ├── DocumentName (text)
│   ├── DocumentType (choice: Trade License/Tax Certificate/Bank Statement/Other)
│   ├── DocumentFile (file)
│   ├── ExpiryDate (date)
│   └── Supplier (lookup → Supplier)  ← parent relationship

Relationships:
  Contact (1) ──── (1) Supplier  (each portal user registers one Supplier)
  Supplier (1) ──── (*) SupplierDocument  (each supplier uploads multiple documents)

Dataverse Views:
  "My Supplier Profile"  (rows: ContactRecord = current user, all columns)
  "My Documents"         (rows: Supplier.ContactRecord = current user)
  "Pending Suppliers"    (rows: Status = Pending, for admin review)

Portal pages:
  /register       → Form (create new Supplier record)
  /my-profile     → Form (edit my Supplier record)
  /my-documents   → List (my documents) + Form (upload new)
  /status         → Read-only form showing my application status
```

---

## POC Task

Build a **Course Registration System** data model:

1. Create table: `Training_Course` with columns:
   - CourseName, Description, StartDate, EndDate, Location, MaxCapacity, Price, Status

2. Create table: `Course_Registration` with columns:
   - RegistrationDate, Status (Registered/Waitlisted/Cancelled), PaymentStatus
   - Lookup → Training_Course
   - Lookup → Contact (the registrant)

3. Create views: "Available Courses" (Status=Open, StartDate > today), "My Registrations"

4. Create forms: "Register for Course" (simple: just the Course lookup), "My Registration Details" (all fields, read-only)

5. Add the "Available Courses" list to a portal page — users can click to register.

## Module README

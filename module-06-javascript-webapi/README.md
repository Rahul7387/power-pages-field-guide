# Module 06 — JavaScript & Web API

> The Portal Web API gives your JavaScript code secure, direct access to Dataverse — read records, create records, update records, all from the browser without a round-trip to a server.

## Portal Web API Overview

The Portal Web API is a REST endpoint that Power Pages exposes on your site. JavaScript code on your pages can call this API to perform CRUD operations on Dataverse tables — subject to the same table permissions configured in Module 04.

```
Your JavaScript → Portal Web API → Table Permission Check → Dataverse → Returns JSON
```

**Base URL:** `/_api/` (relative to your site)

**Format:** OData v4

**Authentication:** The API automatically uses the current portal session. Anonymous users can only call the API on tables where Anonymous Users has read permission.

---

## Enabling the Web API

For each table you want to expose via the Web API:

1. Power Pages Management → Site Settings → + New
2. Create these settings:

```
Webapi/{tableName}/enabled   = true
Webapi/{tableName}/fields    = column1,column2,column3  (or * for all columns)

Example:
Webapi/cr123_product/enabled = true
Webapi/cr123_product/fields  = cr123_name,cr123_price,cr123_category,cr123_description
```

---

## Reading Records (GET)

### Fetch all records

```javascript
async function getProducts() {
  const response = await fetch('/_api/cr123_product', {
    headers: {
      '__RequestVerificationToken': document.querySelector('[name="__RequestVerificationToken"]')?.value
    }
  });
  const data = await response.json();
  return data.value;  // array of product records
}

// Usage
const products = await getProducts();
products.forEach(product => {
  console.log(product.cr123_name, product.cr123_price);
});
```

### Fetch with OData filter

```javascript
// Get active products in the Electronics category
const response = await fetch(
  "/_api/cr123_product?$filter=cr123_category eq 'Electronics' and cr123_isactive eq true&$orderby=cr123_name asc&$select=cr123_name,cr123_price,cr123_productid"
);
const data = await response.json();
```

### Fetch a single record by ID

```javascript
const productId = '12345678-1234-1234-1234-123456789abc';
const response = await fetch(`/_api/cr123_product(${productId})?$select=cr123_name,cr123_price`);
const product = await response.json();
```

### Fetch with related records (expand)

```javascript
// Get cases with customer name from related Contact
const response = await fetch(
  "/_api/incident?$select=title,statecode,createdon&$expand=customerid_contact($select=fullname,emailaddress1)"
);
```

---

## Creating Records (POST)

```javascript
async function submitEnquiry(productId, message) {
  const requestBody = {
    cr123_productid_cr123_enquiry_ProductId@odata.bind: `/cr123_product(${productId})`,
    cr123_message: message,
    cr123_submittedby: currentUserId  // pass from Liquid: {{ currentuser.id | json }}
  };

  const response = await fetch('/_api/cr123_enquiry', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      '__RequestVerificationToken': getAntiForgeryToken()
    },
    body: JSON.stringify(requestBody)
  });

  if (response.status === 204) {
    console.log('Enquiry created successfully');
    const recordId = response.headers.get('OData-EntityId');
    return recordId;
  }
}
```

---

## Updating Records (PATCH)

```javascript
async function updateProfile(contactId, firstName, lastName, phone) {
  const response = await fetch(`/_api/contact(${contactId})`, {
    method: 'PATCH',
    headers: {
      'Content-Type': 'application/json',
      '__RequestVerificationToken': getAntiForgeryToken()
    },
    body: JSON.stringify({
      firstname: firstName,
      lastname: lastName,
      telephone1: phone
    })
  });

  return response.status === 204;  // 204 = success, no content
}
```

---

## Deleting Records (DELETE)

```javascript
async function cancelEnquiry(enquiryId) {
  const response = await fetch(`/_api/cr123_enquiry(${enquiryId})`, {
    method: 'DELETE',
    headers: {
      '__RequestVerificationToken': getAntiForgeryToken()
    }
  });
  return response.status === 204;
}
```

---

## Anti-Forgery Token

Every write operation (POST, PATCH, DELETE) requires an anti-forgery token for CSRF protection. Get it from the page:

```javascript
function getAntiForgeryToken() {
  return document.querySelector('[name="__RequestVerificationToken"]')?.value
    || document.querySelector('input[name="__RequestVerificationToken"]')?.value;
}

// Or inject via Liquid in your page template:
// <input type="hidden" id="anti-forgery" value="{{ anti-forgery }}" />
// Then: document.getElementById('anti-forgery').value
```

---

## Real-World Example: Live Search

A product search that calls the Web API as the user types, no page reload:

```javascript
// Debounced search — waits 300ms after user stops typing
let searchTimeout;

document.getElementById('product-search').addEventListener('input', function() {
  clearTimeout(searchTimeout);
  const query = this.value.trim();

  searchTimeout = setTimeout(async () => {
    if (query.length < 2) {
      document.getElementById('search-results').innerHTML = '';
      return;
    }

    try {
      const encoded = encodeURIComponent(query);
      const url = `/_api/cr123_product?$filter=contains(cr123_name,'${encoded}') and cr123_isactive eq true&$select=cr123_productid,cr123_name,cr123_price,cr123_category&$top=10`;

      const response = await fetch(url);
      const data = await response.json();
      renderSearchResults(data.value);
    } catch (err) {
      console.error('Search failed:', err);
    }
  }, 300);
});

function renderSearchResults(products) {
  const container = document.getElementById('search-results');

  if (products.length === 0) {
    container.innerHTML = '<p class="no-results">No products found.</p>';
    return;
  }

  container.innerHTML = products.map(p => `
    <div class="search-result-item" onclick="window.location='/products/detail?id=${p.cr123_productid}'">
      <div class="result-name">${escapeHtml(p.cr123_name)}</div>
      <div class="result-meta">
        <span class="category">${escapeHtml(p.cr123_category)}</span>
        <span class="price">${formatCurrency(p.cr123_price)}</span>
      </div>
    </div>
  `).join('');
}

function escapeHtml(text) {
  const div = document.createElement('div');
  div.appendChild(document.createTextNode(text));
  return div.innerHTML;
}
```

---

## VS Code Integration

Power Pages has native integration with Visual Studio Code via the **Power Platform Tools extension**.

### Setup

1. Install VS Code (free): https://code.visualstudio.com
2. Install extension: **Power Platform Tools** (Microsoft, official)
3. Install Power Platform CLI (PAC CLI):
   - `npm install -g @microsoft/powerplatform-cli`
   - Or use the VS Code extension's built-in installer

### Connect to Your Environment

```bash
# Authenticate
pac auth create --url https://your-org.crm.dynamics.com

# List available sites
pac pages list

# Download your site to work locally
pac pages download --path ./my-portal-site
```

### Edit Files Locally

After downloading, your portal files are organised locally:

```
my-portal-site/
├── web-pages/            (page HTML + Liquid templates)
│   ├── home-page/
│   │   └── content.html  (edit this in VS Code!)
│   └── products/
│       └── content.html
├── web-templates/        (reusable templates)
├── content-snippets/     (reusable snippets)
├── web-files/           (CSS, JS, images, PDFs)
└── entity-lists/        (list configurations)
```

Edit any file in VS Code with full syntax highlighting for HTML, CSS, JavaScript, and Liquid.

### Upload Changes

```bash
# Upload changed files back to the portal
pac pages upload --path ./my-portal-site

# Or use VS Code's Source Control panel for visual upload
```

### VS Code + vscode.dev (Browser-Based)

Power Pages also integrates with **vscode.dev** (VS Code in the browser):

Design Studio → Advanced → Edit code → Opens your portal files in vscode.dev directly in your browser. No local installation required.

---

## Custom JavaScript Best Practices

### Load JavaScript Efficiently

Place page-specific JavaScript in a custom HTML component at the bottom of the page (just before `</body>`):

```html
<script>
  // Page-specific code here
  (function() {
    'use strict';
    
    // Initialize when DOM is ready
    document.addEventListener('DOMContentLoaded', function() {
      initProductSearch();
      initEnquiryForm();
    });
    
    function initProductSearch() { /* ... */ }
    function initEnquiryForm() { /* ... */ }
    
  })();  // IIFE to avoid polluting global scope
</script>
```

### Passing Liquid Values to JavaScript

```html
<!-- In your page template, inject server-side values -->
<script>
  var portalConfig = {
    currentUserId:   {{ currentuser.id | json }},
    currentUserName: {{ currentuser.fullname | json }},
    isAuthenticated: {{ currentuser | json_bool }},
    pageId:          {{ page.id | json }},
    siteUrl:         '{{ website.adx_primarydomainname }}'
  };
</script>

<!-- Then in your JS file, use portalConfig.currentUserId etc. -->
```

---

## POC Task: Real-Time Order Status Tracker

Build a page where authenticated customers type their Order ID and instantly see the current status without page reload:

1. Enable Web API for your Orders table
2. Build a search form (text input + button)
3. JavaScript calls `/_api/cr123_order?$filter=cr123_ordernumber eq '${orderNum}' and _cr123_customer_value eq '${currentUserId}'`
4. Display: Order number, date, status (with colored badge), line items
5. If no result: "Order not found or doesn't belong to your account"

The Contact scope table permission ensures users can only look up their own orders — even if they guess another order ID.

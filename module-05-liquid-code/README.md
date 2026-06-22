# Module 05 — Liquid Templates

> Liquid is the server-side templating language used in Power Pages. It runs on the server before the page is sent to the browser, allowing you to embed dynamic content, logic, and data directly in your HTML.

## What Is Liquid?

Liquid was originally created by Shopify and is widely used in static site generators and portal platforms. Power Pages uses a superset of Liquid with additional Power Pages–specific objects.

**Key principle:** Liquid runs on the server. By the time the browser receives the page, all Liquid has been evaluated and replaced with plain HTML.

```
Server:                              Browser receives:
{% if user %}                   →    <p>Welcome back, Ahmed!</p>
  <p>Welcome back, {{ user.fullname }}!</p>
{% endif %}
```

---

## Liquid Syntax

### Output Tags `{{ }}`

Render a value:

```liquid
{{ page.title }}
{{ currentuser.fullname }}
{{ currentuser.email }}
{{ 'hello' | upcase }}
{{ product.price | currency }}
```

### Logic Tags `{% %}`

Control flow — do NOT render anything directly:

```liquid
{% if currentuser %}
  <p>You are logged in as {{ currentuser.fullname }}</p>
{% else %}
  <p>Please <a href="/signin">sign in</a> to continue.</p>
{% endif %}

{% for item in items %}
  <li>{{ item.name }}</li>
{% endfor %}

{% assign greeting = 'Hello, ' | append: currentuser.fullname %}
{{ greeting }}
```

### Comment Tags `{# #}`

```liquid
{# This is a Liquid comment — not rendered to the user #}
```

---

## Power Pages Liquid Objects

These objects are available on every page:

### `currentuser` (Authenticated User)

```liquid
{{ currentuser }}                  -- the Contact record of the logged-in user
{{ currentuser.id }}               -- Contact GUID
{{ currentuser.fullname }}         -- Full name
{{ currentuser.email }}            -- Email address
{{ currentuser.contactid }}        -- Contact ID (same as .id)

-- Check if user is logged in:
{% if currentuser %}
  <p>Hello {{ currentuser.fullname }}!</p>
{% else %}
  <p>Please log in.</p>
{% endif %}

-- Check web role:
{% if currentuser.roles contains 'Supplier' %}
  <a href="/supplier-dashboard">Supplier Dashboard</a>
{% endif %}
```

### `page`

```liquid
{{ page.title }}                   -- Current page title
{{ page.adx_partialurl }}          -- Current page URL (partial)
{{ page.description }}             -- Meta description
{{ page.parent.title }}            -- Parent page title (for breadcrumbs)
```

### `website`

```liquid
{{ website.name }}                 -- Site name
{{ website.adx_primarydomainname }} -- Primary domain
{{ website.currentlanguage.name }} -- Current language
```

### `request`

```liquid
{{ request.url }}                  -- Full current URL
{{ request.path }}                 -- URL path only (/products/widget-1)
{{ request.params['category'] }}   -- Query string parameter
{{ request.cookies['preference'] }}-- Cookie value
```

### `settings`

Access site settings (key-value pairs configured in admin):

```liquid
{{ settings['MyApp/SupportEmail'] }}   -- Custom site setting value
{{ settings['MyApp/MaxFileSize'] }}
```

---

## Querying Dataverse Data

The `entitylist`, `entityview`, and `fetchxml` objects let you query Dataverse directly in Liquid.

### Using FetchXML

FetchXML is Dataverse's query language — similar to SQL but XML-based:

```liquid
{% fetchxml my_products %}
<fetch>
  <entity name="cr123_product">
    <attribute name="cr123_name" />
    <attribute name="cr123_price" />
    <attribute name="cr123_category" />
    <filter>
      <condition attribute="cr123_status" operator="eq" value="1" />
      <condition attribute="cr123_isactive" operator="eq" value="1" />
    </filter>
    <order attribute="cr123_name" descending="false" />
  </entity>
</fetch>
{% endfetchxml %}

{% for product in my_products.results.entities %}
<div class="product-card">
  <h3>{{ product.cr123_name }}</h3>
  <p class="price">{{ product.cr123_price | currency }}</p>
  <span class="badge">{{ product.cr123_category }}</span>
</div>
{% endfor %}
```

### Using entityview

Render a Dataverse view (pre-configured filter + columns):

```liquid
{% entityview logical_name:'cr123_product', name:'Active Products Catalogue' %}
  {% for product in entityview.records %}
    <div>{{ product.cr123_name }} — {{ product.cr123_price }}</div>
  {% endfor %}
{% endentityview %}
```

### Count Query

```liquid
{% fetchxml case_count %}
<fetch aggregate="true">
  <entity name="incident">
    <attribute name="incidentid" alias="count" aggregate="count" />
    <filter>
      <condition attribute="customerid" operator="eq" value="{{ currentuser.id }}" />
      <condition attribute="statecode" operator="eq" value="0" />
    </filter>
  </entity>
</fetch>
{% endfetchxml %}
You have {{ case_count.results.entities[0].count }} open cases.
```

---

## Filters

Filters transform values using the `|` pipe operator:

```liquid
{{ 'hello world' | capitalize }}        → Hello world
{{ 'HELLO' | downcase }}               → hello
{{ 'hello' | upcase }}                 → HELLO
{{ '  spaces  ' | strip }}             → spaces
{{ 'Hello, World!' | remove: 'World' }} → Hello, !
{{ 'Hello, World!' | replace: 'World', 'Power Pages' }} → Hello, Power Pages!

{{ 1234567.89 | currency }}            → SAR 1,234,567.89 (based on site locale)
{{ 1234567 | number_with_delimiter }}  → 1,234,567

{{ some_date | date: '%d %B %Y' }}    → 15 June 2024
{{ some_date | date_to_xmlschema }}   → 2024-06-15T00:00:00Z

{{ some_array | first }}              → first item
{{ some_array | last }}               → last item
{{ some_array | size }}               → count of items
{{ some_array | sort: 'name' }}       → sorted by name property
{{ some_array | where: 'status', 'Active' }} → filtered to active items
{{ some_array | map: 'name' }}        → array of just the name values
```

---

## Custom Templates

Instead of using the Design Studio page builder, you can write a fully custom HTML/Liquid template for a page.

### Creating a Custom Template

1. Power Pages Management app → Web Templates → + New
2. Name: "Product Detail Template"
3. Source (write Liquid + HTML):

```liquid
{% assign product_id = request.params['id'] %}

{% fetchxml product_data %}
<fetch top="1">
  <entity name="cr123_product">
    <all-attributes />
    <filter>
      <condition attribute="cr123_productid" operator="eq" value="{{ product_id }}" />
    </filter>
  </entity>
</fetch>
{% endfetchxml %}

{% assign product = product_data.results.entities[0] %}

{% if product %}
<div class="product-detail-page">
  <nav aria-label="breadcrumb">
    <ol class="breadcrumb">
      <li><a href="/">Home</a></li>
      <li><a href="/products">Products</a></li>
      <li class="active">{{ product.cr123_name }}</li>
    </ol>
  </nav>

  <div class="row">
    <div class="col-md-6">
      {% if product.cr123_image %}
        <img src="{{ product.cr123_image }}" alt="{{ product.cr123_name }}" class="img-fluid rounded" />
      {% else %}
        <div class="no-image-placeholder">No image available</div>
      {% endif %}
    </div>

    <div class="col-md-6">
      <h1>{{ product.cr123_name }}</h1>
      <p class="price display-4">{{ product.cr123_price | currency }}</p>
      
      <div class="product-badges mb-3">
        <span class="badge badge-primary">{{ product.cr123_category }}</span>
        {% if product.cr123_isnew %}
          <span class="badge badge-success">New Arrival</span>
        {% endif %}
        {% if product.cr123_stock == 0 %}
          <span class="badge badge-danger">Out of Stock</span>
        {% endif %}
      </div>

      <p class="description">{{ product.cr123_description }}</p>

      {% if currentuser %}
        <a href="/enquire?product={{ product.cr123_productid }}" 
           class="btn btn-primary btn-lg">
          Request a Quote
        </a>
      {% else %}
        <a href="/signin?returnUrl=/products/detail?id={{ product.cr123_productid }}" 
           class="btn btn-outline-primary">
          Sign in to request a quote
        </a>
      {% endif %}
    </div>
  </div>
</div>
{% else %}
<div class="alert alert-warning">
  Product not found. <a href="/products">Browse all products</a>
</div>
{% endif %}
```

---

## Reusable Snippets (Content Snippets)

Content Snippets let you define reusable Liquid/HTML blocks that appear across multiple pages — like components.

**Creating a snippet:**
1. Power Pages Management → Content Snippets → + New
2. Name: "contact-cta-banner"
3. Value (HTML + Liquid):

```html
<div class="cta-banner">
  <h3>Need help? We're here.</h3>
  <p>Our team is available Monday–Friday, 9am–6pm GST</p>
  <a href="/contact" class="btn btn-white">Contact Us</a>
</div>
```

**Using a snippet in any page template:**

```liquid
{% include 'contact-cta-banner' %}
```

---

## POC Task: Custom Product Detail Page

Build a product detail page using a custom Liquid template:
1. The page reads a product ID from the URL query string (?id=...)
2. FetchXML queries the product from Dataverse
3. Renders the product name, image, price, description, and category
4. Shows "Add to Enquiry" button for authenticated users, "Login to enquire" for anonymous
5. Shows "Out of Stock" badge if quantity = 0
6. Link from your product list page to this detail page

This demonstrates Liquid at its most powerful.

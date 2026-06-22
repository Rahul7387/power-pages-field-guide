# Module 10 — Capstone Project: GlobalMart Customer Self-Service Portal

> Build a production-grade customer portal for GlobalMart — a multinational retail chain. This project uses every concept from Modules 1–9 and results in a polished, board-presentable portal.

---

## Project Brief

**Client:** GlobalMart Retail Chain
**Objective:** Replace 40% of support call volume with self-service capabilities
**Target users:** 5,000 active customers (authenticated) + 50,000 monthly anonymous visitors
**Timeline:** 8–12 hours of hands-on work

---

## What You Will Build

### Site Map

```
GlobalMart Customer Portal
│
├── / (Home — Anonymous)
│   ├── Hero: "Manage your GlobalMart account, anytime"
│   ├── Feature highlights: Track orders, raise tickets, update profile
│   ├── Embedded Copilot Studio chatbot (bottom-right)
│   └── Latest promotions (from Promotions table)
│
├── /products (Product Catalogue — Anonymous)
│   ├── Searchable/filterable product list
│   └── /products/detail?id=xxx (Product detail page)
│
├── /stores (Store Locator — Anonymous)
│   ├── Google Maps with all store locations
│   └── Store search by city/country
│
├── /faq (Knowledge Base — Anonymous)
│   ├── Category navigation
│   ├── Article search
│   └── /faq/article?id=xxx (Article detail)
│
├── /signin  (Login page — auto-generated)
├── /register (Registration — auto-generated)
│
│── [AUTHENTICATED ONLY — requires login]
│
├── /dashboard (Customer Dashboard)
│   ├── KPI cards: Open Cases, Pending Orders, Loyalty Points
│   ├── Recent orders table (last 5)
│   ├── Open cases table
│   └── "Quick Actions" panel
│
├── /orders (Order History)
│   ├── Searchable/filterable order list
│   └── /orders/detail?id=xxx (Order detail + status timeline)
│
├── /cases (Support Cases)
│   ├── My cases list
│   ├── "New Case" button
│   └── /cases/detail?id=xxx (Case detail + messages)
│
├── /profile (My Profile)
│   ├── Personal details form (editable)
│   ├── Communication preferences
│   └── Password change
│
└── /returns (Return Requests)
    ├── My returns list
    └── /returns/new (New return request form)
```

---

## Phase 1 — Dataverse Data Model

### Tables to Create

#### 1. cr_Order (Customer Orders)

```
Columns:
  OrderNumber       (autonumber, format: ORD-{SEQNUM:6})
  OrderDate         (date, required)
  DeliveryDate      (date)
  Customer          (lookup → Contact, required)
  Store             (lookup → cr_Store)
  TotalAmount       (currency, required)
  Status            (choice: Processing/Shipped/Out for Delivery/Delivered/Cancelled)
  TrackingNumber    (text, 50 chars)
  DeliveryAddress   (multiline text)
  Notes             (multiline text)
```

#### 2. cr_OrderItem (Order Line Items)

```
Columns:
  Order             (lookup → cr_Order, required)
  Product           (lookup → cr_Product, required)
  Quantity          (whole number, required, min: 1)
  UnitPrice         (currency, required)
  LineTotal         (currency, calculated: Quantity × UnitPrice)
  Status            (choice: Pending/Packed/Shipped/Delivered/Returned)
```

#### 3. cr_Product (Product Catalogue)

```
Columns:
  ProductCode       (autonumber, format: PRD-{SEQNUM:5})
  ProductName       (text, 200 chars, required)
  Description       (multiline rich text)
  Category          (choice: Electronics/Furniture/Office/Appliances/Clothing)
  SubCategory       (text, 100 chars)
  Price             (currency, required)
  CostPrice         (currency)
  StockQty          (whole number)
  ImageURL          (URL)
  Brand             (text, 100 chars)
  IsActive          (yes/no, default: yes)
  Weight_kg         (decimal)
  LaunchDate        (date)
```

#### 4. cr_Store (Store Locations)

```
Columns:
  StoreName         (text, 200 chars, required)
  StoreCode         (autonumber, format: STR-{SEQNUM:4})
  City              (text, 100 chars, required)
  Country           (choice: SA/AE/KW/QA/BH/OM/UK/DE/FR/IN/SG)
  Address           (multiline text)
  Phone             (phone)
  Email             (email)
  Latitude          (decimal, required for map)
  Longitude         (decimal, required for map)
  OpeningHours      (multiline text, e.g. "Mon-Sat: 9am-10pm")
  StoreManager      (text, 100 chars)
  StoreType         (choice: Flagship/Standard/Mall/Express)
  IsOpen            (yes/no, default: yes)
  OpenedDate        (date)
```

#### 5. cr_SupportCase (Support Cases)

```
Columns:
  CaseNumber        (autonumber, format: CASE-{SEQNUM:6})
  Subject           (text, 300 chars, required)
  Description       (multiline text, required)
  Category          (choice: Order Issue/Product Defect/Delivery/Billing/General)
  Priority          (choice: High/Medium/Low, default: Medium)
  Status            (choice: Open/In Progress/Awaiting Customer/Resolved/Closed)
  Customer          (lookup → Contact, required)
  RelatedOrder      (lookup → cr_Order)
  AssignedTo        (lookup → SystemUser — internal agent)
  CreatedDate       (date, auto-set)
  ResolvedDate      (date)
  ResolutionNotes   (multiline text)
  SatisfactionScore (whole number, 1-5, filled by customer)
```

#### 6. cr_CaseMessage (Case Communication Thread)

```
Columns:
  Case              (lookup → cr_SupportCase, required)
  MessageText       (multiline text, required)
  SentBy            (choice: Customer/Agent)
  SentDate          (date+time, auto-set)
  Attachment        (file)
  IsRead            (yes/no, default: no)
```

#### 7. cr_ReturnRequest (Product Returns)

```
Columns:
  ReturnNumber      (autonumber, format: RET-{SEQNUM:5})
  OrderItem         (lookup → cr_OrderItem, required)
  Reason            (choice: Defective/Wrong Item/Changed Mind/Damaged Delivery/Other)
  Details           (multiline text)
  PreferredResolution (choice: Full Refund/Exchange/Store Credit/Repair)
  ProductCondition  (choice: Unopened/Opened/Damaged)
  Photos            (file — allow multiple)
  Status            (choice: Submitted/Under Review/Approved/Rejected/Completed)
  Customer          (lookup → Contact, required)
  SubmittedDate     (date, auto-set)
  ReviewedDate      (date)
  ReviewNotes       (multiline text)
```

#### 8. cr_FAQ_Article (Knowledge Base)

```
Columns:
  Title             (text, 300 chars, required)
  Category          (choice: Orders/Returns/Products/Account/Delivery/Other)
  Content           (multiline rich text, required)
  Tags              (multiline text, comma-separated)
  ViewCount         (whole number, auto-incremented)
  IsPublished       (yes/no)
  HelpfulCount      (whole number)
  NotHelpfulCount   (whole number)
  LastUpdated       (date)
```

#### 9. cr_Promotion (Active Promotions)

```
Columns:
  PromotionTitle    (text, 200 chars)
  Description       (multiline text)
  DiscountType      (choice: Percentage/Fixed Amount/Free Shipping)
  DiscountValue     (decimal)
  PromoCode         (text, 20 chars)
  StartDate         (date)
  EndDate           (date)
  ImageURL          (URL)
  IsActive          (yes/no)
```

---

## Phase 2 — Table Permissions

### Permission Matrix

| Table | Anonymous | Authenticated Customer | Support Agent (internal) |
|-------|-----------|----------------------|--------------------------|
| cr_Product | Read (Global) | Read (Global) | Read+Write (Global) |
| cr_Store | Read (Global) | Read (Global) | Read+Write (Global) |
| cr_FAQ_Article | Read (Global, IsPublished=true) | Read (Global) | Read+Write (Global) |
| cr_Promotion | Read (Global, IsActive=true) | Read (Global) | Read+Write (Global) |
| cr_Order | None | Read+Write (Contact scope) | Read (Global) |
| cr_OrderItem | None | Read (Parent: Order) | Read (Global) |
| cr_SupportCase | None | Read+Create+Write (Contact scope) | Read+Write (Global) |
| cr_CaseMessage | None | Read+Create (Parent: Case) | Read+Write+Create (Global) |
| cr_ReturnRequest | None | Read+Create (Contact scope) | Read+Write (Global) |

**Web Roles:**
- `Authenticated Users` — all logged-in customers
- `SupportAgent` — internal staff (access via Power Apps, not portal)

---

## Phase 3 — Portal Pages Build Guide

### Page 1: Home (Anonymous)

```
Section 1 (Full width, dark bg):
  Hero: "Your account. Your way." | Subtitle | [Sign In] [Create Account] buttons
  Conditional: if authenticated → "Welcome back, {{ currentuser.firstname }}! [Go to Dashboard]"

Section 2 (4-column):
  Icon + "Track Orders" + description
  Icon + "Raise a Ticket" + description
  Icon + "Easy Returns" + description
  Icon + "Store Locator" + description

Section 3 (Full width):
  "Latest Promotions" heading

  {% fetchxml promotions %}
  <fetch top="3">
    <entity name="cr_promotion">
      <attribute name="cr_promotiontitle" />
      <attribute name="cr_description" />
      <attribute name="cr_imageurl" />
      <attribute name="cr_promoCode" />
      <filter>
        <condition attribute="cr_isactive" operator="eq" value="1" />
        <condition attribute="cr_enddate" operator="gt" value="{{ now | date: '%Y-%m-%d' }}" />
      </filter>
    </entity>
  </fetch>
  {% endfetchxml %}

  <div class="promo-grid">
  {% for promo in promotions.results.entities %}
    <div class="promo-card">
      <img src="{{ promo.cr_imageurl }}" alt="{{ promo.cr_promotiontitle }}" />
      <h3>{{ promo.cr_promotiontitle }}</h3>
      <p>{{ promo.cr_description }}</p>
      <code class="promo-code">{{ promo.cr_promoCode }}</code>
    </div>
  {% endfor %}
  </div>

Section 4 (Full width):
  Embedded Copilot Studio chatbot component
```

### Page 2: Customer Dashboard (Authenticated)

```liquid
{% if currentuser == null %}
  {% redirect '/signin?returnUrl=/dashboard' %}
{% endif %}

{% assign contact_id = currentuser.id %}

<!-- Open Cases Count -->
{% fetchxml open_cases %}
<fetch aggregate="true">
  <entity name="cr_supportcase">
    <attribute name="cr_supportcaseid" alias="count" aggregate="count" />
    <filter>
      <condition attribute="cr_customer_customerid" operator="eq" value="{{ contact_id }}" />
      <condition attribute="cr_status" operator="in">
        <value>Open</value>
        <value>In Progress</value>
        <value>Awaiting Customer</value>
      </condition>
    </filter>
  </entity>
</fetch>
{% endfetchxml %}

<!-- Recent Orders -->
{% fetchxml recent_orders %}
<fetch top="5">
  <entity name="cr_order">
    <attribute name="cr_ordernumber" />
    <attribute name="cr_orderdate" />
    <attribute name="cr_totalamount" />
    <attribute name="cr_status" />
    <attribute name="cr_trackingnumber" />
    <filter>
      <condition attribute="cr_customer_customerid" operator="eq" value="{{ contact_id }}" />
    </filter>
    <order attribute="cr_orderdate" descending="true" />
  </entity>
</fetch>
{% endfetchxml %}

<div class="dashboard-container">
  <!-- KPI Row -->
  <div class="kpi-row">
    <div class="kpi-card">
      <div class="kpi-value">{{ open_cases.results.entities[0].count }}</div>
      <div class="kpi-label">Open Cases</div>
    </div>
    <!-- Add more KPI cards -->
  </div>

  <!-- Recent Orders Table -->
  <h2>Recent Orders</h2>
  <table class="table portal-table">
    <thead>
      <tr>
        <th>Order #</th><th>Date</th><th>Amount</th><th>Status</th><th>Actions</th>
      </tr>
    </thead>
    <tbody>
      {% for order in recent_orders.results.entities %}
      <tr>
        <td>{{ order.cr_ordernumber }}</td>
        <td>{{ order.cr_orderdate | date: '%d %b %Y' }}</td>
        <td>{{ order.cr_totalamount | currency }}</td>
        <td>
          <span class="badge badge-{% case order.cr_status %}
            {% when 'Delivered' %}success
            {% when 'Cancelled' %}danger
            {% when 'Processing' %}warning
            {% else %}info
          {% endcase %}">{{ order.cr_status }}</span>
        </td>
        <td><a href="/orders/detail?id={{ order.cr_orderid }}">View</a></td>
      </tr>
      {% endfor %}
    </tbody>
  </table>
</div>
```

### Page 3: Store Locator (Anonymous)

```html
<!-- Google Maps store locator -->
<div class="store-locator">
  <div class="search-bar">
    <input type="text" id="city-search" placeholder="Search by city or country..." />
    <button onclick="filterStores()">Find Stores</button>
  </div>
  <div id="store-map" style="height: 500px; border-radius: 12px;"></div>
  <div id="store-list" class="store-cards"></div>
</div>

<script>
let allStores = [];
let map;

async function initStoreMap() {
  const response = await fetch(
    '/_api/cr_store?$filter=cr_isopen eq true&$select=cr_storename,cr_city,cr_country,cr_latitude,cr_longitude,cr_address,cr_phone,cr_openinghours,cr_storetype'
  );
  const data = await response.json();
  allStores = data.value;
  
  map = new google.maps.Map(document.getElementById('store-map'), {
    center: { lat: 25.0, lng: 50.0 },
    zoom: 5,
    styles: [ /* custom map style */ ]
  });
  
  renderStores(allStores);
}

function renderStores(stores) {
  stores.forEach(store => {
    const marker = new google.maps.Marker({
      position: { lat: store.cr_latitude, lng: store.cr_longitude },
      map,
      title: store.cr_storename,
      icon: '/store-marker.png'
    });
    
    const infoWindow = new google.maps.InfoWindow({
      content: `
        <div class="store-info-window">
          <h4>${store.cr_storename}</h4>
          <p>${store.cr_address}</p>
          <p>📞 ${store.cr_phone}</p>
          <p>🕐 ${store.cr_openinghours}</p>
        </div>
      `
    });
    
    marker.addListener('click', () => infoWindow.open(map, marker));
  });
  
  renderStoreCards(stores);
}

function filterStores() {
  const query = document.getElementById('city-search').value.toLowerCase();
  const filtered = allStores.filter(s =>
    s.cr_city.toLowerCase().includes(query) ||
    s.cr_country.toLowerCase().includes(query)
  );
  renderStores(filtered);
}
</script>
<script async src="https://maps.googleapis.com/maps/api/js?key=YOUR_KEY&callback=initStoreMap"></script>
```

---

## Phase 4 — Power Automate Flows

### Flow 1: New Case → Notify + Create Task

```
Trigger: When a new cr_supportcase row is added

Steps:
1. Send email to customer (from Dataverse row data)
   Subject: "Case #{CaseNumber} received — {Subject}"
   Body: HTML email with case details and expected response time

2. Post Teams message to Support team channel
   Message: "🎫 New case #{CaseNumber} from {CustomerName}
             Category: {Category} | Priority: {Priority}"

3. Create Planner task in Support board
   Title: "{CaseNumber}: {Subject}"
   Due date: + 24 hours for High, + 72 hours for Medium
```

### Flow 2: Return Request → Approval

```
Trigger: When cr_returnrequest status = "Submitted"

Steps:
1. Start approval (Approver: returns.manager@globalmart.com)
   Title: "Return #{ReturnNumber} — {Reason}"
   Details: Product, Order, Photos attached

Wait for approval

If Approved:
2a. Update ReturnRequest status = "Approved"
2b. Email customer: "Your return #{ReturnNumber} has been approved"
2c. If PreferredResolution = "Full Refund": create refund record

If Rejected:
2a. Update ReturnRequest status = "Rejected"
2b. Email customer with rejection reason
```

### Flow 3: New Case Message → Email Notification

```
Trigger: When cr_casemessage row added AND SentBy = "Agent"

Steps:
1. Get the related Case record
2. Get the Customer's email from Contact
3. Send email: "New reply on your case #{CaseNumber}"
4. Update CaseMessage IsRead = false (customer needs to read)
```

---

## Phase 5 — Copilot Studio Agent

Create an agent named "GlobalMart Assistant" with these topics:

### Topic 1: Order Status

```
Trigger phrases: "order status", "where is my order", "track my order", "order {number}"

Flow:
1. "I can look up your order status. What's your order number?"
2. [User enters order number]
3. Call Power Automate flow: Get-Order-Status (queries cr_order table)
4. "Your order {OrderNumber} is currently: {Status}. 
   {if Shipped} Tracking number: {TrackingNumber} {endif}
   Expected delivery: {DeliveryDate}"
5. "Is there anything else I can help you with?"
```

### Topic 2: Raise a Support Case

```
Trigger phrases: "raise a ticket", "support", "problem", "issue", "complaint"

Flow:
1. "I'm sorry to hear you're having an issue. I'll help you raise a case.
   What is the issue about?" [Show: Order Issue/Product Defect/Delivery/Billing/General]
2. [User selects category]
3. "Please briefly describe the issue:"
4. [User enters description]
5. "One moment while I create a case for you..."
6. Call Power Automate: Create-Support-Case (creates cr_supportcase)
7. "I've created case #{CaseNumber} for you. 
   Our team will respond within 24 hours.
   You can track your case at /cases"
```

### Topic 3: FAQ Lookup

```
Trigger phrases: "how do I", "what is", "help with", "can I", "where"

Flow:
1. Call Power Automate: Search-FAQ (searches cr_faq_article content)
2. If results found:
   "Here's what I found: [Article Title]
    {First 200 chars of content}...
    Read the full article: /faq/article?id={ArticleId}"
3. If no results:
   "I couldn't find an article on that topic.
    Would you like me to connect you with our support team?
    [Yes, connect me] [No thanks]"
```

---

## Phase 6 — Security Configuration

```
Web Roles:
  "Authenticated Users" (system role, all logged-in users)
  
Authentication:
  Enable: Microsoft Entra ID (or Google for testing)
  Local accounts: Enabled (for test users)
  Self-registration: Enabled (customers can register themselves)
  
Table Permissions:
  (Configure exactly as in the Permission Matrix above)

Page Access:
  /dashboard, /orders/*, /cases/*, /profile, /returns/*
  → Authenticated Users only
  → Redirect to /signin if not logged in
  
  /products, /stores, /faq
  → Anonymous + Authenticated (public)
```

---

## Phase 7 — UI Theme & Branding

```
Primary Color:    #1F3864 (GlobalMart navy)
Secondary Color:  #FFC000 (GlobalMart gold)
Background:       #F8FAFC (near white)
Text:             #1A1A2E (near black)
Success:          #00B050 (green)
Alert:            #C00000 (red)
Font:             Inter (from Google Fonts)

Logo: GlobalMart wordmark (SVG)
Favicon: 32×32 globe icon

Custom CSS:
  - Card hover lift effect
  - Status badge color system (each order/case status has a distinct color)
  - Responsive tables (horizontal scroll on mobile)
  - Loading spinner for Web API calls
```

---

## Phase 8 — Deployment

```
1. Dev environment: build and test everything
2. Export solution (managed) from Dev
3. Import to QA → run through full test checklist
4. UAT with 5 real test customers (ask colleagues to register and use the portal)
5. Fix issues found in UAT → re-deploy to QA
6. Import managed solution to Production
7. Configure custom domain: customers.globalmart.com
8. SSL: automatically managed by Power Pages
9. Configure authenticated user capacity: start with 100-user pack
10. Announce to customers + monitor adoption in Power Platform admin center
```

---

## Final Deliverable Checklist

- [ ] All 9 Dataverse tables created with correct column types
- [ ] Table permissions configured per the permission matrix
- [ ] All site pages built (11 pages)
- [ ] Authentication configured (Entra ID or local + Google)
- [ ] Store locator map working with real coordinates
- [ ] Customer dashboard shows real data from Dataverse (Liquid FetchXML)
- [ ] All forms create/update correct Dataverse records
- [ ] 3 Power Automate flows running correctly
- [ ] Copilot Studio agent embedded on home page with 3 working topics
- [ ] Security: tested as an anonymous user (cannot see /dashboard, /orders, etc.)
- [ ] Security: tested as Customer A (cannot see Customer B's orders/cases)
- [ ] Brand theme applied consistently across all pages
- [ ] Mobile responsive (tested at 375px width — iPhone screen)
- [ ] Portal published and accessible at public URL
- [ ] Capacity licence configured in Power Platform admin center

**Estimated build time:** 10–15 hours (spread across several sessions)

---

## What This Project Demonstrates to Your Manager

✅ External portal accessible without M365 accounts  
✅ Secure self-service: customers manage their own data  
✅ Row-level security: Customer A cannot see Customer B's records  
✅ Real-time data: no manual exports or PDF reports  
✅ AI chatbot: handles common queries automatically  
✅ Power Automate automation: email, Teams, approvals  
✅ Full ALM: dev/QA/prod environments  
✅ Custom domain and SSL  
✅ Scalable licensing model  

**Business impact statement:** "This portal can deflect 35–40% of inbound support calls, saving approximately 2,500 support hours annually while improving customer satisfaction through 24/7 self-service access."

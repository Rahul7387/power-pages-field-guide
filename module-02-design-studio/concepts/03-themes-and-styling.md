# 03 — Themes & Styling

## Design Studio Styling Workspace

Click the **Styling** tab in the left panel to access the full theme editor.

### Color Palette

Power Pages uses a token-based color system. Set once, apply everywhere:

| Token | Usage |
|-------|-------|
| Primary color | Buttons, links, active nav items |
| Secondary color | Hover states, accents |
| Background color | Page background |
| Text color | Body text |
| Header background | Navigation bar |
| Header text | Navigation links |
| Footer background | Footer section |
| Footer text | Footer links and text |

**Best practice:** Choose a primary color that meets WCAG AA contrast ratio (4.5:1) against white.

### Typography

```
Font family:     Choose from Google Fonts (500+ options)
                 e.g., Inter, Roboto, Open Sans, Lato, Poppins

Base font size:  16px recommended (WCAG minimum)
Heading scale:   H1 → H6 sizes automatically scaled

Custom fonts:    Upload a .woff2 file and reference it in Custom CSS
```

### Logo and Favicon

- **Logo:** Appears in navigation bar. Recommended: SVG (scalable) or PNG (transparent background), ~200×60px
- **Favicon:** Browser tab icon. Exactly 32×32px or 16×16px, ICO or PNG format

### Buttons

Customise for each button variant:
- Background color, hover color, text color, border, border-radius
- Apply consistently across the site via the theme (no per-button styling needed)

---

## Custom CSS

For styling beyond what the theme editor offers:

**In Design Studio:** Click `</>` (Code editor) → Custom CSS

```css
/* Example: custom nav style */
.header-nav {
  box-shadow: 0 2px 8px rgba(0,0,0,0.1);
}

/* Card hover effect */
.card-gallery-item:hover {
  transform: translateY(-4px);
  transition: transform 0.2s ease;
  box-shadow: 0 8px 24px rgba(0,0,0,0.12);
}

/* Custom announcement banner */
.portal-announcement {
  background: linear-gradient(135deg, #1F3864, #2E75B6);
  color: white;
  padding: 12px 24px;
  text-align: center;
  font-size: 14px;
}

/* Responsive: hide sidebar on mobile */
@media (max-width: 768px) {
  .sidebar-column { display: none; }
  .main-column { width: 100%; }
}
```

**Where to add CSS:**
- Design Studio → Styling → Custom CSS (applies to all pages)
- Page-level custom CSS: Set up → Site settings → Header/Footer HTML injection

---

## CSS Class Utility System

Power Pages includes Bootstrap 4 (and its utility classes) by default:

```html
<!-- Spacing: m = margin, p = padding, t/b/l/r = top/bottom/left/right, 0-5 = size -->
<div class="mt-4 mb-2 px-3">Content</div>

<!-- Text utilities -->
<p class="text-center text-muted font-weight-bold">Centered bold muted text</p>

<!-- Grid (Bootstrap 12-column) -->
<div class="row">
  <div class="col-md-8">Main content</div>
  <div class="col-md-4">Sidebar</div>
</div>

<!-- Alerts -->
<div class="alert alert-success">Your form was submitted successfully!</div>
<div class="alert alert-danger">An error occurred. Please try again.</div>
```

---

## POC Task

Apply a consistent brand theme:
1. Set primary color: #2E75B6 (professional blue)
2. Set font: Inter (Google Fonts)
3. Upload a logo (can be a placeholder SVG)
4. Add a custom CSS rule: cards have a hover lift effect
5. Verify the theme looks good on mobile (resize browser to 375px width)

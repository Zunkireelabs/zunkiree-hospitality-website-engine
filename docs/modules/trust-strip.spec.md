# Module Specification: trust-strip

**Module Name:** trust-strip
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Last Updated:** 2026-01-16

---

## 1. PURPOSE

Displays a row of trust badges/social proof indicators. Typically used below the hero section to establish credibility with visitors through visual trust signals (ratings, certifications, guarantees).

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.trust_badges` | array | Array of badge objects |
| `badge.title` | string | Badge headline text |
| `badge.description` | string | Badge supporting text |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `badge.icon` | string | No icon rendered if missing or unrecognized |

### Supported Icon Values

| Value | Description |
|-------|-------------|
| `plane` | Airplane icon |
| `utensils` | Fork/knife icon |
| `family` | People group icon |
| `star` | Star icon |
| `shield` | Shield with checkmark icon |
| `clock` | Clock icon |

Any other value or missing icon renders an empty icon container.

---

## 3. ACTIVATION RULE

Module renders ONLY IF:

```json
{
  "engine": {
    "modules": {
      "trust_strip": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Rules:**
- Missing flag = disabled
- `false` = disabled
- Module renders nothing when disabled
- CSS only loads when module is enabled

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/trust-strip/trust-strip.liquid` | HTML structure |
| CSS | `src/assets/css/modules/trust-strip.css` | Scoped styles |
| JS | None | Module has no JavaScript |

---

## 5. INCLUDE PATH

```liquid
{% include "modules/trust-strip/trust-strip.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.trust_strip -%}
<link rel="stylesheet" href="/assets/css/modules/trust-strip.css">
{%- endif -%}
```

---

## 7. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No console errors

### If data missing:
- `homepage.trust_badges` missing → renders nothing
- `homepage.trust_badges` empty array → renders empty section
- `badge.icon` unrecognized → renders item without icon
- `badge.title` missing → renders empty title
- `badge.description` missing → renders empty description

---

## 8. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h4>`, `<p>`)
- SVG icons have no explicit labels (decorative)
- Content is readable without icons
- Color contrast maintained via design tokens

---

## 9. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--space-1` | Title margin |
| `--space-3` | Item gap |
| `--space-4` | Mobile padding |
| `--space-5` | Section padding |
| `--space-6` | Mobile padding, tablet gap |
| `--space-8` | Section padding, desktop gap |
| `--container-2xl` | Max width |
| `--text-sm` | Description font size |
| `--text-base` | Title font size |
| `--font-semibold` | Title weight |
| `--color-primary` | Icon color |
| `--color-text-secondary` | Description color |

---

## 10. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1200px) | 4 items in row, flex layout |
| Tablet (768-1200px) | 2 items per row, wrapped |
| Mobile (<768px) | Single column, stacked |

---

## 11. NON-FUNCTIONAL REQUIREMENTS

- No global state mutation
- No dependency on other modules
- No assumptions about page order
- Safe to remove at any time
- Self-contained CSS (no external dependencies except design tokens)

---

## 12. EXAMPLE DATA

```json
{
  "trust_badges": [
    {
      "icon": "star",
      "title": "4.9 Rating",
      "description": "1000+ Reviews"
    },
    {
      "icon": "shield",
      "title": "Safe & Secure",
      "description": "Verified Payments"
    },
    {
      "icon": "clock",
      "title": "24/7 Support",
      "description": "Always Available"
    },
    {
      "icon": "plane",
      "title": "ATOL Protected",
      "description": "Financial Security"
    }
  ]
}
```

---

## 13. MIGRATION NOTES

### For Projects Using This Module

1. Add to `site.json`:
```json
{
  "engine": {
    "modules": {
      "trust_strip": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "trust-strip.liquid" %}

<!-- New -->
{% include "modules/trust-strip/trust-strip.liquid" %}
```

3. Update template submodule reference

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

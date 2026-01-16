# Module Specification: inclusions

**Module Name:** inclusions
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Last Updated:** 2026-01-16

---

## 1. PURPOSE

Displays a "What's Included" checklist section with icons, titles, and descriptions. Typically used to show package inclusions for travel/hospitality offerings (flights, meals, transfers, etc.).

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.inclusions_section` | object | Section configuration |
| `homepage.inclusions_section.inclusions` | array | Array of inclusion items (must have at least 1 item) |
| `item.title` | string | Inclusion item title |
| `item.description` | string | Inclusion item description |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.inclusions_section.headline` | string | No headline rendered |
| `homepage.inclusions_section.intro` | string | No intro paragraph rendered |
| `homepage.inclusions_section.disclaimer` | string | No disclaimer note rendered |
| `item.icon` | string | No icon rendered if missing or unrecognized |

### Supported Icon Values

| Value | Description |
|-------|-------------|
| `plane` | Airplane icon |
| `bus` | Bus/transport icon |
| `utensils` | Fork/knife icon |
| `snowflake` | Snowflake icon |
| `jacket` | Winter jacket icon |

Any other value or missing icon renders an empty icon container.

---

## 3. ACTIVATION RULE

Module renders ONLY IF:

```json
{
  "engine": {
    "modules": {
      "inclusions": true
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
| Template | `src/_includes/modules/inclusions/inclusions.liquid` | HTML structure |
| CSS | `src/assets/css/modules/inclusions.css` | Scoped styles |
| JS | None | Module has no JavaScript |

---

## 5. INCLUDE PATH

```liquid
{% include "modules/inclusions/inclusions.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.inclusions -%}
<link rel="stylesheet" href="/assets/css/modules/inclusions.css">
{%- endif -%}
```

---

## 7. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No console errors

### If data missing:
- `homepage.inclusions_section` missing → renders nothing
- `homepage.inclusions_section.inclusions` missing → renders nothing
- `homepage.inclusions_section.inclusions` empty array → renders nothing
- `item.icon` unrecognized → renders item without icon
- `item.title` missing → renders empty title
- `item.description` missing → renders empty description

---

## 8. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h2>`, `<h4>`, `<p>`)
- SVG icons have no explicit labels (decorative)
- Content is readable without icons
- Color contrast maintained via design tokens

---

## 9. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--space-2` | Small padding |
| `--space-3` | Gap, padding, margin |
| `--space-4` | Padding, gap |
| `--space-6` | Gap |
| `--space-8` | Padding, margin |
| `--space-10` | Section padding, margin |
| `--space-12` | Section padding |
| `--container-2xl` | Max width |
| `--text-xs` | Small text |
| `--text-sm` | Description text |
| `--text-base` | Item title text |
| `--text-lg` | Intro text |
| `--color-text-secondary` | Muted text color |

---

## 10. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `#FDF8F3` | Section background | `--color-bg-warm` |
| `black` | Title color | `--color-text-primary` |
| `black` | Icon color | `--color-text-primary` |
| `black` | Item title color | `--color-text-primary` |
| `rgba(0, 0, 0, 0.08)` | Note border | `--color-border-light` |
| `2.5rem` | Desktop title size | `--text-4xl` or custom |
| `2rem` | Mobile title size | `--text-3xl` or custom |
| `600` | Item title weight | `--font-semibold` |
| `400` | Section title weight | `--font-normal` |

---

## 11. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1200px) | 5 columns grid |
| Tablet (900-1200px) | 3 columns grid |
| Tablet (768-900px) | 2 columns grid |
| Mobile (480-768px) | 2 columns grid, reduced spacing |
| Small mobile (<480px) | 2 columns grid, minimal spacing |

---

## 12. NON-FUNCTIONAL REQUIREMENTS

- No global state mutation
- No dependency on other modules
- No assumptions about page order
- Safe to remove at any time
- Self-contained CSS (no external dependencies except design tokens)

---

## 13. EXAMPLE DATA

```json
{
  "inclusions_section": {
    "headline": "What's Included",
    "intro": "Everything you need for your perfect Lapland adventure",
    "inclusions": [
      {
        "icon": "plane",
        "title": "Return Flights",
        "description": "Direct flights from UK airports"
      },
      {
        "icon": "bus",
        "title": "Airport Transfers",
        "description": "Comfortable coach transfers"
      },
      {
        "icon": "utensils",
        "title": "Full Board Meals",
        "description": "Breakfast, lunch & dinner"
      },
      {
        "icon": "snowflake",
        "title": "Winter Activities",
        "description": "Husky safaris & snowmobiles"
      },
      {
        "icon": "jacket",
        "title": "Thermal Clothing",
        "description": "Full thermal suit provided"
      }
    ],
    "disclaimer": "Activities subject to weather conditions. See full terms."
  }
}
```

---

## 14. MIGRATION NOTES

### For Projects Using This Module

1. Add to `site.json`:
```json
{
  "engine": {
    "modules": {
      "inclusions": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "inclusions-section.liquid" %}

<!-- New -->
{% include "modules/inclusions/inclusions.liquid" %}
```

3. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

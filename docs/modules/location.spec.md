# Module Specification: location

**Module Name:** location
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** MEDIUM
**Risk Level:** MEDIUM
**Last Updated:** 2026-01-18

---

## 1. PURPOSE

Displays location information with a bento-style image grid, feature checklist, and optional statistics. Used to showcase destination details with visual imagery and key selling points.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.location_section` | object | Location section configuration |
| `homepage.location_section.features` | array | Array of feature strings (must be non-empty) |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.location_section.headline` | string | No headline rendered |
| `homepage.location_section.intro` | string | No intro paragraph rendered |
| `homepage.location_section.subheadline` | string | No features title rendered |
| `homepage.location_section.images` | object | No images rendered |
| `homepage.location_section.images.left` | string | Left bento slot empty |
| `homepage.location_section.images.middle_top` | string | Middle top slot empty |
| `homepage.location_section.images.middle_bottom` | string | Middle bottom slot empty |
| `homepage.location_section.images.right` | string | Right bento slot empty |
| `homepage.location_section.stats` | array | Stats section not rendered |
| `homepage.location_section.stats[].icon` | string | Default circle icon |
| `homepage.location_section.stats[].number` | string | Stat number |
| `homepage.location_section.stats[].label` | string | Stat label |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.location` is `true`
2. `homepage.location_section` exists
3. `homepage.location_section.features` exists
4. `homepage.location_section.features.size > 0`

```json
{
  "engine": {
    "modules": {
      "location": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- if site.engine.modules.location
  and homepage.location_section
  and homepage.location_section.features
  and homepage.location_section.features.size > 0
-%}
```

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/location/location.liquid` | HTML structure |
| CSS | `src/assets/css/modules/location.css` | Scoped styles |
| JS | None | Module has no JavaScript |

---

## 5. INCLUDE PATH

```liquid
{% include "modules/location/location.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.location -%}
<link rel="stylesheet" href="/assets/css/modules/location.css">
{%- endif -%}
```

---

## 7. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No console errors

### If data missing:
- `homepage.location_section` missing → renders nothing
- `homepage.location_section.features` missing → renders nothing
- `homepage.location_section.features` empty array → renders nothing
- `homepage.location_section.headline` missing → no headline rendered
- `homepage.location_section.intro` missing → no intro rendered
- `homepage.location_section.images` missing → bento grid empty
- `homepage.location_section.stats` missing → stats section not rendered

---

## 8. RENDERING MODES

| Mode | Condition | Features |
|------|-----------|----------|
| Standard | features array exists | Header, bento grid, features list |
| With Stats | stats array provided | Adds stats section below features |

---

## 9. JAVASCRIPT FUNCTIONALITY

This module has no JavaScript.

---

## 10. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h2>`, `<h4>`, `<ul>`, `<li>`)
- Images have alt text from headline
- SVG icons are decorative
- Color contrast maintained via design tokens

---

## 11. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-white` | Section background |
| `--color-primary` | Feature icons, stats icons |
| `--color-text-primary` | Feature text, stat numbers |
| `--color-text-secondary` | Intro text, stat labels |
| `--color-frost` | Mobile stat item background |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--font-*` | Font weights |
| `--radius-lg`, `--radius-md` | Border radius |
| `--container-2xl` | Max container width |

---

## 12. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `2.5rem` | Desktop headline | `--text-3xl` |
| `2rem` | Mobile headline | `--text-2xl` |
| `400px` | Bento grid height desktop | `--bento-height-lg` |
| `250px` | Bento grid height tablet | `--bento-height-md` |
| `200px` | Bento left/right mobile | `--bento-height-sm` |
| `150px` | Bento middle mobile | `--bento-height-xs` |
| `48px` | Stat icon size | `--icon-size-lg` |
| `black` | Title color | `--color-text-primary` |
| `rgba(135,28,83,0.08)` | Stat icon background | `--color-primary-alpha-08` |
| `1fr 1fr 1.5fr` | Bento grid columns | Custom layout |

---

## 13. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1024px) | 3-column bento (1:1:1.5), 400px height |
| Tablet (768-1024px) | 2-column bento, 250px height, right spans 2 |
| Mobile (<768px) | 1-column bento, varied heights, stats stacked |

---

## 14. ICON MAPPING

The stats section supports the following icon values:

| Icon Value | Description |
|------------|-------------|
| `plane` | Airplane icon |
| `globe` | Globe/world icon |
| `map-pin` | Location marker |
| `mountain` | Mountain icon |
| `clock` | Clock/time icon |
| `building` | Building icon |
| (default) | Circle icon |

---

## 15. NON-FUNCTIONAL REQUIREMENTS

- No global state mutation
- No dependency on other modules
- No assumptions about page order
- Safe to remove at any time
- Self-contained CSS, no JavaScript

---

## 16. EXAMPLE DATA

```json
{
  "location_section": {
    "headline": "Discover Lapland",
    "intro": "Experience the magic of Finnish Lapland, where the northern lights dance across pristine wilderness.",
    "images": {
      "left": "/assets/media/location/aurora.jpg",
      "middle_top": "/assets/media/location/cabin.jpg",
      "middle_bottom": "/assets/media/location/reindeer.jpg",
      "right": "/assets/media/location/landscape.jpg"
    },
    "subheadline": "What Families Love",
    "features": [
      "Direct flights from major European cities",
      "Safe, family-friendly environment",
      "Authentic Arctic experiences",
      "English widely spoken"
    ],
    "stats": [
      { "icon": "plane", "number": "3h", "label": "From London" },
      { "icon": "globe", "number": "66°N", "label": "Arctic Circle" },
      { "icon": "map-pin", "number": "Rovaniemi", "label": "Gateway City" }
    ]
  }
}
```

---

## 17. MIGRATION NOTES

### For Projects Using This Module

1. Add to `site.json`:
```json
{
  "engine": {
    "modules": {
      "location": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "location-section.liquid" %}

<!-- New -->
{% include "modules/location/location.liquid" %}
```

3. Ensure data has `location_section` with non-empty `features` array

4. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

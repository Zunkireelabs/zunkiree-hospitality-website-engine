# Module Specification: why

**Module Name:** why
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** LOW
**Risk Level:** LOW
**Last Updated:** 2026-01-16

---

## 1. PURPOSE

Displays a benefits/features highlight section with a headline, intro text, subheadline, and a grid of benefit cards with icons, titles, and descriptions. Each card has a background image with hover effects.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.why_section` | object | Why section configuration |
| `homepage.why_section.benefits` | array | Array of benefit objects (must be non-empty) |
| `homepage.why_section.benefits[].title` | string | Benefit card title |
| `homepage.why_section.benefits[].description` | string | Benefit card description |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.why_section.headline` | string | No headline rendered |
| `homepage.why_section.intro` | string | No intro paragraph rendered |
| `homepage.why_section.subheadline` | string | No subheadline rendered |
| `homepage.why_section.benefits[].icon` | string | No icon rendered |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.why` is `true`
2. `homepage.why_section` exists
3. `homepage.why_section.benefits` exists
4. `homepage.why_section.benefits.size > 0`

```json
{
  "engine": {
    "modules": {
      "why": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- if site.engine.modules.why and homepage.why_section and homepage.why_section.benefits and homepage.why_section.benefits.size > 0 -%}
```

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/why/why.liquid` | HTML structure |
| CSS | `src/assets/css/modules/why.css` | Scoped styles |
| JS | None | Module has no JavaScript |

---

## 5. INCLUDE PATH

```liquid
{% include "modules/why/why.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.why -%}
<link rel="stylesheet" href="/assets/css/modules/why.css">
{%- endif -%}
```

---

## 7. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No console errors

### If data missing:
- `homepage.why_section` missing → renders nothing
- `homepage.why_section.benefits` missing → renders nothing
- `homepage.why_section.benefits` empty array → renders nothing
- `homepage.why_section.headline` missing → no headline rendered
- `homepage.why_section.intro` missing → no intro rendered
- `homepage.why_section.subheadline` missing → no subheadline rendered

---

## 8. RENDERING MODES

| Mode | Condition | Features |
|------|-----------|----------|
| Standard | benefits array exists | 4-column grid with benefit cards |

---

## 9. JAVASCRIPT FUNCTIONALITY

This module has no JavaScript.

---

## 10. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h2>`, `<h3>`, `<p>`)
- SVG icons are decorative (no alt text needed)
- Color contrast maintained via design tokens

---

## 11. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-white` | Section background |
| `--color-primary` | Headline color |
| `--color-text-primary` | Subheadline color |
| `--color-text-secondary` | Intro text color |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--font-*` | Font weights |
| `--transition-normal` | Card transitions |
| `--radius-lg` | Card border radius |
| `--container-2xl` | Max container width |

---

## 12. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `2.5rem` | Desktop headline | `--text-3xl` |
| `2rem` | Mobile headline | `--text-2xl` |
| `280px` | Card min-height | `--card-min-height-lg` |
| `220px` | Mobile card min-height | `--card-min-height-md` |
| `rgba(0,0,0,0.03)` | Card background | `--color-bg-card-subtle` |
| `rgba(0,0,0,0.05)` | Card hover | `--color-bg-card-hover` |
| `rgba(0,0,0,0.12)` | Card shadow | `--shadow-card-hover` |
| `rgba(0,0,0,0.25)` | Gradient overlay | `--overlay-gradient-dark` |
| `rgba(0,0,0,0.15)` | Hover gradient overlay | `--overlay-gradient-light` |
| `rgba(0,0,0,0.5)` | Text shadow | `--shadow-text` |
| `rgba(135, 28, 83, 0.9)` | Icon background | `--color-primary-alpha-90` |
| Unsplash URLs | Background images | Project data (not tokens) |

---

## 13. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1024px) | 4-column grid, 280px card min-height |
| Tablet (768-1024px) | 2-column grid |
| Mobile (<768px) | 1-column grid, 220px card min-height |

---

## 14. NON-FUNCTIONAL REQUIREMENTS

- No global state mutation
- No dependency on other modules
- No assumptions about page order
- Safe to remove at any time
- Self-contained CSS, no JavaScript

---

## 15. EXAMPLE DATA

```json
{
  "why_section": {
    "headline": "Why Choose Us",
    "intro": "Experience the difference with our dedicated service and attention to detail.",
    "subheadline": "Four reasons families trust us",
    "benefits": [
      {
        "title": "Full-Board Comfort",
        "description": "All meals included with local and international cuisine"
      },
      {
        "title": "Guided Experiences",
        "description": "Expert-led activities tailored for all ages"
      },
      {
        "title": "Authentic Location",
        "description": "Stay in the heart of the destination"
      },
      {
        "title": "Family Support",
        "description": "Dedicated concierge for family needs"
      }
    ]
  }
}
```

---

## 16. MIGRATION NOTES

### For Projects Using This Module

1. Add to `site.json`:
```json
{
  "engine": {
    "modules": {
      "why": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "why-section.liquid" %}

<!-- New -->
{% include "modules/why/why.liquid" %}
```

3. Ensure data has `why_section` with non-empty `benefits` array

4. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

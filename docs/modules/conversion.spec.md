# Module Specification: conversion

**Module Name:** conversion
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** LOW
**Risk Level:** LOW
**Last Updated:** 2026-01-18

---

## 1. PURPOSE

Displays a conversion-focused CTA section with headline, call-to-action button, reassurance points, and optional image collage. Used to drive user action at key points in the page flow.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.conversion_section` | object | Conversion section configuration |
| `homepage.conversion_section.cta` | object | CTA button configuration |
| `homepage.conversion_section.cta.text` | string | Button text |
| `homepage.conversion_section.cta.url` | string | Button link URL |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.conversion_section.headline` | string | No headline rendered |
| `homepage.conversion_section.intro` | string | No intro text rendered |
| `homepage.conversion_section.reassurance` | array | No reassurance points rendered |
| `homepage.conversion_section.images` | array | No image collage rendered |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.conversion` is `true`
2. `homepage.conversion_section` exists
3. `homepage.conversion_section.cta` exists

```json
{
  "engine": {
    "modules": {
      "conversion": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- if site.engine.modules.conversion
  and homepage.conversion_section
  and homepage.conversion_section.cta
-%}
```

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/conversion/conversion.liquid` | HTML structure |
| CSS | `src/assets/css/modules/conversion.css` | Scoped styles |
| JS | None | Module has no JavaScript |

---

## 5. INCLUDE PATH

```liquid
{% include "modules/conversion/conversion.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.conversion -%}
<link rel="stylesheet" href="/assets/css/modules/conversion.css">
{%- endif -%}
```

---

## 7. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No console errors

### If data missing:
- `homepage.conversion_section` missing → renders nothing
- `homepage.conversion_section.cta` missing → renders nothing
- `homepage.conversion_section.headline` missing → no headline rendered
- `homepage.conversion_section.intro` missing → no intro rendered
- `homepage.conversion_section.reassurance` missing → no reassurance points rendered
- `homepage.conversion_section.images` missing → no image collage rendered

---

## 8. RENDERING MODES

| Mode | Condition | Features |
|------|-----------|----------|
| Minimal | CTA only | Just the CTA button |
| Standard | CTA + headline/intro | Headline, intro, and CTA |
| Full | All fields provided | Headline, intro, CTA, reassurance, image collage |

---

## 9. JAVASCRIPT FUNCTIONALITY

This module has no JavaScript.

---

## 10. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h2>`, `<ul>`, `<li>`)
- CTA is a proper anchor element
- Images have alt text from headline
- SVG icons are decorative
- Color contrast maintained via design tokens

---

## 11. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-frost` | Section background |
| `--color-white` | Card background |
| `--color-text-secondary` | Intro text, reassurance text |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--radius-xl`, `--radius-lg` | Border radius |
| `--container-2xl` | Max container width |
| `--transition-normal`, `--transition-fast` | Animations |

---

## 12. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `2.5rem` | Title desktop | `--text-3xl` |
| `2rem` | Title mobile | `--text-2xl` |
| `550px` | Content max-width | `--content-width-md` |
| `480px` | Collage max-width desktop | `--collage-width-lg` |
| `380px` | Collage max-width tablet | `--collage-width-md` |
| `320px` | Collage max-width mobile | `--collage-width-sm` |
| `420px` | Collage height desktop | `--collage-height-lg` |
| `350px` | Collage height tablet | `--collage-height-md` |
| `300px` | Collage height mobile | `--collage-height-sm` |
| `200px/220px` | Collage item widths | `--collage-item-*` |
| `black` | CTA background | `--color-text-primary` |
| `white` | CTA text color | `--color-white` |
| `#1a1a1a` | CTA hover bg | `--color-gray-900` |
| `#16a34a` | Checkmark color | `--color-success` |
| `rgba(0,0,0,0.06)` | Card shadow | `--shadow-sm` |
| `rgba(0,0,0,0.12)` | Collage shadow | `--shadow-md` |
| `rgba(0,0,0,0.15)` | CTA hover shadow | `--shadow-lg` |
| External URL | Background image | Project-specific asset |

---

## 13. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1024px) | 2-column grid, full collage size |
| Tablet (768-1024px) | 2-column grid, reduced collage |
| Mobile (<768px) | 1-column stack, images above content, centered text |

---

## 14. INTERNAL GUARDS

The images collage has its own conditional rendering:

```liquid
{% if homepage.conversion_section.images and homepage.conversion_section.images.size > 0 %}
```

This ensures the collage only renders when images are provided.

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
  "conversion_section": {
    "headline": "Ready to Start Planning?",
    "intro": "Get a personalized quote for your family's dream Lapland adventure. No obligation, just inspiration.",
    "cta": {
      "text": "Get Your Free Quote",
      "url": "#inquiry"
    },
    "reassurance": [
      "No obligation",
      "Personalized itinerary",
      "Expert advice"
    ],
    "images": [
      "/assets/media/conversion/family-snow.jpg",
      "/assets/media/conversion/northern-lights.jpg",
      "/assets/media/conversion/husky-ride.jpg"
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
      "conversion": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "conversion-section.liquid" %}

<!-- New -->
{% include "modules/conversion/conversion.liquid" %}
```

3. Ensure data has `conversion_section` with `cta` object containing `text` and `url`

4. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

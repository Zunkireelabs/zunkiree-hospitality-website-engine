# Module Specification: trip-length

**Module Name:** trip-length
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** MEDIUM
**Risk Level:** MEDIUM
**Last Updated:** 2026-01-16

---

## 1. PURPOSE

Displays a trip duration/package selector section with option cards showing different trip lengths, descriptions, ideal audience, and optional availability calendar. Each card includes an image, title, subtitle, description, and CTA button.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.trip_length_section.options` | array | Array of trip option objects (must be non-empty) |
| `homepage.trip_length_section.options[].title` | string | Option card title |
| `homepage.trip_length_section.options[].image` | string | Option card image URL |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.trip_length_section.headline` | string | No headline rendered |
| `homepage.trip_length_section.intro` | string | No intro paragraph rendered |
| `homepage.trip_length_section.note` | string | No note rendered |
| `homepage.trip_length_section.options[].subtitle` | string | No subtitle rendered |
| `homepage.trip_length_section.options[].description` | string | No description rendered |
| `homepage.trip_length_section.options[].ideal_for` | string | No ideal-for list rendered |
| `homepage.trip_length_section.show_availability` | boolean | Availability card not rendered |
| `homepage.trip_length_section.availability` | object | Availability card not rendered |
| `homepage.trip_length_section.availability.title` | string | Defaults to "2026 Dates" |
| `homepage.trip_length_section.availability.subtitle` | string | Defaults to "Winter Season Availability" |
| `homepage.trip_length_section.availability.months` | array | No months rendered |
| `homepage.trip_length_section.availability.months[].name` | string | Month name |
| `homepage.trip_length_section.availability.months[].dates` | string | Date range text |
| `homepage.trip_length_section.availability.months[].calendar` | array | Calendar day numbers |
| `homepage.trip_length_section.availability.features` | array | No features rendered |
| `homepage.trip_length_section.availability.cta` | object | No CTA rendered |
| `homepage.trip_length_section.availability.cta.url` | string | CTA link URL |
| `homepage.trip_length_section.availability.cta.text` | string | CTA button text |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.trip_length` is `true`
2. `homepage.trip_length_section.options.size > 0`

```json
{
  "engine": {
    "modules": {
      "trip_length": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- if site.engine.modules.trip_length and homepage.trip_length_section.options.size > 0 -%}
```

---

## 4. AVAILABILITY CARD RENDERING RULE

The availability card renders ONLY IF both conditions are met:

1. `homepage.trip_length_section.show_availability` is `true`
2. `homepage.trip_length_section.availability` object exists

**Guard Logic (inside template):**
```liquid
{% if homepage.trip_length_section.show_availability and homepage.trip_length_section.availability %}
```

**Failure Behavior:**
- `show_availability` is `false` → availability card not rendered
- `show_availability` is `true` but `availability` missing → availability card not rendered

---

## 5. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/trip-length/trip-length.liquid` | HTML structure |
| CSS | `src/assets/css/modules/trip-length.css` | Scoped styles |
| JS | None | Module has no JavaScript |

---

## 6. INCLUDE PATH

```liquid
{% include "modules/trip-length/trip-length.liquid" %}
```

---

## 7. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.trip_length -%}
<link rel="stylesheet" href="/assets/css/modules/trip-length.css">
{%- endif -%}
```

---

## 8. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No console errors

### If data missing:
- `homepage.trip_length_section.options` missing → renders nothing
- `homepage.trip_length_section.options` empty array → renders nothing
- `homepage.trip_length_section.headline` missing → no headline rendered
- `homepage.trip_length_section.intro` missing → no intro rendered
- `homepage.trip_length_section.note` missing → no note rendered
- Availability card conditions not met → no availability card rendered

---

## 9. RENDERING MODES

| Mode | Condition | Features |
|------|-----------|----------|
| Standard | options array exists | 3-column grid with option cards |
| With Availability | show_availability AND availability | Adds availability calendar card |

---

## 10. JAVASCRIPT FUNCTIONALITY

This module has no JavaScript.

---

## 11. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h2>`, `<h3>`, `<p>`, `<ul>`, `<li>`)
- SVG icons are decorative (no alt text needed)
- Images have alt text from option.title
- Color contrast maintained via design tokens

---

## 12. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-white` | Section/card backgrounds |
| `--color-text-secondary` | Secondary text |
| `--color-frost` | Calendar header background |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--radius-lg`, `--radius-md` | Border radius |
| `--container-2xl` | Max container width |

---

## 13. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `2.5rem` | Desktop headline | `--text-3xl` |
| `2rem` | Mobile headline | `--text-2xl` |
| `200px` | Card image height | `--card-image-height` |
| `160px` | Mobile card image height | `--card-image-height-sm` |
| `black` | Title, icon, button | `--color-text-primary` |
| `white` | Button text color | `--color-white` |
| `rgba(0,0,0,0.08)` | Border color | `--color-border-light` |
| `rgba(0,0,0,0.06)` | Calendar border | `--color-border-subtle` |
| `#FDF8F3` | Availability card bg | `--color-bg-warm` |
| `#FDD5A9` | Button icon color | `--color-accent-warm` |
| `rgba(34,197,94,0.15)` | Available day bg | `--color-success-light` |
| `#16a34a` | Available day text | `--color-success` |
| `10px` | Border radius | `--radius-sm` |
| `9px`, `10px` | Calendar font sizes | `--text-2xs` |
| `20px` | Calendar cell height | `--calendar-cell` |
| `6px` | List bullet size | `--bullet-size` |

---

## 14. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1100px) | 3-column grid, 200px image height |
| Tablet (768-1100px) | 2-column grid, availability spans 2 columns |
| Mobile (<768px) | 1-column grid, 160px image height |

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
  "trip_length_section": {
    "headline": "Choose Your Trip Length",
    "intro": "Select the perfect duration for your adventure",
    "options": [
      {
        "title": "3-Night Escape",
        "subtitle": "Quick winter getaway",
        "image": "/assets/media/trip-3night.jpg",
        "description": "Perfect for a weekend adventure with highlights.",
        "ideal_for": "Couples, Short breaks, First-timers"
      },
      {
        "title": "5-Night Adventure",
        "subtitle": "Our most popular",
        "image": "/assets/media/trip-5night.jpg",
        "description": "The ideal balance of activities and relaxation.",
        "ideal_for": "Families, Groups, Photography enthusiasts"
      }
    ],
    "show_availability": true,
    "availability": {
      "title": "2026 Dates",
      "subtitle": "Winter Season Availability",
      "months": [
        {
          "name": "December",
          "dates": "Dec 1-31",
          "calendar": [0,0,0,0,0,0,1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30,31]
        }
      ],
      "features": ["Flexible booking", "Free cancellation"],
      "cta": {
        "text": "Check Availability",
        "url": "/availability"
      }
    },
    "note": "All trips include airport transfers and full-board accommodation."
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
      "trip_length": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "trip-length-section.liquid" %}

<!-- New -->
{% include "modules/trip-length/trip-length.liquid" %}
```

3. Ensure data has `trip_length_section` with non-empty `options` array

4. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

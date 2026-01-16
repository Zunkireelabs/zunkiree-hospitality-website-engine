# Module Specification: hero

**Module Name:** hero
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** HIGH
**Risk Level:** HIGH
**Last Updated:** 2026-01-16

---

## 1. PURPOSE

Displays the homepage hero section with background imagery (single image or auto-rotating slider), headline, tagline, and either a search bar with dropdowns OR CTA buttons. This is a high-visibility, high-complexity module that serves as the primary visual entry point for the homepage.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.hero` | object | Hero section configuration |
| `homepage.hero.headline` | string | Main headline text (must be non-empty) |

### Conditionally Required (at least one)

| Field | Type | Description |
|-------|------|-------------|
| `homepage.hero.search_bar` | object | Search bar configuration |
| `homepage.hero.cta_primary` | object | Primary CTA button |
| `homepage.hero.cta_secondary` | object | Secondary CTA button |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.hero.tagline` | string | No tagline rendered |
| `homepage.hero.images` | array | Falls back to `background_image` |
| `homepage.hero.background_image` | string | Default placeholder styling |
| `homepage.hero.search_bar.experiences` | object | Field not rendered |
| `homepage.hero.search_bar.months` | object | Field not rendered |
| `homepage.hero.search_bar.locations` | object | Field not rendered |
| `homepage.hero.search_bar.travelers` | object | Field not rendered |
| `homepage.hero.search_bar.button` | object | Default text/url used |
| `homepage.hero.search_bar.helper` | object | Helper row not rendered |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.hero` is `true`
2. `homepage.hero.headline` exists and is non-empty
3. Either `search_bar` OR at least one CTA exists

```json
{
  "engine": {
    "modules": {
      "hero": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- assign has_search_bar = homepage.hero.search_bar -%}
{%- assign has_cta = homepage.hero.cta_primary or homepage.hero.cta_secondary -%}
{%- if site.engine.modules.hero and homepage.hero.headline and (has_search_bar or has_cta) -%}
```

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/hero/hero.liquid` | HTML structure + inline JS |
| CSS | `src/assets/css/modules/hero.css` | Scoped styles |
| JS | Inline in hero.liquid | Image slider, dropdown toggles |

**Phase 1.5 Constraint:** JavaScript remains inline. No external JS file.

---

## 5. INCLUDE PATH

```liquid
{% include "modules/hero/hero.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.hero -%}
<link rel="stylesheet" href="/assets/css/modules/hero.css">
{%- endif -%}
```

---

## 7. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No JavaScript executed
- No console errors

### If data missing:
- `homepage.hero` missing → renders nothing
- `homepage.hero.headline` missing → renders nothing
- Neither `search_bar` nor CTAs exist → renders nothing
- `homepage.hero.images` empty → falls back to `background_image`
- `homepage.hero.tagline` missing → no tagline rendered
- Individual search_bar fields missing → those fields not rendered

---

## 8. RENDERING MODES

| Mode | Condition | CSS Class | Features |
|------|-----------|-----------|----------|
| Search Bar | `search_bar` exists | `.hero` | Left-aligned content, search UI at bottom |
| Simple | No `search_bar` | `.hero.hero-simple` | Centered content, CTA buttons |

---

## 9. JAVASCRIPT FUNCTIONALITY

All JavaScript is inline (Phase 1.5 constraint).

### Image Slider
- Activates when `homepage.hero.images` has more than 1 image
- Auto-rotates every 5 seconds
- Crossfade transition (1s ease-in-out)

### Search Dropdowns
- Click to open/close dropdown
- Click outside closes all dropdowns
- Only one dropdown open at a time

---

## 10. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h1>`, `<p>`)
- SVG icons are decorative (no alt text needed)
- Form controls have associated labels
- Keyboard navigable buttons and links
- Color contrast maintained via design tokens

---

## 11. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-white` | Text, backgrounds |
| `--color-arctic` | Border, background |
| `--color-frost` | Dropdown backgrounds |
| `--color-primary` | CTA, links |
| `--color-primary-light` | CTA gradient |
| `--color-gray-*` | Various UI elements |
| `--color-text-*` | Text colors |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--font-*` | Font weights |
| `--transition-*` | Transitions |
| `--container-2xl` | Max width |

---

## 12. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `85vh` | Hero min-height | `--hero-height` |
| `90px` | Header offset | `--header-height` |
| `4rem` | Headline desktop | `--text-5xl` |
| `3rem` | Headline tablet | `--text-4xl` |
| `2.25rem` | Headline mobile | `--text-3xl` |
| `#FDF8F3` | Helper background | `--color-bg-warm` |
| `#F5F5F5` | Search field bg | `--color-bg-input` |
| `#EFEFEF` | Search field hover | `--color-bg-input-hover` |
| `#000000` | Search button | `--color-bg-dark` |
| `#333333` | Search button hover | `--color-bg-dark-hover` |
| `#25D366` | WhatsApp CTA | `--color-whatsapp` |
| `#20bd5a` | WhatsApp hover | `--color-whatsapp-hover` |
| `#1E3A5F` | Checkbox/apply | `--color-accent-dark` |
| `#2A4A73` | Apply hover | `--color-accent-dark-hover` |
| `rgba(...)` | Overlays/shadows | Various overlay tokens |

---

## 13. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1024px) | Full height hero, 4-field search bar |
| Tablet (768-1024px) | Reduced headline, 2x2 search grid, centered dropdowns |
| Mobile (<768px) | Auto height, stacked search, bottom sheet dropdowns |

---

## 14. NON-FUNCTIONAL REQUIREMENTS

- No global state mutation
- No dependency on other modules
- No assumptions about page order
- Safe to remove at any time
- Self-contained CSS and inline JS

---

## 15. EXAMPLE DATA

### Search Bar Mode
```json
{
  "hero": {
    "headline": "Discover the Magic of Lapland",
    "tagline": "Unforgettable family adventures in Finnish Lapland",
    "images": [
      "/assets/media/hero-1.jpg",
      "/assets/media/hero-2.jpg"
    ],
    "search_bar": {
      "experiences": {
        "label": "Any experience",
        "header": "Select Experiences",
        "options": [
          { "label": "Northern Lights", "icon": "✨" },
          { "label": "Husky Safari", "icon": "🐕" }
        ]
      },
      "months": {
        "label": "Any month",
        "options": ["December", "January", "February"]
      },
      "button": {
        "text": "Search",
        "url": "/search"
      }
    }
  }
}
```

### Simple Mode (CTAs)
```json
{
  "hero": {
    "headline": "Welcome to Hotel Yatri Gorkha",
    "tagline": "Experience authentic Nepali hospitality",
    "background_image": "/assets/media/hero-bg.jpg",
    "cta_primary": {
      "text": "Book Now",
      "url": "/contact"
    },
    "cta_secondary": {
      "text": "WhatsApp Us",
      "url": "https://wa.me/..."
    }
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
      "hero": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "hero-homepage.liquid" %}

<!-- New -->
{% include "modules/hero/hero.liquid" %}
```

3. Ensure data has headline AND (search_bar OR CTAs)

4. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

# Module Specification: social-proof

**Module Name:** social_proof
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** MEDIUM
**Risk Level:** MEDIUM
**Last Updated:** 2026-01-18

---

## 1. PURPOSE

Displays a social proof section with a two-column hero layout (image with frosted glass card + partner block) and a features grid. Used to build trust and credibility through partner endorsements and key benefits.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.social_proof_section` | object | Social proof section configuration |
| `homepage.social_proof_section.features` | array | Array of feature objects (must be non-empty) |
| `homepage.social_proof_section.features[].title` | string | Feature title |
| `homepage.social_proof_section.features[].description` | string | Feature description |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.social_proof_section.headline` | string | No headline rendered |
| `homepage.social_proof_section.intro` | string | No intro text rendered |
| `homepage.social_proof_section.background_image` | string | No hero image rendered |
| `homepage.social_proof_section.partner_note` | string | No partner note rendered |
| `homepage.social_proof_section.features[].icon` | string | No icon rendered |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.social_proof` is `true`
2. `homepage.social_proof_section` exists
3. `homepage.social_proof_section.features` exists
4. `homepage.social_proof_section.features.size > 0`

```json
{
  "engine": {
    "modules": {
      "social_proof": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- if site.engine.modules.social_proof
  and homepage.social_proof_section
  and homepage.social_proof_section.features
  and homepage.social_proof_section.features.size > 0
-%}
```

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/social-proof/social-proof.liquid` | HTML structure |
| CSS | `src/assets/css/modules/social-proof.css` | Scoped styles |
| JS | None | Module has no JavaScript |

---

## 5. INCLUDE PATH

```liquid
{% include "modules/social-proof/social-proof.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.social_proof -%}
<link rel="stylesheet" href="/assets/css/modules/social-proof.css">
{%- endif -%}
```

---

## 7. FAILURE BEHAVIOR

### If disabled:
- No HTML output
- No CSS loaded
- No console errors

### If data missing:
- `homepage.social_proof_section` missing → renders nothing
- `homepage.social_proof_section.features` missing → renders nothing
- `homepage.social_proof_section.features` empty array → renders nothing
- `homepage.social_proof_section.headline` missing → no headline in card
- `homepage.social_proof_section.intro` missing → no intro in card
- `homepage.social_proof_section.background_image` missing → no hero image
- `homepage.social_proof_section.partner_note` missing → no partner note

---

## 8. RENDERING MODES

| Mode | Condition | Features |
|------|-----------|----------|
| Standard | features array exists | Hero section + features grid |
| Full | All optional fields provided | Complete hero with image, card content, partner block |

---

## 9. JAVASCRIPT FUNCTIONALITY

This module has no JavaScript.

---

## 10. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h2>`, `<h4>`, `<div>`)
- Hero image has alt text from headline
- SVG icons are decorative
- Color contrast maintained via design tokens

---

## 11. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-white` | Section background, feature icon bg |
| `--color-frost` | Features grid background |
| `--color-text-secondary` | Partner note, feature descriptions |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--radius-lg` | Border radius |
| `--container-2xl` | Max container width |

---

## 12. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `1.75rem` | Card title desktop | `--text-xl` |
| `1.5rem` | Card title mobile | `--text-lg` |
| `350px` | Hero left min-height | `--hero-height-lg` |
| `300px` | Hero left tablet | `--hero-height-md` |
| `280px` | Hero left mobile | `--hero-height-sm` |
| `420px` | Content card max-width | `--card-width-lg` |
| `56px` | Partner badge size | `--badge-size-lg` |
| `48px` | Feature icon size | `--icon-size-lg` |
| `40px` | Feature icon mobile | `--icon-size-md` |
| `#FDF8F3` | Partner block bg | `--color-warm-bg` |
| `black` | Badge bg, text colors | `--color-text-primary` |
| `white` | Card title color | `--color-white` |
| `rgba(255,255,255,0.15)` | Card glass background | `--glass-bg` |
| `rgba(255,255,255,0.85)` | Card intro color | `--color-text-light-alpha` |
| `rgba(255,255,255,0.25)` | Card border | `--glass-border` |
| `rgba(0,0,0,0.2/0.1)` | Overlay gradient | `--overlay-gradient` |
| `rgba(0,0,0,0.08)` | Feature icon border | `--border-subtle` |
| `1.5fr 1fr` | Hero grid columns | Custom layout |

---

## 13. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1024px) | 2-column hero (1.5:1), 4-column features grid |
| Tablet (900-1024px) | 2-column hero, 2-column features grid |
| Small tablet (<900px) | 1-column hero stacked |
| Mobile (<768px) | 1-column features grid, reduced spacing |

---

## 14. ICON MAPPING

The features section supports the following icon values:

| Icon Value | Description |
|------------|-------------|
| `compass` | Compass/navigation icon |
| `handshake` | Handshake/partnership icon |
| `heart` | Heart/love icon |
| `shield` | Shield/security icon |
| (default) | No icon rendered |

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
  "social_proof_section": {
    "headline": "Why Families Trust Us",
    "intro": "We've helped thousands of families create magical winter memories in Lapland.",
    "background_image": "/assets/media/social-proof/family-aurora.jpg",
    "partner_note": "Officially endorsed by Visit Finland as a trusted family travel specialist.",
    "features": [
      {
        "icon": "compass",
        "title": "Expert Local Knowledge",
        "description": "Our team has lived in Lapland for over 20 years."
      },
      {
        "icon": "handshake",
        "title": "Trusted Partners",
        "description": "We work only with vetted, family-friendly providers."
      },
      {
        "icon": "heart",
        "title": "Family-First Approach",
        "description": "Every experience is designed with children in mind."
      },
      {
        "icon": "shield",
        "title": "Safe & Secure",
        "description": "Full ATOL protection and 24/7 support during your trip."
      }
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
      "social_proof": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "social-proof-section.liquid" %}

<!-- New -->
{% include "modules/social-proof/social-proof.liquid" %}
```

3. Ensure data has `social_proof_section` with non-empty `features` array

4. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

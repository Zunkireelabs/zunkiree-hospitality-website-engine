# Module Specification: experiences

**Module Name:** experiences
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** MEDIUM
**Risk Level:** MEDIUM
**Last Updated:** 2026-01-16

---

## 1. PURPOSE

Displays a horizontal carousel of experience/service cards with images, titles, and descriptions. Each card features hover effects including image blur, overlay, and description reveal. Carousel navigation via arrow buttons on desktop, swipe/scroll on mobile.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.experiences_section` | object | Experiences section configuration |
| `homepage.experiences_section.experiences` | array | Array of experience objects (must be non-empty) |
| `homepage.experiences_section.experiences[].title` | string | Experience card title |
| `homepage.experiences_section.experiences[].image` | string | Experience card image URL |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.experiences_section.headline` | string | No headline rendered |
| `homepage.experiences_section.intro` | string | No intro paragraph rendered |
| `homepage.experiences_section.experiences[].description` | string | No description rendered on hover |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.experiences` is `true`
2. `homepage.experiences_section` exists
3. `homepage.experiences_section.experiences` exists
4. `homepage.experiences_section.experiences.size > 0`

```json
{
  "engine": {
    "modules": {
      "experiences": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- if site.engine.modules.experiences
  and homepage.experiences_section
  and homepage.experiences_section.experiences
  and homepage.experiences_section.experiences.size > 0
-%}
```

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/experiences/experiences.liquid` | HTML structure + inline JS |
| CSS | `src/assets/css/modules/experiences.css` | Scoped styles |
| JS | Inline in experiences.liquid | Carousel navigation |

**Phase 1.5 Constraint:** JavaScript remains inline. No external JS file.

---

## 5. INCLUDE PATH

```liquid
{% include "modules/experiences/experiences.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.experiences -%}
<link rel="stylesheet" href="/assets/css/modules/experiences.css">
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
- `homepage.experiences_section` missing → renders nothing
- `homepage.experiences_section.experiences` missing → renders nothing
- `homepage.experiences_section.experiences` empty array → renders nothing
- `homepage.experiences_section.headline` missing → no headline rendered
- `homepage.experiences_section.intro` missing → no intro rendered
- Individual experience description missing → no description on hover

---

## 8. RENDERING MODES

| Mode | Condition | Features |
|------|-----------|----------|
| Standard | experiences array exists | Horizontal carousel with cards |

---

## 9. JAVASCRIPT FUNCTIONALITY

All JavaScript is inline (Phase 1.5 constraint).

### Carousel Navigation
- Click prev/next buttons to scroll carousel by 300px
- Arrow visibility updates based on scroll position
- Prev arrow disabled/dimmed at start of carousel
- Next arrow disabled/dimmed at end of carousel
- Event listeners: click, scroll, resize

### Image Handling
- Local images (starting with `/`): Uses `<picture>` element with AVIF/WebP srcset
- External images: Uses `background-image` on div

---

## 10. ACCESSIBILITY

- Semantic HTML (`<section>`, `<h2>`, `<h3>`, `<p>`)
- Arrow buttons have `aria-label` attributes
- Images have alt text from experience title
- Keyboard navigable via tab to buttons
- SVG icons are decorative

---

## 11. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-white` | Section/card backgrounds |
| `--color-text-primary` | Arrow icon color |
| `--color-text-secondary` | Intro text |
| `--color-primary` | Arrow hover background |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--transition-normal` | Transitions |
| `--container-2xl` | Max container width |

---

## 12. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `2.5rem` | Desktop headline | `--text-3xl` |
| `2rem` | Mobile headline | `--text-2xl` |
| `1.75rem` | Card title desktop | `--text-xl` |
| `1.5rem` | Card title mobile | `--text-lg` |
| `280px` | Card width desktop | `--card-width-lg` |
| `260px` | Card width tablet | `--card-width-md` |
| `240px` | Card width mobile | `--card-width-sm` |
| `380px` | Card height desktop | `--card-height-lg` |
| `360px` | Card height tablet | `--card-height-md` |
| `320px` | Card height mobile | `--card-height-sm` |
| `48px` | Arrow size desktop | `--arrow-size-lg` |
| `40px` | Arrow size tablet | `--arrow-size-md` |
| `16px` | Card border radius | `--radius-lg` |
| `black` | Title color | `--color-text-primary` |
| `rgba(0,0,0,0.35)` | Hover overlay | `--overlay-dark` |
| `rgba(0,0,0,0.15)` | Arrow shadow | `--shadow-arrow` |
| `rgba(0,0,0,0.4)` | Title text shadow | `--shadow-text-strong` |
| `rgba(0,0,0,0.3)` | Desc text shadow | `--shadow-text-light` |
| `rgba(255,255,255,0.95)` | Desc text color | `--color-text-light-alpha` |
| `-24px`, `-12px` | Arrow positioning | `--arrow-offset` |
| `300` | JS scroll amount | Config value |

---

## 13. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>1024px) | 280x380px cards, 48px arrows at -24px offset |
| Tablet (768-1024px) | 260x360px cards, 40px arrows at -12px offset |
| Mobile (<768px) | 240x320px cards, arrows hidden, swipe to scroll |

---

## 14. NON-FUNCTIONAL REQUIREMENTS

- No global state mutation
- No dependency on other modules
- No assumptions about page order
- Safe to remove at any time
- Self-contained CSS and inline JS

---

## 15. EXAMPLE DATA

```json
{
  "experiences_section": {
    "headline": "Unforgettable Experiences",
    "intro": "Discover the magic of Lapland through our curated activities",
    "experiences": [
      {
        "title": "Northern Lights",
        "image": "/assets/media/experiences/northern-lights.jpg",
        "description": "Chase the aurora borealis across the Arctic sky"
      },
      {
        "title": "Husky Safari",
        "image": "/assets/media/experiences/husky-safari.jpg",
        "description": "Glide through snowy forests with our friendly huskies"
      },
      {
        "title": "Reindeer Farm",
        "image": "https://example.com/reindeer.jpg",
        "description": "Meet Santa's helpers and learn about Sami culture"
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
      "experiences": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "experiences-section.liquid" %}

<!-- New -->
{% include "modules/experiences/experiences.liquid" %}
```

3. Ensure data has `experiences_section` with non-empty `experiences` array

4. Pull updated template submodule

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

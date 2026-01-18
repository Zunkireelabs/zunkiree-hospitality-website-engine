# Zunkire Hospitality Website Engine — MODULE INVENTORY & CLASSIFICATION

**Date:** 2026-01-16
**Engine Version:** 1.0.0
**Status:** Phase 1.5 — Governance Locked

---

## 1. CURRENT FILE INVENTORY

### Components (`src/_includes/`)

| # | File | Lines | Current Location |
|---|------|-------|------------------|
| 1 | `layouts/base.liquid` | ~80 | `src/_includes/layouts/` |
| 2 | `header.liquid` | 797 | `src/_includes/` |
| 3 | `footer.liquid` | 325 | `src/_includes/` |
| 4 | `hero.liquid` | 185 | `src/_includes/modules/hero/` |
| 5 | `trust-strip.liquid` | 38 | `src/_includes/modules/trust-strip/` |
| 6 | `experiences.liquid` | 97 | `src/_includes/modules/experiences/` |
| 7 | `why.liquid` | 48 | `src/_includes/modules/why/` |
| 8 | `why-lapland-section.liquid` | 151 | `src/_includes/` |
| 9 | `location.liquid` | 103 | `src/_includes/modules/location/` |
| 10 | `trip-length.liquid` | 119 | `src/_includes/modules/trip-length/` |
| 11 | `inclusions.liquid` | 52 | `src/_includes/modules/inclusions/` |
| 12 | `social-proof-section.liquid` | 300 | `src/_includes/` |
| 13 | `faq-section.liquid` | 339 | `src/_includes/` |
| 14 | `inquiry-form-section.liquid` | 836 | `src/_includes/` |
| 15 | `availability-section.liquid` | 226 | `src/_includes/` |
| 16 | `conversion-section.liquid` | 288 | `src/_includes/` |
| 17 | `video-showcase-section.liquid` | 172 | `src/_includes/` |
| 18 | `media-video.liquid` | 43 | `src/_includes/` |
| 19 | `hls-init.liquid` | 69 | `src/_includes/` |

### CSS (`src/assets/css/`)

| # | File | Lines | Scope |
|---|------|-------|-------|
| 1 | `global.css` | 646 | Engine Core |
| 2 | `layout.css` | 429 | Engine Core |
| 3 | `typography.css` | 287 | Engine Core |
| 4 | `components.css` | 467 | Mixed (needs audit) |
| 5 | `utilities.css` | 367 | Engine Core |

---

## 2. CLASSIFICATION TABLE

### CORE (Always Present, Never Optional)

| Component | File | Purpose | Spec Compliant |
|-----------|------|---------|----------------|
| Base Layout | `layouts/base.liquid` | HTML document wrapper | ✅ Yes |
| Header | `header.liquid` | Navigation, logo, CTAs | ✅ Yes |
| Footer | `footer.liquid` | Footer links, contact, copyright | ✅ Yes |

**Core CSS:**
- `global.css` — Variables, reset, animations
- `layout.css` — Header, footer, grids
- `typography.css` — Text styles
- `utilities.css` — Helper classes

---

### MODULES (Optional, Explicitly Enabled)

| Module Name | Current File | Purpose | Activation Key | Spec Compliant |
|-------------|--------------|---------|----------------|----------------|
| `hero` | `modules/hero/hero.liquid` | Homepage hero with slider | `engine.modules.hero` | ✅ Yes |
| `trust_strip` | `modules/trust-strip/trust-strip.liquid` | Trust badges row | `engine.modules.trust_strip` | ✅ Yes |
| `experiences` | `modules/experiences/experiences.liquid` | Services/amenities carousel | `engine.modules.experiences` | ✅ Yes |
| `why` | `modules/why/why.liquid` | Benefits/features highlight | `engine.modules.why` | ✅ Yes |
| `why-lapland` | `why-lapland-section.liquid` | Destination selling points | `engine.modules.why-lapland` | ❌ No |
| `location` | `modules/location/location.liquid` | Location info + bento grid | `engine.modules.location` | ✅ Yes |
| `trip_length` | `modules/trip-length/trip-length.liquid` | Duration/package selector | `engine.modules.trip_length` | ✅ Yes |
| `inclusions` | `modules/inclusions/inclusions.liquid` | What's included checklist | `engine.modules.inclusions` | ✅ Yes |
| `social-proof` | `social-proof-section.liquid` | Testimonials | `engine.modules.social-proof` | ❌ No |
| `faq` | `faq-section.liquid` | FAQ accordion | `engine.modules.faq` | ❌ No |
| `inquiry` | `inquiry-form-section.liquid` | Multi-step contact form | `engine.modules.inquiry` | ❌ No |
| `availability` | `availability-section.liquid` | Availability checker | `engine.modules.availability` | ❌ No |
| `conversion` | `conversion-section.liquid` | CTA blocks | `engine.modules.conversion` | ❌ No |
| `video` | `video-showcase-section.liquid` | Video player section | `engine.modules.video` | ❌ No |

**Helper/Utility Components (Sub-modules):**

| Component | Current File | Used By | Classification |
|-----------|--------------|---------|----------------|
| `media-video` | `media-video.liquid` | `video` module | Sub-component of `video` |
| `hls-init` | `hls-init.liquid` | `video` module | Sub-component of `video` |

---

## 3. COMPLIANCE GAP ANALYSIS

### Current State vs. Required Spec

| Requirement | Current State | Gap |
|-------------|---------------|-----|
| **File Structure** | All in `src/_includes/` | ❌ Should be `modules/<name>/` |
| **Module CSS** | Mixed in `components.css` | ❌ Each module should own its CSS |
| **Module JS** | Inline in Liquid files | ⚠️ Should be `<module>.js` if needed |
| **Activation Check** | Uses `homepage.<section>.enabled` | ❌ Should use `site.engine.modules.<name>` |
| **Data Contract** | Implicit in code | ❌ Not documented per spec |
| **Isolation** | CSS potentially leaks | ⚠️ Needs audit |

---

## 4. REQUIRED DIRECTORY STRUCTURE (Target State)

```
zunkire-hospitality-website-engine/
├── src/
│   ├── _includes/
│   │   ├── layouts/
│   │   │   └── base.liquid          ← CORE
│   │   ├── header.liquid            ← CORE
│   │   └── footer.liquid            ← CORE
│   │
│   └── assets/css/
│       ├── global.css               ← CORE
│       ├── layout.css               ← CORE
│       ├── typography.css           ← CORE
│       └── utilities.css            ← CORE
│
├── modules/
│   ├── hero/
│   │   ├── hero.liquid
│   │   ├── hero.css
│   │   └── hero.js (if needed)
│   │
│   ├── trust-strip/
│   │   ├── trust-strip.liquid
│   │   └── trust-strip.css
│   │
│   ├── experiences/
│   │   ├── experiences.liquid
│   │   └── experiences.css
│   │
│   ├── faq/
│   │   ├── faq.liquid
│   │   ├── faq.css
│   │   └── faq.js
│   │
│   ├── inquiry/
│   │   ├── inquiry.liquid
│   │   ├── inquiry.css
│   │   └── inquiry.js
│   │
│   ├── video/
│   │   ├── video.liquid
│   │   ├── video.css
│   │   ├── video.js
│   │   ├── _media-video.liquid      ← sub-component
│   │   └── _hls-init.liquid         ← sub-component
│   │
│   └── [... 8 more modules ...]
│
└── docs/
    ├── ENGINE_RULES.md
    ├── MODULE_SPEC_TEMPLATE.md
    └── modules/
        ├── hero.spec.md
        ├── faq.spec.md
        └── [... per-module specs ...]
```

---

## 5. MODULE COUNT SUMMARY

| Classification | Count |
|----------------|-------|
| **CORE** | 3 components + 4 CSS files |
| **MODULES** | 14 |
| **Sub-components** | 2 (belong to `video` module) |
| **Total Components** | 19 |

---

## 6. SPEC COMPLIANCE SUMMARY

| Status | Count | Percentage |
|--------|-------|------------|
| ✅ Compliant | 10 (Core + hero + trust_strip + inclusions + why + trip_length + experiences + location) | 53% |
| ❌ Non-compliant | 9 (Remaining modules) | 47% |

---

## 7. RECOMMENDED MIGRATION ORDER

Based on complexity and dependencies:

| Priority | Module | Complexity | Notes |
|----------|--------|------------|-------|
| 1 | `trust_strip` | Low | ✅ MIGRATED |
| 2 | `why` | Low | ✅ MIGRATED |
| 3 | `inclusions` | Low | ✅ MIGRATED |
| 4 | `conversion` | Low | CTA block |
| 5 | `experiences` | Medium | ✅ MIGRATED |
| 6 | `location` | Medium | ✅ MIGRATED |
| 7 | `social-proof` | Medium | Carousel/grid |
| 8 | `hero` | HIGH | ✅ MIGRATED |
| 9 | `trip_length` | Medium | ✅ MIGRATED |
| 10 | `faq` | Medium | Accordion JS |
| 11 | `video` | High | HLS, sub-components |
| 12 | `availability` | High | Interactive |
| 13 | `inquiry` | High | Multi-step form, validation |
| 14 | `why-lapland` | Decision | May merge with `why` or deprecate |

---

## 8. NEXT STEPS

1. **Decision Required:** Confirm target directory structure
2. **Decision Required:** Confirm `why-lapland` fate (merge/deprecate/keep)
3. **Create:** Individual module spec files in `docs/modules/`
4. **Migrate:** Modules one-by-one following priority order
5. **Audit:** Extract module CSS from `components.css`
6. **Update:** Projects to use new activation contract

---

**Document Status:** Inventory Complete
**Code Changes:** None (governance phase)

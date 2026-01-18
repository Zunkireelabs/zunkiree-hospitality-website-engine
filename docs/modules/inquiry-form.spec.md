# Module Specification: inquiry-form

**Module Name:** inquiry_form
**Status:** Optional
**Type:** Isolated Module
**Version:** 1.0.0
**Complexity:** HIGH
**Risk Level:** MEDIUM
**Last Updated:** 2026-01-18

---

## 1. PURPOSE

Multi-step inquiry form with progress tracking, traveler counters, contact fields, and success state. Used to capture lead information through an interactive, guided flow.

---

## 2. DATA CONTRACT

### Source
`homepage.json` (in project repo)

### Required Fields

| Field | Type | Description |
|-------|------|-------------|
| `homepage.inquiry_section` | object | Inquiry form configuration |
| `homepage.inquiry_section.steps` | array | Array of form steps (must be non-empty) |
| `homepage.inquiry_section.steps[].id` | string | Step identifier (`trip_length`, `month`, `contact`) |
| `homepage.inquiry_section.steps[].question` | string | Step question text |

### Optional Fields

| Field | Type | Default Behavior |
|-------|------|------------------|
| `homepage.inquiry_section.headline` | string | No headline rendered |
| `homepage.inquiry_section.intro` | string | No intro rendered |
| `homepage.inquiry_section.form_action` | string | Form posts to current page |
| `homepage.inquiry_section.buttons` | object | No button text |
| `homepage.inquiry_section.buttons.back` | string | No back button text |
| `homepage.inquiry_section.buttons.next` | string | No next button text |
| `homepage.inquiry_section.buttons.submit` | string | No submit button text |
| `homepage.inquiry_section.success` | object | No success state |
| `homepage.inquiry_section.trust_badges` | array | No trust badges rendered |
| `homepage.inquiry_section.steps[].options` | array | No options for radio steps |
| `homepage.inquiry_section.steps[].travelers` | array | No traveler counters |
| `homepage.inquiry_section.steps[].fields` | array | No contact fields |

---

## 3. ACTIVATION RULE

Module renders ONLY IF all conditions are met:

1. `site.engine.modules.inquiry_form` is `true`
2. `homepage.inquiry_section` exists
3. `homepage.inquiry_section.steps` exists
4. `homepage.inquiry_section.steps.size > 0`

```json
{
  "engine": {
    "modules": {
      "inquiry_form": true
    }
  }
}
```

**Location:** `site.json` in project repo

**Activation Guard Logic:**
```liquid
{%- if site.engine.modules.inquiry_form
  and homepage.inquiry_section
  and homepage.inquiry_section.steps
  and homepage.inquiry_section.steps.size > 0
-%}
```

---

## 4. FILE OWNERSHIP

| File | Path | Purpose |
|------|------|---------|
| Template | `src/_includes/modules/inquiry-form/inquiry-form.liquid` | HTML structure + inline JS |
| CSS | `src/assets/css/modules/inquiry-form.css` | Scoped styles |
| JS | Inline in template | Form step navigation, counters, validation |
| Legacy | `src/_includes/inquiry-form-section.liquid` | DEPRECATED - rollback only |

---

## 5. INCLUDE PATH

```liquid
{% include "modules/inquiry-form/inquiry-form.liquid" %}
```

---

## 6. CSS LOADING

CSS is loaded conditionally in `layouts/base.liquid`:

```liquid
{%- if site.engine.modules.inquiry_form -%}
<link rel="stylesheet" href="/assets/css/modules/inquiry-form.css">
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
- `homepage.inquiry_section` missing → renders nothing
- `homepage.inquiry_section.steps` missing → renders nothing
- `homepage.inquiry_section.steps` empty → renders nothing
- Individual step missing → that step skipped
- `homepage.inquiry_section.trust_badges` missing → no badges rendered

---

## 8. RENDERING MODES

| Mode | Condition | Features |
|------|-----------|----------|
| Minimal | Steps array only | Form with steps, default UI |
| Standard | Steps + buttons + success | Full form flow with success state |
| Full | All fields provided | Complete form with badges and customization |

---

## 9. JAVASCRIPT FUNCTIONALITY

JavaScript is inline in the template (Phase 1.5 requirement).

### Features:
- **Step Navigation:** Back/Next/Submit button handling
- **Progress Tracking:** Updates progress bar percentage and step indicator
- **Traveler Counters:** Increment/decrement with min/max validation
- **Form Submission:** Prevents default, shows success state
- **Button State Management:** Enables/disables based on counter limits

### Event Listeners:
- `btnNext.click` → Advances to next step
- `btnBack.click` → Returns to previous step
- `counterBtn.click` → Adjusts traveler counts
- `form.submit` → Handles form submission

---

## 10. ACCESSIBILITY

- Semantic HTML (`<section>`, `<form>`, `<label>`, `<input>`)
- Form inputs have associated labels
- Radio buttons properly grouped by name
- Progress indicators provide status
- Buttons have visible focus states
- SVG icons are decorative

---

## 11. CSS VARIABLES USED

All from `global.css`:

| Variable | Usage |
|----------|-------|
| `--color-white` | Card background, counter background |
| `--color-text-secondary` | Intro text, descriptions |
| `--space-*` | Spacing throughout |
| `--text-*` | Font sizes |
| `--radius-xl`, `--radius-lg`, `--radius-md`, `--radius-full` | Border radius |

---

## 12. TECHNICAL DEBT (Phase 1.5)

The following hardcoded values are accepted technical debt and will be migrated to design tokens in Phase 2:

| Value | Usage | Future Token |
|-------|-------|--------------|
| `2.5rem` | Headline desktop | `--text-3xl` |
| `2rem` | Headline mobile | `--text-2xl` |
| `1.75rem` | Success headline | `--text-xl` |
| `1.25rem` | Step question | `--text-lg` |
| `750px` | Container max-width | `--container-sm` |
| `120px` | Option card min-height | `--card-height-sm` |
| `80px` | Success icon size | `--icon-size-xl` |
| `36px` | Counter button size | `--button-size-sm` |
| `20px` | Radio button size | `--radio-size` |
| `6px` | Progress bar height | `--progress-height` |
| `#FDF8F3` | Section background | `--color-warm-bg` |
| `#E5E7EB` | Border color | `--color-gray-200` |
| `#10B981` | Success/progress color | `--color-success` |
| `#34D399` | Progress gradient end | `--color-success-light` |
| `#ECFDF5` | Success icon bg | `--color-success-bg` |
| `black` | Primary buttons | `--color-text-primary` |
| `#1a1a1a` | Button hover | `--color-gray-900` |

---

## 13. RESPONSIVE BREAKPOINTS

| Breakpoint | Behavior |
|------------|----------|
| Desktop (>640px) | 3-column option cards, 2-column contact fields |
| Mobile (<640px) | 1-column cards, stacked nav, full-width buttons |

---

## 14. STEP TYPES

The form supports three step types identified by `step.id`:

| Step ID | Type | Data Required |
|---------|------|---------------|
| `trip_length` | Radio selection | `options[]` with `value`, `label`, `description` |
| `month` | Radio selection | `options[]` with `value`, `label`, `description` |
| `contact` | Form fields + counters | `travelers[]`, `fields[]` |

---

## 15. ICON MAPPING

Trust badges support the following icon values:

| Icon Value | Description |
|------------|-------------|
| `check` | Checkmark icon |
| `clock` | Clock/time icon |
| `shield` | Shield/security icon |

---

## 16. NON-FUNCTIONAL REQUIREMENTS

- No external JavaScript dependencies
- No global state mutation (uses IIFE)
- No dependency on other modules
- No assumptions about page order
- Safe to remove at any time
- Self-contained CSS and inline JS

---

## 17. EXAMPLE DATA

```json
{
  "inquiry_section": {
    "headline": "Get Your Free Quote",
    "intro": "Tell us about your dream trip and we'll create a personalized itinerary.",
    "form_action": "/api/inquiry",
    "steps": [
      {
        "id": "trip_length",
        "question": "How long would you like to stay?",
        "options": [
          { "value": "3-4", "label": "3-4 nights", "description": "Perfect for a long weekend" },
          { "value": "5-6", "label": "5-6 nights", "description": "Most popular choice" },
          { "value": "7+", "label": "7+ nights", "description": "Extended adventure" }
        ]
      },
      {
        "id": "month",
        "question": "When would you like to travel?",
        "options": [
          { "value": "nov-dec", "label": "Nov-Dec", "description": "Early season" },
          { "value": "jan-feb", "label": "Jan-Feb", "description": "Peak aurora" },
          { "value": "mar-apr", "label": "Mar-Apr", "description": "Late season" }
        ]
      },
      {
        "id": "contact",
        "question": "Who's traveling?",
        "travelers": [
          { "type": "adults", "label": "Adults", "description": "Ages 18+", "min": 1, "max": 10, "default": 2 },
          { "type": "children", "label": "Children", "description": "Ages 2-17", "min": 0, "max": 8, "default": 0 }
        ],
        "fields": [
          { "name": "name", "label": "Your Name", "type": "text", "placeholder": "John Smith", "required": true },
          { "name": "email", "label": "Email", "type": "email", "placeholder": "john@example.com", "required": true }
        ]
      }
    ],
    "buttons": {
      "back": "Back",
      "next": "Continue",
      "submit": "Get My Quote"
    },
    "success": {
      "headline": "Thank You!",
      "message": "We've received your inquiry and will be in touch within 24 hours.",
      "sub_message": "Check your email for a confirmation.",
      "cta": { "text": "Browse Experiences", "url": "#experiences" }
    },
    "trust_badges": [
      { "icon": "check", "text": "No obligation" },
      { "icon": "clock", "text": "Response within 24h" },
      { "icon": "shield", "text": "ATOL protected" }
    ]
  }
}
```

---

## 18. MIGRATION NOTES

### For Projects Using This Module

1. Add to `site.json`:
```json
{
  "engine": {
    "modules": {
      "inquiry_form": true
    }
  }
}
```

2. Update include path in page files:
```liquid
<!-- Old -->
{% include "inquiry-form-section.liquid" %}

<!-- New -->
{% include "modules/inquiry-form/inquiry-form.liquid" %}
```

3. Ensure data has `inquiry_section` with non-empty `steps` array

4. Pull updated template submodule

### Rollback

If issues occur, revert to legacy file:
```liquid
{% include "inquiry-form-section.liquid" %}
```

The legacy file is preserved at `src/_includes/inquiry-form-section.liquid` but marked as deprecated.

---

**Spec Status:** Complete
**Compliant with:** MODULE_SPEC_TEMPLATE.md v1.0.0

# CLAUDE.md - AI Agent Instructions

> **CRITICAL: READ THIS ENTIRE FILE BEFORE MAKING ANY CHANGES**

This is the **Zunkire Hospitality Website Engine** - a shared template repository used by multiple projects. Changes here affect ALL projects using this template.

---

## STOP! Understand the Architecture First

```
┌─────────────────────────────────────────────────────────────────┐
│                    REPOSITORY STRUCTURE                          │
│                                                                  │
│  This is a SHARED TEMPLATE REPO - NOT a standalone website!     │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│  THIS REPO: zunkire-hospitality-website-engine                  │
│  Type: TEMPLATE (shared code)                                   │
│  Contains: Layouts, components, CSS                             │
│  Used by: Multiple project repos via git submodule              │
└─────────────────────────────────────────────────────────────────┘
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         ▼                    ▼                    ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│ PROJECT: HYG    │  │ PROJECT: Lapland│  │ PROJECT: Future │
│ (Hotel Yatri)   │  │ (Lapland Fam.)  │  │ (New projects)  │
│                 │  │                 │  │                 │
│ Has: Content    │  │ Has: Content    │  │ Has: Content    │
│ - site.json     │  │ - site.json     │  │ - site.json     │
│ - images        │  │ - images        │  │ - images        │
│ - docker config │  │ - docker config │  │ - docker config │
│                 │  │                 │  │                 │
│ Uses: template/ │  │ Uses: template/ │  │ Uses: template/ │
│ (submodule →    │  │ (submodule →    │  │ (submodule →    │
│  this repo)     │  │  this repo)     │  │  this repo)     │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

---

## CRITICAL RULES FOR AI AGENTS

### Rule 1: NEVER Hardcode Project-Specific Content

```liquid
<!-- ❌ WRONG - Hardcoded content -->
<h1>Hotel Yatri Gorkha</h1>
<p>Welcome to our hotel in Gorkha, Nepal</p>

<!-- ✅ CORRECT - Use variables from data files -->
<h1>{{ site.name }}</h1>
<p>{{ homepage.hero.tagline }}</p>
```

### Rule 2: NEVER Delete Components Without Checking All Projects

Before removing ANY file, verify it's not used by:
- HYG project
- Lapland project
- Any other project using this template

### Rule 3: ALWAYS Use CSS Variables for Theming

```css
/* ❌ WRONG - Hardcoded colors */
.header {
  background: #1A1A1C;
  color: #D4AF37;
}

/* ✅ CORRECT - Use CSS variables */
.header {
  background: var(--color-bg-dark);
  color: var(--color-primary);
}
```

### Rule 4: ALWAYS Test Changes Affect All Projects Correctly

Template changes must work for:
- Hotels (HYG)
- Travel/Tourism (Lapland)
- Future hospitality projects

### Rule 5: Components Must Be Data-Driven

```liquid
<!-- ❌ WRONG - Static content in template -->
<nav>
  <a href="/rooms">Rooms</a>
  <a href="/dining">Dining</a>
</nav>

<!-- ✅ CORRECT - Loop through data -->
<nav>
  {% for item in site.nav %}
    <a href="{{ item.url }}">{{ item.text }}</a>
  {% endfor %}
</nav>
```

---

## What's In This Repository

```
zunkire-hospitality-website-engine/
├── src/
│   ├── _includes/                 ← LIQUID COMPONENTS
│   │   ├── layouts/
│   │   │   └── base.liquid        ← HTML document wrapper
│   │   ├── header.liquid          ← Site header + navigation
│   │   ├── footer.liquid          ← Site footer
│   │   ├── hero-homepage.liquid   ← Homepage hero section
│   │   ├── faq-section.liquid     ← FAQ accordion
│   │   ├── experiences-section.liquid
│   │   ├── location-section.liquid
│   │   ├── social-proof-section.liquid
│   │   └── [other sections].liquid
│   │
│   └── assets/css/                ← STYLESHEETS
│       ├── global.css             ← CSS variables, resets, animations
│       ├── layout.css             ← Header, footer, navigation
│       ├── typography.css         ← Text styles
│       ├── components.css         ← UI components
│       └── utilities.css          ← Helper classes
│
├── docs/
│   └── DEVELOPMENT-WORKFLOW.md    ← Full development guide
│
├── CLAUDE.md                      ← THIS FILE (AI instructions)
├── WORKFLOW.md                    ← Quick reference for humans
├── README.md                      ← Repository overview
└── package.json                   ← Package metadata
```

---

## How Projects Use This Template

Projects include this repo as a **git submodule** and create **symlinks**:

```
project-repo/
├── template/                      ← Git submodule (this repo)
├── src/
│   ├── _data/                     ← Project-specific content
│   │   ├── site.json              ← Site name, nav, contact
│   │   ├── homepage.json          ← Homepage content
│   │   └── media.json             ← Video URLs
│   │
│   ├── _includes → ../template/src/_includes    (SYMLINK)
│   ├── assets/
│   │   ├── css → ../../template/src/assets/css  (SYMLINK)
│   │   └── media/                 ← Project images (NOT shared)
│   │
│   └── index.liquid               ← Uses template components
└── docker-compose.yml             ← Project deployment
```

---

## Making Changes

### If You're Working on THIS REPO (Template)

```bash
# You're editing shared components/CSS
# Changes will affect ALL projects

# 1. Make your changes
vim src/_includes/header.liquid

# 2. Commit to template repo
git add .
git commit -m "Update header design"
git push

# 3. Projects need to sync
# (This happens in each project repo separately)
```

### If You're Working on a PROJECT REPO

```bash
# For CONTENT changes (text, images) - edit project files directly:
vim src/_data/site.json
vim src/_data/homepage.json
# Add images to src/assets/media/

# For TEMPLATE changes (design, components) - edit via submodule:
cd template/
vim src/_includes/header.liquid
git add . && git commit -m "Update header" && git push
cd ..
git add template
git commit -m "Sync template changes"
```

---

## CSS Variables Reference

All colors, spacing, and styling should use these variables from `global.css`:

```css
/* Colors */
--color-primary: #D4AF37;           /* Gold accent */
--color-primary-light: #F4C542;
--color-bg-dark: #1A1A1C;           /* Dark background */
--color-bg-medium: #242426;
--color-bg-darker: #0A0A0C;
--color-text-light: #FFFFFF;
--color-text-medium: #E0E0E0;
--color-text-muted: #B0B0B0;

/* Spacing */
--spacing-xs: 0.5rem;
--spacing-sm: 1rem;
--spacing-md: 2rem;
--spacing-lg: 3rem;
--spacing-xl: 4rem;

/* Border Radius */
--radius-sm: 8px;
--radius-md: 15px;
--radius-lg: 20px;
--radius-full: 50px;

/* Transitions */
--transition-fast: 0.2s ease;
--transition-normal: 0.3s ease;
--transition-slow: 0.6s ease;
```

---

## Liquid Template Syntax Quick Reference

```liquid
<!-- Output variables -->
{{ site.name }}
{{ homepage.hero.title }}

<!-- Include components -->
{% include "header.liquid" %}
{% include "footer.liquid" %}

<!-- Conditionals -->
{% if condition %}
  <p>Show this</p>
{% endif %}

<!-- Loops -->
{% for item in site.nav %}
  <a href="{{ item.url }}">{{ item.text }}</a>
{% endfor %}

<!-- Filters -->
{{ title | escape }}
{{ description | truncate: 160 }}
```

---

## Data Structure Expected by Templates

Templates expect these data structures from project `src/_data/` files:

### site.json (Required)
```json
{
  "name": "Project Name",
  "description": "Site description",
  "email": "contact@example.com",
  "logo": "/assets/media/logo.png",
  "nav": [
    { "text": "Home", "url": "/" },
    { "text": "About", "url": "/about" }
  ]
}
```

### homepage.json (Required for homepage)
```json
{
  "meta": { "title": "...", "description": "..." },
  "hero": {
    "headline": "...",
    "tagline": "...",
    "cta_primary": { "text": "...", "url": "..." }
  },
  "sections": { ... }
}
```

---

## DO's and DON'Ts Summary

### ✅ DO
- Use CSS variables for all colors/spacing
- Use Liquid variables for all text content
- Test changes work for hotels AND travel sites
- Keep components generic and reusable
- Document any new components you create

### ❌ DON'T
- Hardcode project-specific text in templates
- Delete files without checking all projects
- Use inline styles with hardcoded values
- Make changes that only work for one project
- Modify template structure without updating docs

---

## Projects Using This Template

| Project | Type | Location | Status |
|---------|------|----------|--------|
| HYG | Hotel | `../hyg-web-dev/` | Active |
| Lapland Famille | Travel | `../laplandfamille/` | Active |

**Always consider impact on ALL projects before making template changes.**

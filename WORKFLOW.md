# Zunkire Hospitality Website Engine - Quick Reference

## This is a SHARED TEMPLATE

Changes here affect **ALL projects** using this template:
- HYG (Hotel Yatri Gorkha)
- Lapland Famille
- (Future projects)

---

## What's in This Repo

```
src/
├── _includes/           ← Liquid components
│   ├── layouts/
│   │   └── base.liquid  ← HTML wrapper (head, body structure)
│   ├── header.liquid    ← Site header & navigation
│   ├── footer.liquid    ← Site footer
│   └── [sections].liquid ← Page sections (hero, faq, etc.)
│
└── assets/css/          ← Stylesheets
    ├── global.css       ← CSS variables, resets
    ├── layout.css       ← Header, footer, nav styles
    ├── typography.css   ← Text styles
    ├── components.css   ← UI components
    └── utilities.css    ← Helper classes
```

---

## Making Changes

```bash
# 1. Edit files in src/

# 2. Commit
git add .
git commit -m "Describe your change"
git push

# 3. Update in each project that uses this template:
cd /path/to/project
git submodule update --remote template
git add template
git commit -m "Sync template updates"
```

---

## Guidelines

✅ **DO:**
- Use CSS variables for colors/spacing
- Keep components data-driven (use `{{ variable }}`)
- Test changes in at least one project before pushing
- Write clear commit messages

❌ **DON'T:**
- Hardcode project-specific content
- Remove existing components without checking all projects
- Push untested changes

---

## CSS Variables (in global.css)

```css
--color-primary: #D4AF37;
--color-bg-dark: #1A1A1C;
--spacing-sm: 1rem;
--spacing-md: 2rem;
/* etc. */
```

Projects can override these in their own stylesheets if needed.

---

## Projects Using This Template

| Project | Location | Dev URL |
|---------|----------|---------|
| HYG | `../hyg-web-dev/` | hyg.zunkireelabs.com |
| Lapland | `../laplandfamille/` | dev.laplandfamille.co.uk |

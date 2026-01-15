# Zunkire Hospitality Website Engine

A shared Eleventy template for building hospitality websites (hotels, travel, tourism).

> **For AI Agents:** Read [CLAUDE.md](./CLAUDE.md) before making any changes.

---

## Repository Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│              GIT REPOSITORY STRUCTURE                            │
│                                                                  │
│         One Template Repo + One Repo Per Project                 │
└─────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────────┐
│                                                                  │
│   zunkire-hospitality-website-engine    ← YOU ARE HERE          │
│   ════════════════════════════════════                          │
│                                                                  │
│   Type: SHARED TEMPLATE                                          │
│   Contains:                                                      │
│     • Liquid components (_includes/)                            │
│     • CSS stylesheets (assets/css/)                             │
│     • Documentation (docs/, CLAUDE.md)                          │
│                                                                  │
│   Does NOT contain:                                              │
│     • Project-specific content                                   │
│     • Images or media files                                      │
│     • Docker/deployment configs                                  │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ Used as git submodule by:
                              │
         ┌────────────────────┼────────────────────┐
         │                    │                    │
         ▼                    ▼                    ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│                 │  │                 │  │                 │
│   hyg-web-dev   │  │  lapland-web    │  │  [future-proj]  │
│   ═════════════ │  │  ════════════   │  │  ═════════════  │
│                 │  │                 │  │                 │
│   Hotel Yatri   │  │ Lapland Famille │  │  New Project    │
│   Gorkha        │  │                 │  │                 │
│                 │  │                 │  │                 │
│   Has own repo  │  │   Has own repo  │  │  Has own repo   │
│   Own content   │  │   Own content   │  │  Own content    │
│   Own domain    │  │   Own domain    │  │  Own domain     │
│                 │  │                 │  │                 │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

---

## How It Works

### Separation of Concerns

| What | Where | Shared? |
|------|-------|---------|
| **Design/Layout** | This template repo | ✅ Yes - All projects |
| **Components** | `src/_includes/*.liquid` | ✅ Yes - All projects |
| **CSS Styles** | `src/assets/css/*.css` | ✅ Yes - All projects |
| **Content/Text** | Project's `src/_data/*.json` | ❌ No - Per project |
| **Images** | Project's `src/assets/media/` | ❌ No - Per project |
| **Docker Config** | Project's `docker-compose.yml` | ❌ No - Per project |

### Project Directory Structure

Each project that uses this template looks like:

```
project-name/
│
├── template/                      ← Git submodule (THIS REPO)
│   └── src/
│       ├── _includes/             ← Shared components
│       └── assets/css/            ← Shared styles
│
├── src/
│   ├── _data/                     ← PROJECT CONTENT
│   │   ├── site.json              ← Site name, navigation
│   │   ├── homepage.json          ← Homepage text/content
│   │   └── media.json             ← Video URLs
│   │
│   ├── _includes → symlink to template/src/_includes
│   ├── assets/
│   │   ├── css → symlink to template/src/assets/css
│   │   └── media/                 ← PROJECT IMAGES
│   │
│   ├── index.liquid               ← Homepage (uses template)
│   └── [pages]/                   ← Other pages
│
├── docker-compose.dev.yml         ← Dev environment
├── docker-compose.yml             ← Production environment
├── .eleventy.js                   ← Eleventy config
└── .gitmodules                    ← Tracks this submodule
```

---

## Template Contents

### Components (`src/_includes/`)

```
_includes/
├── layouts/
│   └── base.liquid          ← HTML document wrapper
│
├── header.liquid            ← Site header & navigation
├── footer.liquid            ← Site footer
├── hero-homepage.liquid     ← Homepage hero section
├── faq-section.liquid       ← FAQ accordion
├── experiences-section.liquid
├── location-section.liquid
├── social-proof-section.liquid
├── conversion-section.liquid
├── trust-strip.liquid
├── video-showcase-section.liquid
├── why-section.liquid
├── inclusions-section.liquid
├── trip-length-section.liquid
├── availability-section.liquid
├── media-video.liquid       ← Video player component
└── hls-init.liquid          ← HLS video initialization
```

### Stylesheets (`src/assets/css/`)

```
css/
├── global.css       ← CSS variables, resets, animations
├── layout.css       ← Header, footer, navigation, grids
├── typography.css   ← Headings, text, fonts
├── components.css   ← Buttons, cards, UI elements
└── utilities.css    ← Helper classes
```

---

## Workflow

### Making Template Changes

```bash
# 1. Edit files in this repo
vim src/_includes/header.liquid
vim src/assets/css/components.css

# 2. Commit and push
git add .
git commit -m "Update header design"
git push

# 3. Sync to projects (run in each project)
cd ../hyg-web-dev/
git submodule update --remote template
git add template && git commit -m "Sync template" && git push
```

### Creating a New Project

See [docs/DEVELOPMENT-WORKFLOW.md](./docs/DEVELOPMENT-WORKFLOW.md) for complete instructions.

---

## Documentation

| Document | Purpose |
|----------|---------|
| [CLAUDE.md](./CLAUDE.md) | **AI Agent Instructions** - Rules and guidelines |
| [WORKFLOW.md](./WORKFLOW.md) | Quick reference for developers |
| [docs/DEVELOPMENT-WORKFLOW.md](./docs/DEVELOPMENT-WORKFLOW.md) | Complete development guide |

---

## Projects Using This Template

| Project | Description | Status |
|---------|-------------|--------|
| [hyg-web-dev](../hyg-web-dev/) | Hotel Yatri Gorkha website | Active |
| [lapland-web](../laplandfamille/) | Lapland Famille travel website | Active |

---

## Tech Stack

- **Static Site Generator:** [Eleventy (11ty)](https://www.11ty.dev/) v3.x
- **Template Language:** [Liquid](https://liquidjs.com/)
- **Styling:** Vanilla CSS with CSS Variables
- **Deployment:** Docker + Nginx + Traefik

---

## License

Private - Zunkire Labs

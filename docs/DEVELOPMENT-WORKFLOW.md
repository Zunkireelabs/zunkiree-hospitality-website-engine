# Development Workflow Guide

> **Last Updated:** January 2026
> **Maintainer:** Zunkire Labs

This guide explains how to work with the Zunkire Hospitality Website Engine - a shared template system for building hospitality websites (hotels, travel, tourism).

---

## Table of Contents

1. [Architecture Overview](#architecture-overview)
2. [Quick Start](#quick-start)
3. [Working on Existing Projects](#working-on-existing-projects)
4. [Creating a New Project](#creating-a-new-project)
5. [Template Updates & Syncing](#template-updates--syncing)
6. [Adding New Features](#adding-new-features)
7. [Content vs Template Changes](#content-vs-template-changes)
8. [Team Collaboration](#team-collaboration)
9. [Deployment](#deployment)
10. [Troubleshooting](#troubleshooting)

---

## Architecture Overview

### The Two-Repo System

We use a **shared template** architecture where design/code is separated from content:

```
┌─────────────────────────────────────────────────────────────┐
│         zunkire-hospitality-website-engine                  │
│         (TEMPLATE REPO - Shared across all projects)        │
├─────────────────────────────────────────────────────────────┤
│  src/_includes/     → Liquid components (header, footer,    │
│                       sections, layouts)                    │
│  src/assets/css/    → Global stylesheets                    │
│  package.json       → Template metadata                     │
└─────────────────────────────────────────────────────────────┘
                              │
          ┌───────────────────┼───────────────────┐
          │                   │                   │
          ▼                   ▼                   ▼
┌─────────────────┐  ┌─────────────────┐  ┌─────────────────┐
│   hyg-web-dev   │  │ lapland-web-dev │  │  [new-project]  │
│   (HYG Hotel)   │  │ (Lapland Fam.)  │  │                 │
├─────────────────┤  ├─────────────────┤  ├─────────────────┤
│ src/_data/      │  │ src/_data/      │  │ src/_data/      │
│   └─ site.json  │  │   └─ site.json  │  │   └─ site.json  │
│   └─ homepage.  │  │   └─ homepage.  │  │   └─ homepage.  │
│ src/assets/     │  │ src/assets/     │  │ src/assets/     │
│   └─ media/     │  │   └─ media/     │  │   └─ media/     │
│ template/ ──────┼──┼─────────────────┼──┼─► (submodule)   │
└─────────────────┘  └─────────────────┘  └─────────────────┘
```

### Key Concepts

| Concept | Location | Shared? | Examples |
|---------|----------|---------|----------|
| **Template** | `template/` submodule | ✅ Yes | Header, footer, CSS, layouts |
| **Content** | `src/_data/*.json` | ❌ No | Site name, text, navigation |
| **Media** | `src/assets/media/` | ❌ No | Images, videos |
| **Pages** | `src/*.liquid` | ❌ No | Page files referencing data |
| **Infrastructure** | `docker-compose*.yml` | ❌ No | Deployment config |

### Directory Structure

```
project-name/
├── template/                    ← Git submodule (DO NOT EDIT directly here)
│   └── src/
│       ├── _includes/           ← Shared Liquid components
│       │   ├── layouts/
│       │   │   └── base.liquid  ← HTML wrapper
│       │   ├── header.liquid
│       │   ├── footer.liquid
│       │   └── [sections].liquid
│       └── assets/css/          ← Shared stylesheets
│
├── src/
│   ├── _data/                   ← PROJECT CONTENT (edit this)
│   │   ├── site.json            ← Site name, nav, contact
│   │   ├── homepage.json        ← Homepage content
│   │   └── media.json           ← Video URLs
│   ├── _includes → ../template/src/_includes  (symlink)
│   ├── assets/
│   │   ├── css → ../../template/src/assets/css (symlink)
│   │   └── media/               ← PROJECT IMAGES (edit this)
│   ├── index.liquid             ← Homepage
│   └── [other pages].liquid
│
├── docker-compose.dev.yml       ← Dev environment
├── docker-compose.yml           ← Production environment
├── deploy.sh                    ← Production deployment
├── .eleventy.js                 ← Eleventy config
├── .gitmodules                  ← Submodule tracking
└── docs/                        ← Documentation
```

---

## Quick Start

### Prerequisites

- Git
- Docker & Docker Compose
- Node.js 18+ (for local development only)
- Access to the VPS (for deployment)

### Clone an Existing Project

```bash
# Clone with submodules
git clone --recurse-submodules <project-repo-url>
cd project-name

# If you forgot --recurse-submodules:
git submodule update --init --recursive

# Start development server
docker compose -f docker-compose.dev.yml up -d

# View logs
docker compose -f docker-compose.dev.yml logs -f

# Site will be available at the configured dev URL
```

---

## Working on Existing Projects

### Daily Development Workflow

```bash
# 1. Pull latest changes (including submodule updates)
git pull
git submodule update --recursive

# 2. Start dev server
docker compose -f docker-compose.dev.yml up -d

# 3. Make changes to content files in src/_data/
#    - Edit site.json for navigation, contact info
#    - Edit homepage.json for homepage content
#    - Add images to src/assets/media/

# 4. View changes (hot reload is enabled)
#    Browser auto-refreshes on file save

# 5. Commit your changes
git add .
git commit -m "Update homepage content"
git push
```

### What Files Should I Edit?

| I want to... | Edit this file | Location |
|--------------|----------------|----------|
| Change site name/logo | `site.json` | `src/_data/` |
| Update navigation menu | `site.json` | `src/_data/` |
| Change homepage text | `homepage.json` | `src/_data/` |
| Add/change images | Add files | `src/assets/media/` |
| Update video URLs | `media.json` | `src/_data/` |
| Change page structure | `*.liquid` files | `src/` |
| Change header/footer design | See [Template Updates](#template-updates--syncing) | |
| Change CSS/styles | See [Template Updates](#template-updates--syncing) | |

---

## Creating a New Project

### Step 1: Create Project Directory

```bash
cd /home/zunkireelabs/devprojects/websiteprojects/
mkdir new-project-name
cd new-project-name
git init
```

### Step 2: Add Template Submodule

```bash
git submodule add /home/zunkireelabs/devprojects/websiteprojects/zunkire-hospitality-website-engine template
```

### Step 3: Create Project Structure

```bash
# Create directories
mkdir -p src/_data src/assets/media docs

# Create symlinks to template
ln -sf ../template/src/_includes src/_includes
ln -sf ../../template/src/assets/css src/assets/css
```

### Step 4: Copy Starter Files

Copy from an existing project (e.g., HYG):
```bash
# Copy config files
cp ../hyg-web-dev/.eleventy.js .
cp ../hyg-web-dev/package.json .
cp ../hyg-web-dev/nginx.conf .
cp ../hyg-web-dev/deploy.sh .

# Copy docker files (update domain names!)
cp ../hyg-web-dev/docker-compose.dev.yml .
cp ../hyg-web-dev/docker-compose.yml .

# Copy starter content (then customize)
cp ../hyg-web-dev/src/_data/*.json src/_data/
cp ../hyg-web-dev/src/index.liquid src/
cp -r ../hyg-web-dev/src/contact src/
```

### Step 5: Customize Configuration

Edit these files to configure for your new project:

**package.json:**
```json
{
  "name": "new-project-name",
  ...
}
```

**docker-compose.dev.yml:**
- Change `container_name`
- Change Traefik router names
- Change domain in `Host()` rule

**docker-compose.yml:**
- Same changes as dev

**deploy.sh:**
- Update project name and URLs

**src/_data/site.json:**
- Update all content for new project

### Step 6: Initialize and Test

```bash
npm install
docker compose -f docker-compose.dev.yml up -d
```

---

## Template Updates & Syncing

### When to Update the Template

Update the template when you need to change:
- Header or footer design
- CSS styles (colors, fonts, layouts)
- Component structure (sections, cards)
- New reusable components

### Making Template Changes

**Option A: Edit directly in template repo**
```bash
cd /home/zunkireelabs/devprojects/websiteprojects/zunkire-hospitality-website-engine

# Make your changes to files in src/_includes/ or src/assets/css/

# Commit and push
git add .
git commit -m "Update header design"
git push
```

**Option B: Edit from within a project (recommended for testing)**
```bash
cd project-name/template

# Make changes
git add .
git commit -m "Update header design"
git push

# Go back to project root
cd ..

# Update submodule reference
git add template
git commit -m "Update template submodule"
git push
```

### Pulling Template Updates to Other Projects

```bash
cd other-project

# Pull latest template
git submodule update --remote template

# Commit the submodule update
git add template
git commit -m "Sync template updates"
git push

# Restart dev server to see changes
docker compose -f docker-compose.dev.yml restart
```

### Template Update Workflow Diagram

```
Developer A (HYG)                    Developer B (Lapland)
       │                                    │
       │ 1. Edit template/src/_includes/    │
       │    header.liquid                   │
       │                                    │
       ▼                                    │
┌──────────────────┐                        │
│ cd template/     │                        │
│ git commit       │                        │
│ git push         │                        │
└────────┬─────────┘                        │
         │                                  │
         │ 2. Push to template repo         │
         │                                  │
         ▼                                  │
┌──────────────────────────────────────────────────────────┐
│           zunkire-hospitality-website-engine             │
│                    (Template Repo)                       │
└──────────────────────────────────────────────────────────┘
                                            │
                                            │ 3. Pull updates
                                            │
                                            ▼
                               ┌──────────────────┐
                               │ git submodule    │
                               │ update --remote  │
                               │ template         │
                               └──────────────────┘
```

---

## Adding New Features

### Adding a New Component to Template

1. **Create the component file:**
   ```bash
   cd template/src/_includes/
   # Create new-component.liquid
   ```

2. **Add CSS if needed:**
   ```bash
   # Add styles to template/src/assets/css/components.css
   # Or create inline styles in the component
   ```

3. **Commit to template:**
   ```bash
   cd template
   git add .
   git commit -m "Add new-component"
   git push
   ```

4. **Use in projects:**
   ```liquid
   {% include "new-component.liquid" %}
   ```

### Adding a New Page to a Project

1. **Create the page file:**
   ```bash
   # For /about URL, create:
   mkdir -p src/about
   touch src/about/index.liquid
   ```

2. **Add frontmatter and content:**
   ```liquid
   ---
   layout: layouts/base.liquid
   title: About Us
   description: Learn about our company
   ---

   {% include "header.liquid" %}

   <main>
     <h1>{{ title }}</h1>
     <!-- Page content -->
   </main>

   {% include "footer.liquid" %}
   ```

3. **Add page data (if needed):**
   ```bash
   # Add to src/_data/about.json if the page needs its own data
   ```

### Adding New Data Fields

1. **Add to JSON data file:**
   ```json
   // src/_data/homepage.json
   {
     "new_section": {
       "title": "New Section",
       "items": [...]
     }
   }
   ```

2. **Reference in template:**
   ```liquid
   <section>
     <h2>{{ homepage.new_section.title }}</h2>
     {% for item in homepage.new_section.items %}
       <div>{{ item.name }}</div>
     {% endfor %}
   </section>
   ```

---

## Content vs Template Changes

### Decision Tree

```
Is this change about WHAT is displayed (text, images, data)?
│
├─ YES → Edit src/_data/*.json (Content)
│        No template sync needed
│        Just commit to your project
│
└─ NO → Is this about HOW it's displayed (layout, design, CSS)?
        │
        ├─ YES → Edit template/ (Template)
        │        Will affect ALL projects using this template
        │        Must sync to other projects
        │
        └─ ONLY FOR THIS PROJECT? → Edit src/*.liquid (Page-specific)
                                    Won't affect other projects
```

### Examples

| Change | Type | Where to Edit |
|--------|------|---------------|
| "Change hotel name to 'Grand Hotel'" | Content | `src/_data/site.json` |
| "Add new image to hero" | Content | `src/assets/media/` + `homepage.json` |
| "Make header sticky" | Template | `template/src/_includes/header.liquid` |
| "Change primary color to blue" | Template | `template/src/assets/css/global.css` |
| "Add testimonials section" | Template | `template/src/_includes/testimonials.liquid` |
| "Add custom page only for HYG" | Page | `src/custom-page/index.liquid` |

---

## Team Collaboration

### Git Workflow

```
main (production-ready)
  │
  └── feature/your-feature-name
        │
        └── Work here, then PR to main
```

### Before Starting Work

```bash
# Always pull latest
git pull
git submodule update --recursive

# Create feature branch
git checkout -b feature/your-feature-name
```

### Committing Changes

```bash
# Stage your changes
git add .

# Commit with clear message
git commit -m "Add: new testimonials section"
# or
git commit -m "Fix: header alignment on mobile"
# or
git commit -m "Update: homepage hero content"

# Push
git push -u origin feature/your-feature-name
```

### Code Review Checklist

- [ ] Content changes only affect `src/_data/` and `src/assets/media/`
- [ ] Template changes are intentional (will affect all projects)
- [ ] No hardcoded content in template files
- [ ] Images are optimized (WebP/AVIF with fallbacks)
- [ ] Tested on mobile viewport
- [ ] Hot reload works (no build errors)

---

## Deployment

### Development Environment

```bash
# Start dev server
docker compose -f docker-compose.dev.yml up -d

# View logs
docker compose -f docker-compose.dev.yml logs -f

# Restart (after template changes)
docker compose -f docker-compose.dev.yml restart

# Stop
docker compose -f docker-compose.dev.yml down
```

### Production Deployment

```bash
# Deploy to production (requires root password)
./deploy.sh

# What happens:
# 1. Builds site with ELEVENTY_ENV=production
# 2. Outputs to _site-prod/ (root-owned, read-only)
# 3. Restarts production container
```

### Domain Configuration

Each project has its own domains configured in docker-compose files:

| Project | Dev URL | Production URL |
|---------|---------|----------------|
| HYG | hyg.zunkireelabs.com | (TBD) |
| Lapland | dev.laplandfamille.co.uk | laplandfamille.co.uk |

---

## Troubleshooting

### Symlinks Not Working

```bash
# Verify symlinks
ls -la src/_includes
ls -la src/assets/css

# Should show:
# _includes -> ../template/src/_includes
# css -> ../../template/src/assets/css

# If broken, recreate:
rm src/_includes src/assets/css
ln -sf ../template/src/_includes src/_includes
ln -sf ../../template/src/assets/css src/assets/css
```

### Submodule Not Initialized

```bash
# Initialize submodule
git submodule update --init --recursive

# If still failing
git submodule deinit template
git submodule update --init template
```

### Template Changes Not Showing

```bash
# Make sure you're in the project root
cd project-name

# Pull latest template
git submodule update --remote template

# Restart dev server
docker compose -f docker-compose.dev.yml restart
```

### Build Errors

```bash
# Check container logs
docker compose -f docker-compose.dev.yml logs -f

# Common issues:
# - Missing include file → Check symlinks
# - JSON syntax error → Validate JSON in src/_data/
# - Liquid syntax error → Check template files
```

### Permission Issues (Production)

```bash
# Production files are root-owned for safety
# Only ./deploy.sh can update production

# If you need to manually fix:
sudo chown -R root:root _site-prod/
sudo chmod -R 755 _site-prod/
```

---

## Summary Cheatsheet

```bash
# Daily workflow
git pull && git submodule update --recursive
docker compose -f docker-compose.dev.yml up -d

# Edit content
vim src/_data/site.json
vim src/_data/homepage.json

# Edit template (affects all projects!)
cd template
vim src/_includes/header.liquid
git add . && git commit -m "Update header" && git push
cd ..
git add template && git commit -m "Sync template"

# Pull template updates
git submodule update --remote template
docker compose -f docker-compose.dev.yml restart

# Deploy to production
./deploy.sh
```

---

## Contact

- **Template Issues:** Create issue in template repo
- **Project Issues:** Create issue in project repo
- **Questions:** Contact Zunkire Labs team

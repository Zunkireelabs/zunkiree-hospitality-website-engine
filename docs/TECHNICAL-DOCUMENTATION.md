# Zunkire Hospitality Website Engine - Complete Technical Documentation

## Document Purpose
This document provides a complete technical specification of the Zunkire Hospitality Website Engine and its implementation in projects like Hotel Yatri Gorkha (HYG). It is designed for:
- **Stakeholders** - Understanding architecture and business value
- **AI Agents (ChatGPT, Claude)** - Developing new features with full context
- **Developers** - Onboarding and daily reference

---

## 1. EXECUTIVE SUMMARY

### What This System Is
A **scalable static website engine** for hospitality businesses (hotels, travel companies, tourism operators). It uses a **shared template architecture** where multiple websites share the same components and styling, while maintaining unique content and branding.

### Key Business Benefits
- **Rapid Deployment**: New hospitality website in hours, not weeks
- **Consistent Quality**: All sites use battle-tested components
- **Cost Efficiency**: Fix bugs once, all sites benefit
- **Easy Updates**: Change shared components, all sites update
- **SEO Optimized**: Static HTML = fast loading = better rankings

### Technology Stack
| Layer | Technology | Purpose |
|-------|------------|---------|
| Static Site Generator | Eleventy (11ty) v3.1.2 | Compiles templates to HTML |
| Templating | Liquid | Dynamic content injection |
| Styling | CSS with Design Tokens | Consistent theming |
| Development | Docker + Node.js | Containerized dev environment |
| Production | Docker + Nginx | High-performance static serving |
| Routing/SSL | Traefik | Reverse proxy + Let's Encrypt SSL |
| Video Streaming | HLS (Bunny Stream) | Adaptive video delivery |

---

## 2. ARCHITECTURE OVERVIEW

### The Two-Repository System

```
┌─────────────────────────────────────────────────────────────────┐
│                    TEMPLATE REPOSITORY                           │
│         zunkire-hospitality-website-engine (GitHub)              │
│                                                                  │
│  ┌──────────────────────┐    ┌──────────────────────┐          │
│  │  src/_includes/       │    │  src/assets/css/     │          │
│  │  ├── layouts/         │    │  ├── global.css      │          │
│  │  │   └── base.liquid  │    │  ├── layout.css      │          │
│  │  ├── header.liquid    │    │  ├── components.css  │          │
│  │  ├── footer.liquid    │    │  ├── typography.css  │          │
│  │  ├── hero-homepage    │    │  └── utilities.css   │          │
│  │  ├── faq-section      │    │                      │          │
│  │  ├── inquiry-form     │    │  2,196 lines total   │          │
│  │  └── 15 more...       │    └──────────────────────┘          │
│  │                       │                                       │
│  │  18 components total  │                                       │
│  │  6,500+ lines         │                                       │
│  └──────────────────────┘                                       │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ git submodule
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                     PROJECT REPOSITORY                           │
│              hyg-web-dev (Hotel Yatri Gorkha)                   │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │  template/   │  │  src/_data/  │  │  src/assets/media/   │  │
│  │  (submodule) │  │  site.json   │  │  hero-images/        │  │
│  │      ↓       │  │  homepage.   │  │  room-photos/        │  │
│  │  symlinked   │  │  media.json  │  │  logo/               │  │
│  │  to src/     │  │              │  │                      │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
│         │                  │                    │                │
│         │      SHARED      │   PROJECT-SPECIFIC │                │
│         └──────────────────┴────────────────────┘                │
│                              │                                   │
│                              ▼                                   │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                    ELEVENTY BUILD                         │  │
│  │                                                           │  │
│  │   template components + project data = static HTML        │  │
│  │                                                           │  │
│  └──────────────────────────────────────────────────────────┘  │
│                              │                                   │
│                              ▼                                   │
│  ┌──────────────┐  ┌──────────────┐                            │
│  │  _site-dev/  │  │  _site-prod/ │                            │
│  │  (writable)  │  │  (root-only) │                            │
│  │  hot reload  │  │  nginx serve │                            │
│  └──────────────┘  └──────────────┘                            │
└─────────────────────────────────────────────────────────────────┘
```

### Why This Architecture?

| Problem | Solution |
|---------|----------|
| Code duplication across sites | Shared template repository |
| Inconsistent designs | Centralized CSS design tokens |
| Bug fixes needed everywhere | Fix once in template, pull to all projects |
| Slow new site creation | Clone structure, add content, deploy |
| Content mixed with code | JSON data files separate from templates |

---

## 3. TEMPLATE ENGINE COMPONENTS

### 3.1 Complete Component Inventory

#### Layout Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| Base Layout | `layouts/base.liquid` | ~80 | HTML document wrapper, head meta, CSS/JS includes |

#### Navigation Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| Header | `header.liquid` | 797 | Sticky header, logo, nav menu, mega-dropdown, mobile hamburger, CTAs |
| Footer | `footer.liquid` | 325 | Multi-column footer, contact info, social links, copyright |

#### Hero/Landing Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| Hero Homepage | `hero-homepage.liquid` | 791 | Full-width hero with image slider, headline, search bar, CTAs |

#### Content Section Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| Experiences Section | `experiences-section.liquid` | 349 | Grid of experience/service cards with icons |
| Location Section | `location-section.liquid` | 332 | Location information with map integration |
| Trip Length Section | `trip-length-section.liquid` | 471 | Duration/package selector cards |
| Why Section | `why-section.liquid` | 236 | Benefits/features highlight grid |
| Why Lapland Section | `why-lapland-section.liquid` | 151 | Destination-specific selling points |
| Inclusions Section | `inclusions-section.liquid` | 204 | What's included checklist |

#### Social Proof Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| Social Proof Section | `social-proof-section.liquid` | 300 | Testimonials carousel/grid |
| Trust Strip | `trust-strip.liquid` | 114 | Trust badges, certifications, awards |

#### Interactive Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| FAQ Section | `faq-section.liquid` | 339 | Accordion FAQ with category filters and search |
| Inquiry Form Section | `inquiry-form-section.liquid` | 836 | Multi-field contact form with validation |
| Availability Section | `availability-section.liquid` | 226 | Availability calendar/checker |

#### Conversion Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| Conversion Section | `conversion-section.liquid` | 288 | CTA blocks for booking/contact |

#### Media Components
| Component | File | Lines | Purpose |
|-----------|------|-------|---------|
| Video Showcase Section | `video-showcase-section.liquid` | 172 | Video player with poster |
| Media Video | `media-video.liquid` | 43 | Embedded video helper |
| HLS Init | `hls-init.liquid` | 69 | HLS.js initialization for streaming |

### 3.2 Component Architecture Pattern

Every component follows this pattern:

```liquid
{%- comment -%}
  COMPONENT: hero-homepage.liquid
  PURPOSE: Homepage hero section with image slider

  REQUIRED DATA (from homepage.json):
  - homepage.hero.headline (string)
  - homepage.hero.tagline (string)
  - homepage.hero.images (array of image objects)
  - homepage.hero.cta_primary (object with text, url)
{%- endcomment -%}

<!-- HTML STRUCTURE -->
<section class="hero-homepage" id="hero">
  <div class="hero-slider">
    {% for image in homepage.hero.images %}
      <div class="slide" style="background-image: url('{{ image.url }}')">
        <!-- Slide content -->
      </div>
    {% endfor %}
  </div>

  <div class="hero-content">
    <h1>{{ homepage.hero.headline }}</h1>
    <p>{{ homepage.hero.tagline }}</p>

    {% if homepage.hero.cta_primary %}
      <a href="{{ homepage.hero.cta_primary.url }}" class="cta-primary">
        {{ homepage.hero.cta_primary.text }}
      </a>
    {% endif %}
  </div>
</section>

<!-- COMPONENT-SPECIFIC CSS (inline) -->
<style>
  .hero-homepage {
    position: relative;
    min-height: 80vh;
    /* Uses CSS variables from global.css */
    background: var(--color-arctic);
  }

  .hero-content h1 {
    font-size: var(--text-5xl);
    color: var(--color-white);
  }

  @media (max-width: 768px) {
    .hero-homepage {
      min-height: 60vh;
    }
    .hero-content h1 {
      font-size: var(--text-3xl);
    }
  }
</style>

<!-- COMPONENT-SPECIFIC JS (if needed) -->
<script>
  // Slider initialization, etc.
</script>
```

### 3.3 Key Design Principles

1. **Data-Driven**: All text/content comes from Liquid variables, never hardcoded
2. **Self-Contained**: Each component includes its own CSS inline
3. **CSS Variables**: All values reference design tokens
4. **Responsive**: Mobile-first with breakpoints at 768px, 1024px
5. **Accessible**: Semantic HTML, ARIA labels, keyboard navigation
6. **Conditional**: Components gracefully handle missing optional data

---

## 4. CSS ARCHITECTURE

### 4.1 File Structure (2,196 lines total)

```
src/assets/css/
├── global.css        (646 lines)  ← Foundation: variables, reset, animations
├── layout.css        (429 lines)  ← Structure: header, footer, grids
├── components.css    (467 lines)  ← UI: buttons, cards, forms
├── typography.css    (287 lines)  ← Text: headings, paragraphs, links
└── utilities.css     (367 lines)  ← Helpers: spacing, flexbox, animations
```

### 4.2 Design Token System

#### Color Palette
```css
:root {
  /* Primary Brand Colors */
  --color-primary: #871C53;           /* Plum - main brand color */
  --color-primary-light: #A82B6A;     /* Lighter plum for hover */
  --color-primary-dark: #6B1642;      /* Darker plum for active */

  /* Arctic/Nature Theme */
  --color-arctic: #1E3A5F;            /* Deep blue */
  --color-ice: #E8F4F8;               /* Light ice blue */
  --color-frost: #F5F9FB;             /* Near-white frost */
  --color-snow: #FFFFFF;              /* Pure white */
  --color-aurora: #4ECDC4;            /* Teal accent */

  /* Neutral Grays */
  --color-gray-50: #F9FAFB;
  --color-gray-100: #F3F4F6;
  --color-gray-200: #E5E7EB;
  --color-gray-300: #D1D5DB;
  --color-gray-400: #9CA3AF;
  --color-gray-500: #6B7280;
  --color-gray-600: #4B5563;
  --color-gray-700: #374151;
  --color-gray-800: #1F2937;
  --color-gray-900: #111827;

  /* Semantic Colors */
  --color-success: #10B981;
  --color-warning: #F59E0B;
  --color-error: #EF4444;
  --color-info: #3B82F6;

  /* Text Colors */
  --color-text-primary: var(--color-gray-900);
  --color-text-secondary: var(--color-gray-600);
  --color-text-muted: var(--color-gray-400);
  --color-text-inverse: var(--color-white);
}
```

#### Spacing System (8px base)
```css
:root {
  --space-0: 0;
  --space-1: 0.25rem;    /* 4px */
  --space-2: 0.5rem;     /* 8px */
  --space-3: 0.75rem;    /* 12px */
  --space-4: 1rem;       /* 16px */
  --space-5: 1.25rem;    /* 20px */
  --space-6: 1.5rem;     /* 24px */
  --space-8: 2rem;       /* 32px */
  --space-10: 2.5rem;    /* 40px */
  --space-12: 3rem;      /* 48px */
  --space-16: 4rem;      /* 64px */
  --space-20: 5rem;      /* 80px */
  --space-24: 6rem;      /* 96px */
}
```

#### Typography Scale
```css
:root {
  /* Font Families */
  --font-primary: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;
  --font-heading: 'Inter', var(--font-primary);

  /* Font Sizes */
  --text-xs: 0.75rem;     /* 12px */
  --text-sm: 0.875rem;    /* 14px */
  --text-base: 1rem;      /* 16px */
  --text-lg: 1.125rem;    /* 18px */
  --text-xl: 1.25rem;     /* 20px */
  --text-2xl: 1.5rem;     /* 24px */
  --text-3xl: 1.875rem;   /* 30px */
  --text-4xl: 2.25rem;    /* 36px */
  --text-5xl: 3rem;       /* 48px */
  --text-6xl: 3.5rem;     /* 56px */

  /* Font Weights */
  --font-normal: 400;
  --font-medium: 500;
  --font-semibold: 600;
  --font-bold: 700;

  /* Line Heights */
  --leading-none: 1;
  --leading-tight: 1.1;
  --leading-snug: 1.375;
  --leading-normal: 1.5;
  --leading-relaxed: 1.7;
}
```

#### Animation & Transitions
```css
:root {
  /* Transitions */
  --transition-fast: 0.15s ease;
  --transition-normal: 0.3s ease;
  --transition-slow: 0.5s ease;

  /* Shadows */
  --shadow-sm: 0 1px 2px rgba(0, 0, 0, 0.05);
  --shadow-md: 0 4px 6px rgba(0, 0, 0, 0.1);
  --shadow-lg: 0 10px 15px rgba(0, 0, 0, 0.1);
  --shadow-xl: 0 20px 25px rgba(0, 0, 0, 0.15);

  /* Border Radius */
  --radius-sm: 0.25rem;   /* 4px */
  --radius-md: 0.5rem;    /* 8px */
  --radius-lg: 1rem;      /* 16px */
  --radius-xl: 1.5rem;    /* 24px */
  --radius-full: 9999px;  /* Pill shape */
}
```

### 4.3 CSS Loading Order

In `layouts/base.liquid`:
```liquid
<head>
  <!-- 1. Foundation (variables must load first) -->
  <link rel="stylesheet" href="/assets/css/global.css">

  <!-- 2. Typography (depends on variables) -->
  <link rel="stylesheet" href="/assets/css/typography.css">

  <!-- 3. Layout (structural patterns) -->
  <link rel="stylesheet" href="/assets/css/layout.css">

  <!-- 4. Components (UI elements) -->
  <link rel="stylesheet" href="/assets/css/components.css">

  <!-- 5. Utilities (helpers, overrides) -->
  <link rel="stylesheet" href="/assets/css/utilities.css">
</head>
```

---

## 5. DATA STRUCTURE SPECIFICATIONS

### 5.1 site.json (Required for all projects)

```json
{
  "name": "Hotel Yatri Gorkha",
  "tagline": "Your Gateway to the Himalayas",
  "description": "Premium hotel in Gorkha, Nepal with mountain views",

  "logo": "/assets/media/logo/logo-transparent.png",
  "favicon": "/assets/media/favicon.ico",

  "contact": {
    "email": "info@hotelyatrigorkha.com",
    "phone": "+977 986-1000005",
    "whatsapp": "+9779861000005",
    "address": {
      "street": "Sami Dadha",
      "city": "Gorkha",
      "country": "Nepal",
      "postal_code": "36300"
    }
  },

  "social": {
    "facebook": "https://facebook.com/hotelyatrigorkha",
    "instagram": "https://instagram.com/hotelyatrigorkha",
    "tripadvisor": "https://tripadvisor.com/..."
  },

  "nav": [
    { "text": "Home", "url": "/" },
    { "text": "Rooms", "url": "/rooms" },
    { "text": "Services", "url": "/services" },
    {
      "text": "Explore",
      "dropdown": {
        "intro": {
          "title": "Discover Gorkha",
          "description": "Explore the birthplace of Nepal"
        },
        "items": [
          { "text": "Gorkha Durbar", "url": "/explore/durbar", "icon": "castle" },
          { "text": "Manakamana", "url": "/explore/manakamana", "icon": "temple" }
        ]
      }
    },
    { "text": "About", "url": "/about" },
    { "text": "Contact", "url": "/contact" }
  ],

  "cta": {
    "primary": {
      "text": "Book Now",
      "url": "/booking",
      "style": "filled"
    },
    "secondary": {
      "text": "Call Us",
      "url": "tel:+9779861000005",
      "style": "outline"
    }
  },

  "footer": {
    "columns": [
      {
        "title": "Quick Links",
        "links": [
          { "text": "Rooms", "url": "/rooms" },
          { "text": "Services", "url": "/services" }
        ]
      },
      {
        "title": "Policies",
        "links": [
          { "text": "Privacy Policy", "url": "/privacy" },
          { "text": "Terms of Service", "url": "/terms" }
        ]
      }
    ],
    "copyright": "© 2024 Hotel Yatri Gorkha. All rights reserved."
  }
}
```

### 5.2 homepage.json (Homepage content)

```json
{
  "meta": {
    "title": "Hotel Yatri Gorkha - Premium Himalayan Accommodation",
    "description": "Experience luxury in the heart of Gorkha...",
    "keywords": ["hotel", "gorkha", "nepal", "himalaya"]
  },

  "hero": {
    "headline": "Your Gateway to the Himalayas",
    "tagline": "Experience authentic Nepali hospitality in historic Gorkha",
    "images": [
      {
        "url": "/assets/media/hero/mountain-view.jpg",
        "alt": "Panoramic mountain view from hotel"
      },
      {
        "url": "/assets/media/hero/hotel-exterior.jpg",
        "alt": "Hotel Yatri Gorkha exterior"
      }
    ],
    "cta_primary": {
      "text": "Check Availability",
      "url": "/booking"
    },
    "cta_secondary": {
      "text": "View Rooms",
      "url": "/rooms"
    },
    "search_bar": {
      "enabled": true,
      "placeholder": "Search rooms, services...",
      "fields": ["check_in", "check_out", "guests"]
    }
  },

  "trust_badges": {
    "enabled": true,
    "headline": "Why Choose Us",
    "badges": [
      {
        "icon": "star",
        "title": "4.8 Rating",
        "subtitle": "500+ Reviews"
      },
      {
        "icon": "award",
        "title": "TripAdvisor",
        "subtitle": "Certificate of Excellence"
      },
      {
        "icon": "shield",
        "title": "Safe & Secure",
        "subtitle": "COVID-19 Protocols"
      }
    ]
  },

  "experiences_section": {
    "enabled": true,
    "headline": "Hotel Amenities",
    "tagline": "Everything you need for a comfortable stay",
    "experiences": [
      {
        "title": "Restaurant",
        "description": "Traditional Nepali and international cuisine",
        "icon": "utensils",
        "image": "/assets/media/services/restaurant.jpg",
        "url": "/services#restaurant"
      },
      {
        "title": "Free WiFi",
        "description": "High-speed internet throughout",
        "icon": "wifi",
        "image": "/assets/media/services/wifi.jpg"
      }
    ]
  },

  "location_section": {
    "enabled": true,
    "headline": "Prime Location",
    "description": "Situated in the heart of Gorkha with easy access to...",
    "map_embed": "https://maps.google.com/embed?...",
    "features": [
      { "icon": "map-pin", "text": "5 min to Gorkha Durbar" },
      { "icon": "car", "text": "3 hours from Kathmandu" }
    ]
  },

  "social_proof_section": {
    "enabled": true,
    "headline": "What Our Guests Say",
    "testimonials": [
      {
        "quote": "Amazing hospitality and breathtaking views...",
        "author": "John Smith",
        "location": "United Kingdom",
        "rating": 5,
        "image": "/assets/media/testimonials/john.jpg"
      }
    ]
  },

  "faq_section": {
    "enabled": true,
    "headline": "Frequently Asked Questions",
    "categories": ["booking", "amenities", "location"],
    "faqs": [
      {
        "question": "What is the check-in time?",
        "answer": "Check-in is from 2:00 PM...",
        "category": "booking"
      }
    ]
  },

  "conversion_section": {
    "enabled": true,
    "headline": "Ready to Experience Gorkha?",
    "tagline": "Book your stay today",
    "cta": {
      "text": "Book Now",
      "url": "/booking"
    },
    "background_image": "/assets/media/cta-background.jpg"
  }
}
```

### 5.3 media.json (Video registry)

```json
{
  "hero": {
    "homepage": {
      "hls": "https://cdn.bunnystream.com/.../playlist.m3u8",
      "thumbnail": "/assets/media/hero/video-poster.jpg",
      "fallback_mp4": "/assets/media/hero/video.mp4"
    }
  },
  "showcase": {
    "hotel_tour": {
      "hls": "https://cdn.bunnystream.com/.../playlist.m3u8",
      "thumbnail": "/assets/media/videos/tour-poster.jpg",
      "title": "Virtual Hotel Tour",
      "duration": "2:30"
    }
  }
}
```

---

## 6. PROJECT FILE STRUCTURE

### 6.1 Complete Directory Tree

```
project-name/
├── .eleventy.js                 # Eleventy configuration
├── .gitmodules                  # Submodule reference
├── .gitignore                   # Excludes node_modules, _site-*
├── package.json                 # Dependencies (eleventy only)
├── package-lock.json
│
├── docker-compose.dev.yml       # Development: Node.js + hot reload
├── docker-compose.yml           # Production: Nginx static serving
├── deploy.sh                    # Production deployment script
├── nginx.conf                   # Nginx configuration
│
├── CLAUDE.md                    # AI agent instructions
├── README.md                    # Project readme
│
├── template/                    # GIT SUBMODULE (shared)
│   ├── src/
│   │   ├── _includes/           # 18 Liquid components
│   │   │   ├── layouts/
│   │   │   │   └── base.liquid
│   │   │   ├── header.liquid
│   │   │   ├── footer.liquid
│   │   │   └── [15 more components]
│   │   └── assets/css/          # 5 CSS files
│   │       ├── global.css
│   │       ├── layout.css
│   │       ├── components.css
│   │       ├── typography.css
│   │       └── utilities.css
│   ├── docs/
│   ├── CLAUDE.md
│   └── package.json
│
├── src/                         # PROJECT SOURCE
│   ├── _includes → ../template/src/_includes    # SYMLINK
│   ├── _data/                   # PROJECT CONTENT
│   │   ├── site.json            # Site config
│   │   ├── homepage.json        # Homepage content
│   │   └── media.json           # Video URLs
│   ├── assets/
│   │   ├── css → ../../template/src/assets/css  # SYMLINK
│   │   └── media/               # PROJECT IMAGES
│   │       ├── hero/
│   │       ├── rooms/
│   │       ├── logo/
│   │       └── [other media]
│   ├── index.liquid             # Homepage
│   ├── about/
│   │   └── index.liquid
│   ├── contact/
│   │   └── index.liquid
│   └── [other pages]
│
├── _site-dev/                   # DEV BUILD OUTPUT (auto-generated)
│   └── [built HTML files]
│
└── _site-prod/                  # PROD BUILD OUTPUT (root-owned)
    └── [deployed HTML files]
```

### 6.2 Symlink Architecture

```
src/_includes → ../template/src/_includes
                     │
                     └─► All 18 components available
                         {% include "header.liquid" %}
                         {% include "faq-section.liquid" %}

src/assets/css → ../../template/src/assets/css
                     │
                     └─► All 5 CSS files served at /assets/css/
                         <link href="/assets/css/global.css">
```

**Creating symlinks:**
```bash
# In project root
cd src
ln -s ../template/src/_includes _includes
cd assets
ln -s ../../template/src/assets/css css
```

---

## 7. ELEVENTY CONFIGURATION

### 7.1 .eleventy.js (Complete)

```javascript
module.exports = function(eleventyConfig) {

  // ============================================
  // STATIC ASSET HANDLING
  // ============================================

  // Copy assets directory to output
  eleventyConfig.addPassthroughCopy("src/assets");

  // Watch CSS for changes (triggers rebuild)
  eleventyConfig.addWatchTarget("src/assets/css");

  // ============================================
  // ENVIRONMENT CONFIGURATION
  // ============================================

  // Expose ELEVENTY_ENV to templates
  eleventyConfig.addGlobalData("env", process.env.ELEVENTY_ENV || 'production');

  // ============================================
  // DEVELOPMENT SERVER (Critical for Docker)
  // ============================================

  eleventyConfig.setServerOptions({
    port: 8080,
    host: '0.0.0.0',        // MUST be 0.0.0.0 for Docker/Traefik
    showAllHosts: true,
    watch: ['src/**/*'],
    liveReload: true
  });

  // ============================================
  // OUTPUT DIRECTORY (Environment-aware)
  // ============================================

  const isDev = process.env.ELEVENTY_ENV === 'development';
  const outputDir = process.env.ELEVENTY_OUTPUT_DIR || (isDev ? '_site-dev' : '_site-prod');

  // ============================================
  // DIRECTORY CONFIGURATION
  // ============================================

  return {
    dir: {
      input: "src",
      output: outputDir,
      includes: "_includes",    // Symlinked to template
      data: "_data"            // Project-specific JSON
    },
    templateFormats: ["md", "njk", "html", "liquid"],
    htmlTemplateEngine: "liquid",
    markdownTemplateEngine: "liquid"
  };
};
```

### 7.2 Critical Configuration Notes

| Setting | Value | Why |
|---------|-------|-----|
| `host` | `0.0.0.0` | **Required** for Docker. `localhost` blocks Traefik routing |
| `includes` | `_includes` | Points to symlink → template components |
| `output` | `_site-dev` or `_site-prod` | Separate dirs prevent accidental production changes |
| `templateFormats` | `liquid` primary | Consistent with template components |

---

## 8. DOCKER CONFIGURATION

### 8.1 Development Environment (docker-compose.dev.yml)

```yaml
services:
  hyg-dev:
    image: node:18
    container_name: hyg-dev
    working_dir: /app

    # Install deps and start dev server
    command: sh -c "npm install && npm start"

    # Environment for Eleventy
    environment:
      - ELEVENTY_ENV=development
      - ELEVENTY_OUTPUT_DIR=_site-dev

    # Mount project with anonymous volume for node_modules
    volumes:
      - .:/app
      - /app/node_modules

    # Join Traefik network
    networks:
      - hosting

    # Traefik routing labels
    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=hosting"
      # HTTP redirect to HTTPS
      - "traefik.http.routers.hyg-dev.rule=Host(`hyg.zunkireelabs.com`)"
      - "traefik.http.routers.hyg-dev.entrypoints=web"
      - "traefik.http.routers.hyg-dev.middlewares=redirect-to-https"
      # HTTPS with Let's Encrypt
      - "traefik.http.routers.hyg-dev-secure.rule=Host(`hyg.zunkireelabs.com`)"
      - "traefik.http.routers.hyg-dev-secure.entrypoints=websecure"
      - "traefik.http.routers.hyg-dev-secure.tls.certresolver=letsencrypt"
      - "traefik.http.services.hyg-dev.loadbalancer.server.port=8080"

    # Health check
    healthcheck:
      test: ["CMD", "wget", "-q", "--spider", "http://localhost:8080"]
      interval: 30s
      timeout: 10s
      retries: 3

    restart: unless-stopped

networks:
  hosting:
    external: true
```

### 8.2 Production Environment (docker-compose.yml)

```yaml
services:
  hyg-prod:
    image: nginx:alpine
    container_name: hyg-prod

    # Mount nginx config and built files (read-only)
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf:ro
      - ./_site-prod:/usr/share/nginx/html:ro

    networks:
      - hosting

    labels:
      - "traefik.enable=true"
      - "traefik.docker.network=hosting"
      # Production domain
      - "traefik.http.routers.hyg-prod.rule=Host(`hotelyatrigorkha.com`) || Host(`www.hotelyatrigorkha.com`)"
      - "traefik.http.routers.hyg-prod.entrypoints=web"
      - "traefik.http.routers.hyg-prod.middlewares=redirect-to-https"
      # HTTPS
      - "traefik.http.routers.hyg-prod-secure.rule=Host(`hotelyatrigorkha.com`) || Host(`www.hotelyatrigorkha.com`)"
      - "traefik.http.routers.hyg-prod-secure.entrypoints=websecure"
      - "traefik.http.routers.hyg-prod-secure.tls.certresolver=letsencrypt"
      - "traefik.http.services.hyg-prod.loadbalancer.server.port=80"

    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost/"]
      interval: 30s
      timeout: 10s
      retries: 3

    restart: unless-stopped

networks:
  hosting:
    external: true
```

### 8.3 Nginx Configuration (nginx.conf)

```nginx
server {
    listen 80;
    server_name _;
    root /usr/share/nginx/html;
    index index.html;

    # Clean URLs: /about → /about/index.html
    location / {
        try_files $uri $uri/ $uri/index.html =404;
    }

    # No caching for HTML (always fresh)
    location ~* \.html$ {
        add_header Cache-Control "no-cache, must-revalidate";
    }

    # Aggressive caching for static assets (30 days)
    location ~* \.(jpg|jpeg|png|webp|avif|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    # Video caching with range requests
    location ~* \.(mp4|webm|ogg|ogv)$ {
        expires 30d;
        add_header Cache-Control "public";
        add_header Accept-Ranges bytes;
    }

    # Gzip compression
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml;

    # Security: Block hidden files
    location ~ /\. {
        deny all;
    }

    # Allow .well-known for SSL verification
    location ^~ /.well-known/ {
        allow all;
    }

    # Custom error pages (optional)
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
}
```

---

## 9. DEPLOYMENT WORKFLOW

### 9.1 Development Workflow

```
┌──────────────────────────────────────────────────────────────────┐
│                    DEVELOPMENT WORKFLOW                           │
└──────────────────────────────────────────────────────────────────┘

1. START DEV ENVIRONMENT
   $ docker-compose -f docker-compose.dev.yml up -d

2. EDIT FILES
   Edit src/_data/homepage.json       (content)
   Edit src/index.liquid              (page structure)
   Edit template/src/_includes/*.liquid (components)
   Edit template/src/assets/css/*.css   (styles)

3. AUTO-REBUILD
   Eleventy detects change → rebuilds in ~50ms → _site-dev/

4. AUTO-REFRESH
   BrowserSync pushes update via WebSocket → browser refreshes

5. VIEW CHANGES
   https://hyg.zunkireelabs.com (via Traefik)

6. REPEAT 2-5

7. STOP DEV
   $ docker-compose -f docker-compose.dev.yml down
```

### 9.2 Production Deployment

```
┌──────────────────────────────────────────────────────────────────┐
│                   PRODUCTION DEPLOYMENT                           │
└──────────────────────────────────────────────────────────────────┘

$ ./deploy.sh

Script performs:

1. BUILD TO TEMP DIRECTORY
   ELEVENTY_ENV=production ELEVENTY_OUTPUT_DIR=_site-prod-temp npm run build

2. ESCALATE TO ROOT (prompts for password)
   su -c "
     rm -rf _site-prod/
     mv _site-prod-temp/ _site-prod/
     chown -R root:root _site-prod/
     chmod -R 755 _site-prod/
   "

3. RESTART CONTAINER
   docker compose restart

4. VERIFY
   Site live at https://hotelyatrigorkha.com
```

### 9.3 deploy.sh Script

```bash
#!/bin/bash
set -e

echo "Starting production deployment..."

# Step 1: Build to temporary directory
echo "Building site..."
ELEVENTY_ENV=production ELEVENTY_OUTPUT_DIR=_site-prod-temp npm run build

# Step 2: Verify build succeeded
if [ ! -d "_site-prod-temp" ]; then
    echo "Build failed - no output directory"
    exit 1
fi

# Step 3: Switch directories with root privileges
echo "Switching production directory (requires root)..."
su -c "
    rm -rf _site-prod/ && \
    mv _site-prod-temp/ _site-prod/ && \
    chown -R root:root _site-prod/ && \
    chmod -R 755 _site-prod/
"

# Step 4: Restart container
echo "Restarting production container..."
docker compose restart

echo "Deployment complete!"
echo "Site live at: https://hotelyatrigorkha.com"
```

---

## 10. TEMPLATE SYNCHRONIZATION

### 10.1 Updating Template in Project

```bash
# In project directory (e.g., hyg-web-dev/)

# Pull latest template changes
cd template
git pull origin main
cd ..

# Commit the submodule update
git add template
git commit -m "Update template submodule to latest"
git push

# Restart dev to see changes
docker-compose -f docker-compose.dev.yml restart
```

### 10.2 Making Template Changes

```bash
# In template directory
cd template

# Make changes to components or CSS
# Edit src/_includes/header.liquid
# Edit src/assets/css/global.css

# Commit and push
git add .
git commit -m "Update header mobile menu"
git push origin main

# In each project that uses the template:
cd ../hyg-web-dev
cd template && git pull && cd ..
git add template
git commit -m "Sync template"
```

### 10.3 Creating a New Project

```bash
# 1. Create project directory
mkdir new-hotel-website
cd new-hotel-website

# 2. Initialize git
git init

# 3. Add template as submodule
git submodule add https://github.com/Zunkireelabs/zunkiree-hospitality-website-engine.git template

# 4. Create source structure
mkdir -p src/_data src/assets/media

# 5. Create symlinks
cd src
ln -s ../template/src/_includes _includes
cd assets
ln -s ../../template/src/assets/css css
cd ../..

# 6. Copy config files
cp template/.eleventy.js.example .eleventy.js
cp template/package.json.example package.json
cp template/docker-compose.dev.yml.example docker-compose.dev.yml
cp template/docker-compose.yml.example docker-compose.yml
cp template/nginx.conf.example nginx.conf
cp template/deploy.sh.example deploy.sh

# 7. Create data files
# Edit src/_data/site.json
# Edit src/_data/homepage.json

# 8. Create homepage
# Edit src/index.liquid

# 9. Install and run
npm install
docker-compose -f docker-compose.dev.yml up -d
```

---

## 11. TRAEFIK INTEGRATION

### 11.1 Network Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         INTERNET                                 │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    TRAEFIK REVERSE PROXY                         │
│                                                                  │
│  Ports: 80 (HTTP), 443 (HTTPS)                                  │
│  Network: hosting (Docker network)                               │
│  SSL: Let's Encrypt auto-renewal                                │
│                                                                  │
│  Routing Rules:                                                  │
│  ├── hyg.zunkireelabs.com      → hyg-dev:8080 (dev)             │
│  ├── hotelyatrigorkha.com      → hyg-prod:80 (prod)             │
│  ├── dev.laplandfamille.co.uk  → lapland-dev:8080               │
│  ├── laplandfamille.co.uk      → lapland-prod:80                │
│  └── [other projects...]                                        │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              │ hosting network
                              ▼
┌─────────────────────────────────────────────────────────────────┐
│                    DOCKER CONTAINERS                             │
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐                    │
│  │    hyg-dev       │  │    hyg-prod      │                    │
│  │    (Node.js)     │  │    (Nginx)       │                    │
│  │    Port 8080     │  │    Port 80       │                    │
│  └──────────────────┘  └──────────────────┘                    │
│                                                                  │
│  ┌──────────────────┐  ┌──────────────────┐                    │
│  │   lapland-dev    │  │   lapland-prod   │                    │
│  │    (Node.js)     │  │    (Nginx)       │                    │
│  │    Port 8080     │  │    Port 80       │                    │
│  └──────────────────┘  └──────────────────┘                    │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

### 11.2 Required Traefik Labels

```yaml
labels:
  # Enable Traefik routing
  - "traefik.enable=true"

  # Specify Docker network
  - "traefik.docker.network=hosting"

  # HTTP router (redirects to HTTPS)
  - "traefik.http.routers.{service-name}.rule=Host(`domain.com`)"
  - "traefik.http.routers.{service-name}.entrypoints=web"
  - "traefik.http.routers.{service-name}.middlewares=redirect-to-https"

  # HTTPS router
  - "traefik.http.routers.{service-name}-secure.rule=Host(`domain.com`)"
  - "traefik.http.routers.{service-name}-secure.entrypoints=websecure"
  - "traefik.http.routers.{service-name}-secure.tls.certresolver=letsencrypt"

  # Load balancer port
  - "traefik.http.services.{service-name}.loadbalancer.server.port={port}"
```

---

## 12. LIQUID TEMPLATE REFERENCE

### 12.1 Basic Syntax

```liquid
{# Output variable #}
{{ variable }}
{{ site.name }}
{{ homepage.hero.headline }}

{# Conditional #}
{% if condition %}
  ...
{% elsif other_condition %}
  ...
{% else %}
  ...
{% endif %}

{# Loop #}
{% for item in collection %}
  {{ item.name }}
{% endfor %}

{# Loop with limit #}
{% for item in items limit: 3 %}
  {{ item }}
{% endfor %}

{# Include component #}
{% include "header.liquid" %}
{% include "faq-section.liquid" %}

{# Assign variable #}
{% assign my_var = "value" %}

{# Filters #}
{{ "hello" | upcase }}        → HELLO
{{ "hello" | capitalize }}    → Hello
{{ array | size }}            → 3
{{ array | first }}           → first item
{{ string | truncate: 20 }}   → truncated...
```

### 12.2 Common Patterns in Components

```liquid
{# Conditional section rendering #}
{% if homepage.faq_section.enabled %}
  <section id="faq">
    {% for faq in homepage.faq_section.faqs %}
      <div class="faq-item">
        <h3>{{ faq.question }}</h3>
        <p>{{ faq.answer }}</p>
      </div>
    {% endfor %}
  </section>
{% endif %}

{# Default values #}
{{ hero.headline | default: "Welcome" }}

{# Nested loops #}
{% for category in nav %}
  {% if category.dropdown %}
    {% for item in category.dropdown.items %}
      <a href="{{ item.url }}">{{ item.text }}</a>
    {% endfor %}
  {% endif %}
{% endfor %}

{# Conditional classes #}
<div class="card {% if item.featured %}card--featured{% endif %}">
```

---

## 13. SCALABILITY & FUTURE PROJECTS

### 13.1 Adding a New Project

1. **Create repository** from template structure
2. **Add submodule** pointing to zunkire-hospitality-website-engine
3. **Create symlinks** for `_includes` and `css`
4. **Configure data files** (`site.json`, `homepage.json`)
5. **Add project media** to `src/assets/media/`
6. **Configure Docker** with unique container names and domains
7. **Deploy** using standard workflow

### 13.2 What's Shared vs Project-Specific

| Shared (Template) | Project-Specific |
|-------------------|------------------|
| 18 Liquid components | `site.json` content |
| 5 CSS files | `homepage.json` content |
| CSS variables/tokens | `media.json` videos |
| Layout structure | `/assets/media/` images |
| Responsive breakpoints | Domain configuration |
| Animation keyframes | Docker container names |
| Utility classes | Traefik routing rules |

### 13.3 Template Update Impact

When template is updated:
- **All projects** can pull the update
- **CSS changes** affect all sites uniformly
- **Component fixes** propagate everywhere
- **Projects control** when to pull updates
- **No breaking changes** without coordination

---

## 14. TECHNICAL SPECIFICATIONS

### 14.1 Version Matrix

| Technology | Version | Notes |
|------------|---------|-------|
| Eleventy | 3.1.2 | Static site generator |
| Node.js | 18.x | Development runtime |
| Nginx | Alpine | Production server |
| Traefik | 2.x | Reverse proxy |
| Docker | 24.x+ | Containerization |
| Git | 2.x | Version control |

### 14.2 File Statistics

| Category | Count | Lines |
|----------|-------|-------|
| Liquid components | 18 | ~6,500 |
| CSS files | 5 | 2,196 |
| Total template code | - | ~8,700 |
| JSON data structures | 3 | Variable |

### 14.3 Performance Characteristics

| Metric | Value |
|--------|-------|
| Build time (full) | ~2-5 seconds |
| Rebuild time (incremental) | ~50ms |
| HTML output per page | ~50-150KB |
| CSS total | ~50KB (combined) |
| First Contentful Paint | <1.5s target |

---

## 15. TROUBLESHOOTING GUIDE

### 15.1 Common Issues

| Issue | Cause | Solution |
|-------|-------|----------|
| 404 via Traefik | Server bound to localhost | Set `host: '0.0.0.0'` in .eleventy.js |
| Components not found | Broken symlink | Recreate: `ln -s ../template/src/_includes _includes` |
| CSS not loading | Missing symlink | Recreate: `ln -s ../../template/src/assets/css css` |
| Template out of date | Submodule not updated | `cd template && git pull` |
| Build fails | Missing data | Check all required JSON fields |

### 15.2 Debug Commands

```bash
# Check Eleventy configuration
npm run debug

# View container logs
docker-compose -f docker-compose.dev.yml logs -f

# Check symlinks
ls -la src/_includes
ls -la src/assets/css

# Verify submodule status
git submodule status

# Test Nginx config
docker exec hyg-prod nginx -t
```

---

## 16. SUMMARY

This hospitality website engine provides:

- **18 production-ready components** for hotels/travel sites
- **Consistent design system** via CSS tokens
- **Data-driven architecture** separating content from code
- **Git submodule integration** for version control
- **Docker containerization** for consistent environments
- **Traefik routing** for multi-site hosting
- **Let's Encrypt SSL** for security
- **Permission-protected production** to prevent accidents
- **Hot reload development** for fast iteration
- **Scalable to unlimited projects** with shared maintenance

The architecture enables rapid hospitality website creation while maintaining consistent quality and centralized maintenance.

---

## 17. API EXAMPLES: COMPONENT USAGE

This section provides copy-paste examples for using each component in your pages.

### 17.1 Using Components in Pages

#### Basic Page Structure
```liquid
---
layout: layouts/base.liquid
title: Page Title
description: Page description for SEO
---

{# Include components in order they should appear #}
{% include "hero-homepage.liquid" %}
{% include "trust-strip.liquid" %}
{% include "experiences-section.liquid" %}
{% include "faq-section.liquid" %}
{% include "conversion-section.liquid" %}
```

### 17.2 Header Component

**Include:**
```liquid
{% include "header.liquid" %}
```

**Required in site.json:**
```json
{
  "name": "Hotel Name",
  "logo": "/assets/media/logo/logo.png",
  "nav": [
    { "text": "Home", "url": "/" },
    { "text": "Rooms", "url": "/rooms" },
    {
      "text": "Services",
      "dropdown": {
        "intro": { "title": "Our Services", "description": "What we offer" },
        "items": [
          { "text": "Restaurant", "url": "/services/restaurant", "icon": "utensils" },
          { "text": "Spa", "url": "/services/spa", "icon": "spa" }
        ]
      }
    }
  ],
  "cta": {
    "primary": { "text": "Book Now", "url": "/booking" },
    "secondary": { "text": "Call Us", "url": "tel:+1234567890" }
  }
}
```

### 17.3 Hero Homepage Component

**Include:**
```liquid
{% include "hero-homepage.liquid" %}
```

**Required in homepage.json:**
```json
{
  "hero": {
    "headline": "Welcome to Paradise",
    "tagline": "Your perfect getaway awaits",
    "images": [
      { "url": "/assets/media/hero/slide1.jpg", "alt": "Beach view" },
      { "url": "/assets/media/hero/slide2.jpg", "alt": "Pool area" }
    ],
    "cta_primary": { "text": "Book Now", "url": "/booking" },
    "cta_secondary": { "text": "View Rooms", "url": "/rooms" },
    "search_bar": {
      "enabled": true,
      "placeholder": "Where would you like to go?",
      "fields": ["check_in", "check_out", "guests"]
    }
  }
}
```

### 17.4 Trust Strip Component

**Include:**
```liquid
{% include "trust-strip.liquid" %}
```

**Required in homepage.json:**
```json
{
  "trust_badges": [
    { "icon": "star", "title": "4.9 Rating", "description": "1000+ Reviews" },
    { "icon": "award", "title": "Award Winner", "description": "Best Hotel 2024" },
    { "icon": "clock", "title": "24/7 Support", "description": "Always Available" },
    { "icon": "shield", "title": "Safe & Secure", "description": "Verified Payments" }
  ]
}
```

### 17.5 Experiences Section Component

**Include:**
```liquid
{% include "experiences-section.liquid" %}
```

**Required in homepage.json:**
```json
{
  "experiences_section": {
    "headline": "Unforgettable Experiences",
    "intro": "Discover what makes us special",
    "experiences": [
      {
        "title": "Gourmet Dining",
        "description": "World-class cuisine prepared by expert chefs",
        "icon": "utensils",
        "image": "/assets/media/experiences/dining.jpg",
        "url": "/experiences/dining"
      },
      {
        "title": "Spa & Wellness",
        "description": "Rejuvenate your body and mind",
        "icon": "spa",
        "image": "/assets/media/experiences/spa.jpg",
        "url": "/experiences/spa"
      }
    ]
  }
}
```

### 17.6 FAQ Section Component

**Include:**
```liquid
{% include "faq-section.liquid" %}
```

**Required in homepage.json:**
```json
{
  "faq_section": {
    "headline": "Frequently Asked Questions",
    "tagline": "Everything you need to know",
    "categories": ["booking", "amenities", "policies"],
    "faqs": [
      {
        "question": "What time is check-in and check-out?",
        "answer": "Check-in is at 3:00 PM and check-out is at 11:00 AM.",
        "category": "booking"
      },
      {
        "question": "Is breakfast included?",
        "answer": "Yes, a full breakfast buffet is included with all room bookings.",
        "category": "amenities"
      }
    ]
  }
}
```

### 17.7 Inquiry Form Component

**Include:**
```liquid
{% include "inquiry-form-section.liquid" %}
```

**Required in homepage.json:**
```json
{
  "inquiry_section": {
    "headline": "Book Your Stay",
    "intro": "Fill out this quick form and we'll get back to you",
    "form_action": "/api/inquiry",
    "steps": [
      {
        "id": "trip_length",
        "question": "How many nights would you like to stay?",
        "options": [
          { "value": "1-2", "label": "1-2 Nights", "description": "Quick stopover" },
          { "value": "3-4", "label": "3-4 Nights", "description": "Perfect for exploring" }
        ]
      },
      {
        "id": "contact",
        "question": "Tell us about your group",
        "fields": [
          { "name": "name", "type": "text", "label": "Your Name", "required": true },
          { "name": "email", "type": "email", "label": "Email Address", "required": true }
        ]
      }
    ],
    "buttons": {
      "back": "Back",
      "next": "Continue",
      "submit": "Send Inquiry"
    }
  }
}
```

### 17.8 Social Proof Component

**Include:**
```liquid
{% include "social-proof-section.liquid" %}
```

**Required in homepage.json:**
```json
{
  "social_proof_section": {
    "headline": "Guest Reviews",
    "intro": "See what our guests are saying",
    "testimonials": [
      {
        "quote": "An absolutely magical experience.",
        "author": "Sarah Johnson",
        "location": "New York, USA",
        "rating": 5,
        "image": "/assets/media/testimonials/sarah.jpg"
      }
    ]
  }
}
```

### 17.9 Conversion Section Component

**Include:**
```liquid
{% include "conversion-section.liquid" %}
```

**Required in homepage.json:**
```json
{
  "conversion_section": {
    "headline": "Ready for Your Adventure?",
    "intro": "Book now and save 15% on your first stay",
    "cta": { "text": "Book Your Stay", "url": "/booking" },
    "background_image": "/assets/media/cta-bg.jpg",
    "reassurance": [
      "Free cancellation",
      "Best price guarantee",
      "24/7 customer support"
    ]
  }
}
```

### 17.10 Complete Homepage Example

```liquid
---
layout: layouts/base.liquid
---

{# Hero with image slider #}
{% include "hero-homepage.liquid" %}

{# Trust indicators #}
{% include "trust-strip.liquid" %}

{# Services/experiences grid #}
{% include "experiences-section.liquid" %}

{# Why choose us #}
{% include "why-section.liquid" %}

{# Location information #}
{% include "location-section.liquid" %}

{# Video showcase #}
{% include "video-showcase-section.liquid" %}

{# Guest testimonials #}
{% include "social-proof-section.liquid" %}

{# Contact form #}
{% include "inquiry-form-section.liquid" %}

{# FAQ accordion #}
{% include "faq-section.liquid" %}

{# Final CTA #}
{% include "conversion-section.liquid" %}
```

---

## 18. VISUAL DIAGRAMS

### 18.1 Complete System Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                              INTERNET                                        │
│                                                                              │
│    Users access: https://hotelyatrigorkha.com                               │
│                  https://laplandfamille.co.uk                               │
│                  https://[future-hotel].com                                  │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ DNS Resolution
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                         TRAEFIK REVERSE PROXY                                │
│                                                                              │
│  ┌─────────────────────────────────────────────────────────────────────┐   │
│  │  Responsibilities:                                                    │   │
│  │  - Listen on ports 80 (HTTP) and 443 (HTTPS)                         │   │
│  │  - Route traffic based on domain name                                │   │
│  │  - Auto-generate SSL certificates via Let's Encrypt                  │   │
│  │  - Redirect HTTP → HTTPS                                             │   │
│  │  - Load balance across containers                                    │   │
│  └─────────────────────────────────────────────────────────────────────┘   │
│                                                                              │
│  Routing Table:                                                              │
│  ┌────────────────────────────┬──────────────────────┬───────────────┐     │
│  │ Domain                      │ Container            │ Port          │     │
│  ├────────────────────────────┼──────────────────────┼───────────────┤     │
│  │ hyg.zunkireelabs.com       │ hyg-dev              │ 8080 (Node)   │     │
│  │ hotelyatrigorkha.com       │ hyg-prod             │ 80 (Nginx)    │     │
│  │ dev.laplandfamille.co.uk   │ lapland-dev          │ 8080 (Node)   │     │
│  │ laplandfamille.co.uk       │ lapland-prod         │ 80 (Nginx)    │     │
│  └────────────────────────────┴──────────────────────┴───────────────┘     │
└─────────────────────────────────────────────────────────────────────────────┘
                                    │
                                    │ Docker Network: hosting
                                    ▼
┌─────────────────────────────────────────────────────────────────────────────┐
│                           DOCKER CONTAINERS                                  │
│                                                                              │
│  ┌─────────────────────────────┐    ┌─────────────────────────────┐        │
│  │     DEVELOPMENT CONTAINERS   │    │    PRODUCTION CONTAINERS    │        │
│  │                              │    │                              │        │
│  │  ┌────────────────────────┐ │    │  ┌────────────────────────┐ │        │
│  │  │  hyg-dev               │ │    │  │  hyg-prod              │ │        │
│  │  │  Node.js 18            │ │    │  │  Nginx Alpine          │ │        │
│  │  │  Eleventy dev server   │ │    │  │  Static files only     │ │        │
│  │  │  Hot reload enabled    │ │    │  │  Gzip + caching        │ │        │
│  │  └────────────────────────┘ │    │  └────────────────────────┘ │        │
│  │                              │    │                              │        │
│  │  ┌────────────────────────┐ │    │  ┌────────────────────────┐ │        │
│  │  │  lapland-dev           │ │    │  │  lapland-prod          │ │        │
│  │  │  Node.js 18            │ │    │  │  Nginx Alpine          │ │        │
│  │  │  Eleventy dev server   │ │    │  │  Static files only     │ │        │
│  │  │  Hot reload enabled    │ │    │  │  Gzip + caching        │ │        │
│  │  └────────────────────────┘ │    │  └────────────────────────┘ │        │
│  │                              │    │                              │        │
│  └─────────────────────────────┘    └─────────────────────────────┘        │
└─────────────────────────────────────────────────────────────────────────────┘
```

### 18.2 Build Process Flow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                           BUILD PROCESS FLOW                                 │
└─────────────────────────────────────────────────────────────────────────────┘

                    SOURCE FILES                      BUILD OUTPUT
                    ────────────                      ────────────

┌──────────────┐                              ┌──────────────────────┐
│ src/_data/   │                              │                      │
│  site.json   │──┐                           │                      │
│  homepage.   │  │                           │                      │
│  media.json  │  │                           │                      │
└──────────────┘  │                           │                      │
                  │    ┌─────────────────┐    │   _site-dev/         │
┌──────────────┐  │    │                 │    │   or                 │
│ src/         │  ├───►│    ELEVENTY     │───►│   _site-prod/        │
│  index.      │  │    │                 │    │                      │
│  liquid      │  │    │  (Static Site   │    │   ├── index.html     │
│  about/      │  │    │   Generator)    │    │   ├── about/         │
│  contact/    │  │    │                 │    │   │   └── index.html │
└──────────────┘  │    └─────────────────┘    │   ├── contact/       │
                  │            │              │   │   └── index.html │
┌──────────────┐  │            │              │   └── assets/        │
│ template/    │  │            │              │       ├── css/       │
│ _includes/   │──┘            │              │       └── media/     │
│  header.     │               │              │                      │
│  footer.     │               ▼              └──────────────────────┘
│  faq-section │        Processes in
│  etc...      │        ~50ms per file
└──────────────┘
```

### 18.3 Data Flow: JSON to HTML

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                       DATA FLOW: JSON → HTML                                 │
└─────────────────────────────────────────────────────────────────────────────┘

STEP 1: Data File (src/_data/homepage.json)
─────────────────────────────────────────────

{
  "hero": {
    "headline": "Welcome to Paradise",     ◄─── This text
    "tagline": "Your perfect getaway"
  }
}

                              │
                              │ Eleventy reads JSON
                              ▼

STEP 2: Template File (template/_includes/hero-homepage.liquid)
───────────────────────────────────────────────────────────────

<section class="hero">
  <h1>{{ homepage.hero.headline }}</h1>    ◄─── References data
  <p>{{ homepage.hero.tagline }}</p>
</section>

                              │
                              │ Eleventy merges data + template
                              ▼

STEP 3: Output File (_site/index.html)
──────────────────────────────────────

<section class="hero">
  <h1>Welcome to Paradise</h1>            ◄─── Becomes this HTML
  <p>Your perfect getaway</p>
</section>

                              │
                              │ Browser renders
                              ▼

STEP 4: User Sees
─────────────────

┌─────────────────────────────────────────┐
│                                         │
│         Welcome to Paradise             │
│         Your perfect getaway            │
│                                         │
│            [Book Now]                   │
│                                         │
└─────────────────────────────────────────┘
```

### 18.4 Git Workflow: Template Updates

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    GIT WORKFLOW: TEMPLATE UPDATES                            │
└─────────────────────────────────────────────────────────────────────────────┘

SCENARIO: Fix a bug in the header component

1. DEVELOPER EDITS TEMPLATE
   ─────────────────────────

   $ cd zunkire-hospitality-website-engine/
   $ vim src/_includes/header.liquid        # Fix the bug
   $ git add . && git commit -m "Fix header mobile menu"
   $ git push origin main

   ┌──────────────────────────┐
   │  Template Repo (GitHub)  │
   │  ────────────────────── │
   │  main: abc123 → def456  │  ◄─── New commit pushed
   └──────────────────────────┘


2. PROJECT PULLS UPDATE
   ─────────────────────

   $ cd hyg-web-dev/
   $ cd template/
   $ git pull origin main                   # Get latest
   $ cd ..
   $ git add template
   $ git commit -m "Update template to fix header bug"
   $ git push

   ┌──────────────────────────┐
   │  HYG Repo (GitHub)       │
   │  ────────────────────── │
   │  template @ def456      │  ◄─── Submodule now points to fix
   └──────────────────────────┘


3. OTHER PROJECTS ALSO UPDATE
   ──────────────────────────

   $ cd laplandfamille/
   $ cd template/ && git pull && cd ..
   $ git add template && git commit -m "Sync template"
   $ git push

   ┌──────────────────────────┐
   │  Lapland Repo (GitHub)   │
   │  ────────────────────── │
   │  template @ def456      │  ◄─── Also has the fix now
   └──────────────────────────┘


RESULT: Both projects now have the header fix
```

### 18.5 Multi-Project Architecture

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      MULTI-PROJECT ARCHITECTURE                              │
└─────────────────────────────────────────────────────────────────────────────┘

                         SHARED TEMPLATE (GitHub)
                         ─────────────────────────

                    ┌─────────────────────────────────┐
                    │  zunkire-hospitality-website-   │
                    │  engine                         │
                    │                                 │
                    │  ├── src/_includes/             │
                    │  │   └── 18 components          │
                    │  ├── src/assets/css/            │
                    │  │   └── 5 CSS files            │
                    │  └── docs/                      │
                    └─────────────────────────────────┘
                                    │
                                    │ git submodule
                    ┌───────────────┼───────────────┐
                    │               │               │
                    ▼               ▼               ▼
           ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
           │  hyg-web-dev │ │  lapland-    │ │  future-     │
           │              │ │  famille     │ │  hotel       │
           ├──────────────┤ ├──────────────┤ ├──────────────┤
           │ template/  ──┼─┼── (submodule │ │  (submodule) │
           │ src/_data/   │ │    points to │ │              │
           │ src/assets/  │ │    same repo)│ │              │
           │  media/      │ │              │ │              │
           │ docker-      │ │ [unique      │ │ [unique      │
           │  compose     │ │  content]    │ │  content]    │
           └──────────────┘ └──────────────┘ └──────────────┘
                    │               │               │
                    │               │               │
                    ▼               ▼               ▼
           ┌──────────────┐ ┌──────────────┐ ┌──────────────┐
           │ hotelyatri   │ │ lapland      │ │ future       │
           │ gorkha.com   │ │ famille.co.uk│ │ hotel.com    │
           │              │ │              │ │              │
           │ SAME DESIGN  │ │ SAME DESIGN  │ │ SAME DESIGN  │
           │ UNIQUE DATA  │ │ UNIQUE DATA  │ │ UNIQUE DATA  │
           └──────────────┘ └──────────────┘ └──────────────┘


BENEFITS:
─────────

- Fix a bug in template → All projects get the fix
- Add a component → All projects can use it
- Update CSS → All projects look consistent
- Each project controls when to pull updates
- Projects can have custom pages/components too
```

---

## 19. DEVELOPER ONBOARDING GUIDE

### 19.1 Prerequisites

Before starting, ensure you have:

| Requirement | Version | Check Command |
|-------------|---------|---------------|
| Git | 2.x+ | `git --version` |
| Node.js | 18.x+ | `node --version` |
| npm | 9.x+ | `npm --version` |
| Docker | 24.x+ | `docker --version` |
| Docker Compose | 2.x+ | `docker compose version` |
| SSH access | - | `ssh devuser@server` |

### 19.2 Day 1: Environment Setup

**Step 1: Clone the Project**
```bash
# Clone with submodules
git clone --recurse-submodules https://github.com/Zunkireelabs/hyg-web-dev.git

# If already cloned without submodules
cd hyg-web-dev
git submodule update --init --recursive
```

**Step 2: Verify Symlinks**
```bash
# Check _includes symlink
ls -la src/_includes
# Should show: _includes -> ../template/src/_includes

# Check css symlink
ls -la src/assets/css
# Should show: css -> ../../template/src/assets/css

# If broken, recreate:
cd src && ln -sf ../template/src/_includes _includes
cd assets && ln -sf ../../template/src/assets/css css
```

**Step 3: Install Dependencies**
```bash
npm install
```

**Step 4: Start Development Server**
```bash
# Option A: Docker (recommended for team work)
docker-compose -f docker-compose.dev.yml up -d
docker-compose -f docker-compose.dev.yml logs -f

# Option B: Local (quick testing)
npm start
```

**Step 5: Verify Everything Works**
```bash
# Check site loads
curl -I http://localhost:8080  # Local
# or
curl -I https://hyg.zunkireelabs.com  # Docker

# Check Eleventy is watching
# Edit src/_data/homepage.json, save, and verify browser refreshes
```

### 19.3 Day 1: Key Files to Understand

| Priority | File | Purpose | Read First? |
|----------|------|---------|-------------|
| 1 | `CLAUDE.md` | Complete project docs | Yes |
| 2 | `src/_data/site.json` | Site configuration | Yes |
| 3 | `src/_data/homepage.json` | Homepage content | Yes |
| 4 | `src/index.liquid` | Homepage structure | Yes |
| 5 | `.eleventy.js` | Build configuration | Skim |
| 6 | `template/src/_includes/` | Available components | Browse |
| 7 | `template/src/assets/css/global.css` | CSS variables | Reference |

### 19.4 Common Tasks Reference

| Task | Command/File | Notes |
|------|--------------|-------|
| Start dev server | `docker-compose -f docker-compose.dev.yml up -d` | Use `-d` for detached |
| View dev logs | `docker-compose -f docker-compose.dev.yml logs -f` | `-f` follows output |
| Stop dev server | `docker-compose -f docker-compose.dev.yml down` | Stops containers |
| Update content | Edit `src/_data/*.json` | Auto-rebuilds |
| Add new page | Create `src/pagename/index.liquid` | Use existing as template |
| Update template | `cd template && git pull && cd ..` | Then commit submodule |
| Deploy to prod | `./deploy.sh` | Requires root password |
| Debug build | `npm run debug` | Shows detailed output |
| Check submodule | `git submodule status` | Shows commit hash |

### 19.5 Best Practices

**DO:**
- Always pull latest before starting work
- Test changes locally before pushing
- Write descriptive commit messages
- Keep JSON data well-formatted
- Use CSS variables for consistency
- Ask questions in team chat

**DON'T:**
- Edit template files without team discussion
- Deploy to production without testing
- Commit large binary files to git
- Hardcode text in templates
- Skip the submodule update after pulling

### 19.6 Onboarding Checklist

```
[ ] Day 1
  [ ] Clone repository with submodules
  [ ] Verify symlinks are working
  [ ] Install dependencies
  [ ] Start development server
  [ ] Verify site loads in browser
  [ ] Make a test edit to homepage.json
  [ ] See browser auto-refresh

[ ] Day 2
  [ ] Read CLAUDE.md completely
  [ ] Explore src/_data/ JSON files
  [ ] Open and read src/index.liquid
  [ ] Make first content change
  [ ] Create first commit and push

[ ] Day 3
  [ ] Browse all components in template/_includes/
  [ ] Trace data flow from JSON → Template → HTML
  [ ] Understand shared vs project-specific files
  [ ] Review CSS variables in global.css

[ ] Week 1
  [ ] Create a new page
  [ ] Add content to existing sections
  [ ] Update navigation
  [ ] Practice git workflow

[ ] Week 2
  [ ] Understand deployment process
  [ ] Run deploy.sh (with supervision)
  [ ] Update template submodule
  [ ] Add custom styling to a page
```

---

## 20. APPENDIX: QUICK REFERENCE CARDS

### 20.1 Command Cheat Sheet

```
╔══════════════════════════════════════════════════════════════════════════╗
║                        DEVELOPMENT COMMANDS                               ║
╠══════════════════════════════════════════════════════════════════════════╣
║ START DEV SERVER                                                          ║
║   docker-compose -f docker-compose.dev.yml up -d                         ║
║                                                                          ║
║ VIEW LOGS                                                                ║
║   docker-compose -f docker-compose.dev.yml logs -f                       ║
║                                                                          ║
║ STOP DEV SERVER                                                          ║
║   docker-compose -f docker-compose.dev.yml down                          ║
║                                                                          ║
║ RESTART DEV SERVER                                                        ║
║   docker-compose -f docker-compose.dev.yml restart                       ║
║                                                                          ║
║ LOCAL DEV (no Docker)                                                     ║
║   npm start                                                              ║
║                                                                          ║
║ BUILD ONLY                                                               ║
║   npm run build                                                          ║
║                                                                          ║
║ DEBUG BUILD                                                              ║
║   npm run debug                                                          ║
╚══════════════════════════════════════════════════════════════════════════╝

╔══════════════════════════════════════════════════════════════════════════╗
║                        DEPLOYMENT COMMANDS                                ║
╠══════════════════════════════════════════════════════════════════════════╣
║ DEPLOY TO PRODUCTION                                                      ║
║   ./deploy.sh                                                            ║
║                                                                          ║
║ VIEW PRODUCTION LOGS                                                      ║
║   docker-compose logs -f                                                 ║
║                                                                          ║
║ RESTART PRODUCTION                                                        ║
║   docker-compose restart                                                 ║
╚══════════════════════════════════════════════════════════════════════════╝

╔══════════════════════════════════════════════════════════════════════════╗
║                          GIT COMMANDS                                     ║
╠══════════════════════════════════════════════════════════════════════════╣
║ CLONE WITH SUBMODULES                                                     ║
║   git clone --recurse-submodules [url]                                   ║
║                                                                          ║
║ INIT SUBMODULES (if already cloned)                                       ║
║   git submodule update --init --recursive                                ║
║                                                                          ║
║ UPDATE TEMPLATE                                                           ║
║   cd template && git pull && cd ..                                       ║
║   git add template && git commit -m "Update template"                    ║
║                                                                          ║
║ CHECK SUBMODULE STATUS                                                    ║
║   git submodule status                                                   ║
╚══════════════════════════════════════════════════════════════════════════╝
```

### 20.2 File Location Reference

```
╔══════════════════════════════════════════════════════════════════════════╗
║                        WHERE TO FIND THINGS                               ║
╠══════════════════════════════════════════════════════════════════════════╣
║ CONTENT TO EDIT                    LOCATION                              ║
║ ─────────────────────────────────  ───────────────────────────────────── ║
║ Site name, nav, contact            src/_data/site.json                   ║
║ Homepage content                   src/_data/homepage.json               ║
║ Video URLs                         src/_data/media.json                  ║
║ Page structure                     src/[page]/index.liquid               ║
║ Images                             src/assets/media/                     ║
╠══════════════════════════════════════════════════════════════════════════╣
║ SHARED COMPONENTS                  LOCATION                              ║
║ ─────────────────────────────────  ───────────────────────────────────── ║
║ All components                     template/src/_includes/               ║
║ Layout wrapper                     template/src/_includes/layouts/       ║
║ Header                             template/src/_includes/header.liquid  ║
║ Footer                             template/src/_includes/footer.liquid  ║
╠══════════════════════════════════════════════════════════════════════════╣
║ STYLING                            LOCATION                              ║
║ ─────────────────────────────────  ───────────────────────────────────── ║
║ CSS Variables                      template/src/assets/css/global.css    ║
║ Layout styles                      template/src/assets/css/layout.css    ║
║ Component styles                   template/src/assets/css/components.css║
║ Typography                         template/src/assets/css/typography.css║
║ Utilities                          template/src/assets/css/utilities.css ║
╠══════════════════════════════════════════════════════════════════════════╣
║ CONFIGURATION                      LOCATION                              ║
║ ─────────────────────────────────  ───────────────────────────────────── ║
║ Eleventy config                    .eleventy.js                          ║
║ Docker dev config                  docker-compose.dev.yml                ║
║ Docker prod config                 docker-compose.yml                    ║
║ Nginx config                       nginx.conf                            ║
║ Deploy script                      deploy.sh                             ║
╚══════════════════════════════════════════════════════════════════════════╝
```

### 20.3 CSS Variables Quick Reference

```
╔══════════════════════════════════════════════════════════════════════════╗
║                         CSS VARIABLES                                     ║
╠══════════════════════════════════════════════════════════════════════════╣
║ COLORS                                                                    ║
║ ──────                                                                   ║
║ --color-primary          #871C53    Main brand color (plum)              ║
║ --color-primary-light    #A82B6A    Hover state                          ║
║ --color-primary-dark     #6B1642    Active state                         ║
║ --color-arctic           #1E3A5F    Deep blue                            ║
║ --color-ice              #E8F4F8    Light blue                           ║
║ --color-frost            #F5F9FB    Near-white                           ║
║ --color-success          #10B981    Green                                ║
║ --color-error            #EF4444    Red                                  ║
╠══════════════════════════════════════════════════════════════════════════╣
║ SPACING                                                                   ║
║ ───────                                                                  ║
║ --space-1                0.25rem    4px                                  ║
║ --space-2                0.5rem     8px                                  ║
║ --space-4                1rem       16px                                 ║
║ --space-6                1.5rem     24px                                 ║
║ --space-8                2rem       32px                                 ║
║ --space-12               3rem       48px                                 ║
║ --space-16               4rem       64px                                 ║
╠══════════════════════════════════════════════════════════════════════════╣
║ TYPOGRAPHY                                                                ║
║ ──────────                                                               ║
║ --text-sm                0.875rem   14px                                 ║
║ --text-base              1rem       16px                                 ║
║ --text-lg                1.125rem   18px                                 ║
║ --text-xl                1.25rem    20px                                 ║
║ --text-2xl               1.5rem     24px                                 ║
║ --text-3xl               1.875rem   30px                                 ║
║ --text-4xl               2.25rem    36px                                 ║
╠══════════════════════════════════════════════════════════════════════════╣
║ OTHER                                                                     ║
║ ─────                                                                    ║
║ --radius-sm              0.25rem    Small corners                        ║
║ --radius-md              0.5rem     Medium corners                       ║
║ --radius-lg              1rem       Large corners                        ║
║ --transition-fast        0.15s      Quick animations                     ║
║ --transition-normal      0.3s       Standard animations                  ║
║ --shadow-md              [box-shadow]  Medium elevation                  ║
╚══════════════════════════════════════════════════════════════════════════╝
```

---

**END OF DOCUMENT**

Document Version: 1.0
Last Updated: January 2026
Total Sections: 20
Estimated Read Time: 45-60 minutes

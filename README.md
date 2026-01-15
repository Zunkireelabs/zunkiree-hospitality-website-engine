# Zunkire Hospitality Website Engine

Shared Eleventy template for hospitality websites (hotels, travel).

## Usage

Add as git submodule to your project:
```bash
git submodule add ../zunkire-hospitality-website-engine template
```

Then create symlinks in your project's `src/` directory:
```bash
cd src
ln -sf ../template/src/_includes _includes
ln -sf ../template/src/assets/css assets/css
```

## Structure

```
src/
├── _includes/           ← Liquid components & layouts
│   ├── layouts/
│   │   └── base.liquid  ← Main HTML wrapper
│   ├── header.liquid
│   ├── footer.liquid
│   └── [section components]
└── assets/css/          ← Global stylesheets
    ├── global.css
    ├── layout.css
    ├── typography.css
    ├── components.css
    └── utilities.css
```

## Projects Using This Template

- HYG (Hotel Yatri Gorkha)
- Lapland Famille

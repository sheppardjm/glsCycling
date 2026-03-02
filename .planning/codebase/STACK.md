# Technology Stack

**Analysis Date:** 2026-03-01

## Languages

**Primary:**
- TOML - Hugo configuration files (`config.toml`)
- HTML - Template rendering in Hugo theme layouts (`themes/roxo-hugo/layouts/**/*.html`)
- SCSS - Stylesheet preprocessing (`themes/roxo-hugo/assets/scss/`)
- JavaScript - Client-side interactions (`themes/roxo-hugo/assets/js/`)
- YAML - Data files for content (`data/*.yml`)
- Markdown - Content pages (`content/**/*.md`)

## Runtime

**Environment:**
- Hugo Static Site Generator

**Build Tool:**
- Hugo CLI - Generates static HTML from content and templates

## Frameworks

**Core:**
- Hugo `0.74.3` (production) - Static site generator for site rendering

**CSS:**
- Bootstrap 4 - CSS framework for responsive layouts (`themes/roxo-hugo/assets/css/bootstrap.css`)
- Font Awesome - Icon library for social links (`public/font-awesome/`)

**JavaScript:**
- jQuery - DOM manipulation and event handling (`public/js/vendor.min.js` includes jQuery)
- Vanilla JavaScript - Form handling (`themes/roxo-hugo/assets/js/formhandler.js`)

**Theme:**
- Roxo Hugo (custom fork) - Creative agency theme for cycling team site
  - Location: `themes/roxo-hugo`
  - Repository: `https://github.com/sheppardjm/roxo-hugo.git`

## Key Dependencies

**Critical:**
- Bootstrap 4 - Responsive grid and component library, essential for layout
- Font Awesome - Icon set for social media links and UI elements
- jQuery - JavaScript utilities for interactivity

**Build/Assets:**
- Hugo Resources API - CSS/SCSS processing and minification (`resources.Get`, `resources.ToCSS`)

## Configuration

**Environment:**
- Hugo configuration file: `config.toml`
  - Base URL: `/` (relative)
  - Language code: `en-us`
  - Theme: `roxo-hugo`
  - Summary length: 25 characters

**Build:**
- Netlify configuration: `netlify.toml`
  - Build command: `hugo`
  - Publish directory: `public`
  - Hugo version: `0.74.3`
  - Environment: `HUGO_ENV = production`
  - Git info enabled: `HUGO_ENABLEGITINFO = true`

**CSS Processing:**
- SCSS compilation via Hugo's built-in TOML syntax
- Minification applied to output CSS and JavaScript

## Platform Requirements

**Development:**
- Hugo binary (v0.74.3 or compatible)
- Local file system for content editing
- Git for version control

**Production:**
- Netlify hosting platform
- Supports Hugo builds natively
- Static HTML/CSS/JS output only (no backend required)

**Deployment:**
- Netlify automatic builds from Git commits
- Environment variable configuration on Netlify dashboard
- No external runtime dependencies required

---

*Stack analysis: 2026-03-01*

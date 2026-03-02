# Coding Conventions

**Analysis Date:** 2026-03-01

## Overview

This is a Hugo static site generator project. The codebase uses configuration files (TOML), data files (YAML), and content files (Markdown) to define the website structure and content. There are minimal custom code files - primarily one custom shortcode and configuration.

## Naming Patterns

**Files:**
- Configuration: `config.toml` - Single site config file at root
- Data files: `data/*.yml` - Lowercase with `.yml` extension (e.g., `team.yml`, `clients.yml`, `counter.yml`)
- Content files: `content/[section]/[filename].md` - Markdown with descriptive names
  - Portfolio/blog files: Mixed casing with hyphens (e.g., `2026-results.md`, `Design-inspiration-the-best-projects-from-December.md`)
  - Index pages: `_index.md` for section landing pages
- Layout files: `layouts/shortcodes/gdocs.html` - Descriptive shortcode names
- Archetypes: `archetypes/default.md` - Template for new content

**Content Sections:**
- `portfolio/` - Results and race information
- `blog/` - Blog posts and articles
- `about/` - Sponsors and team information
- `contact/` - Contact page

**YAML Keys in Data Files:**
- snake_case with spaces (e.g., `client_logos`, `counter_item`, `serviceItem`)
- Inconsistent: Some use underscores (`client_logos`, `contact_form_title`), others use camelCase (`serviceItem`)
- Boolean values: `enable: true/false` for feature toggling

**Directory Structure:**
- Static assets: `static/`, `public/` (generated)
- Theme files: `themes/roxo-hugo/` (submodule)
- Content: `content/[section]/`

## Code Style

**Configuration (TOML):**
- String values wrapped in quotes: `title = "GLS Cycling | Cycling Race Team..."`
- Nested sections with brackets: `[params.banner]`, `[params.footer.social]`
- Arrays using double brackets: `[[menu.nav]]`, `[[params.footer.social.link]]`
- No trailing commas

**Data Files (YAML):**
- Key-value pairs with colons and spaces: `enable: true`
- List items with leading dash: `- image: path/to/image.jpg`
- Nested structures indented with 2-4 spaces
- String values sometimes wrapped in quotes, sometimes not: `thumb: "images/clients/..."` vs `enable: true`
- Comments not observed in data files

**Markdown (Content):**
- Front matter: YAML between triple dashes `---`
- Consistent front matter keys:
  - `title` - Page/post title
  - `date` - ISO 8601 datetime with timezone
  - `type` - Content type (e.g., `portfolio`, `blog`)
  - `image` - Featured/thumb image path
  - `category` - Array of categories (uppercase, e.g., `["RESULTS"]`)
- Content sections separated by `### Heading` (H3)
- Blockquotes using `>` prefix
- Image links with alt text in square brackets

## Import Organization

Not applicable - this is a static site generator with no JavaScript imports or code modules. Hugo uses:
- Template includes: `{{ partial "path/to/partial" . }}`
- Shortcodes: `{{< shortcode param="value" >}}`
- Custom shortcodes in: `layouts/shortcodes/`

## Layout and Shortcodes

**Custom Shortcode Pattern** (`layouts/shortcodes/gdocs.html`):
```html
<div class="row">
  <div class="col-lg-12">
    <iframe src="{{ .Get "src" }}" frameborder="0" width="100%" height="1000" allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true"></iframe>
  </div>
</div>
```
- Uses `.Get "paramName"` to access shortcode parameters
- Wraps in Bootstrap row/col classes
- HTML5 attributes for cross-browser compatibility

**Shortcode Usage in Content**:
```
{{< gdocs src="https://docs.google.com/spreadsheets/d/..." >}}
```

## Data Organization

**Pattern for List Data** (`data/clients.yml`, `data/team.yml`):
```yaml
enable: true
title: "Display title"
[items_key]:
  - field1: "value1"
    field2: "value2"
    field3: "value3"
```

Keys follow pattern:
- `enable` - Feature flag (boolean)
- `title` - Display title
- `[noun_plural]` or descriptive array name (e.g., `client_logos`, `members`, `counter_item`)

**Counter Data Pattern** (`data/counter.yml`):
- Simple key-value pairs for stats
- `counter_item` array with `title` and `number` fields

## Error Handling

Not observed in this codebase - Hugo handles build errors, no application error handling code present.

## Comments and Documentation

**Documentation approach:**
- No inline comments observed in custom code (`gdocs.html`)
- Hugo template syntax is self-documenting
- Configuration parameters named descriptively (e.g., `primary_button`, `background_image`)

**Archetype Template** (`archetypes/default.md`):
- Uses Hugo template syntax: `{{ replace .Name "-" " " | title }}`
- Comment blocks not used
- Front matter fields shown as reference

## URL and Path Conventions

**Image Paths:**
- Relative from static root: `images/team/team-member-one.jpg`
- Subdirectories by type: `images/blog/`, `images/clients/`, `images/projects/`, `images/backgrounds/`
- Naming: `[noun]-[descriptor]-[number].jpg` (e.g., `client-logo-one.png`, `project-thumb-nine.jpg`)

**Page URLs:**
- Derived from file path and front matter date
- `portfolio/2026-results.md` → `/portfolio/2026-results/`
- Index pages: `portfolio/_index.md` → `/portfolio/`
- Section-based: `about/`, `blog/`, `contact/`, `portfolio/`

**External Links:**
- Social media: Full HTTPS URLs in footer config
- Sponsor links: HTTP or HTTPS, no protocol assumed

## HTML Structure Conventions

**Bootstrap Usage:**
- Row/column grid: `<div class="row"><div class="col-lg-12">`
- Icon library: Font Awesome (referenced as `ion-ios-*` classes in service data)

## Date and Time

**Date Format in Config:**
- `dateFormat = "26 FEB 1994"` - Uppercase month abbreviations, day without leading zero

**Datetime in Front Matter:**
- ISO 8601 with timezone: `2026-01-06T15:44:46+06:00`
- Used consistently across all content files

## Build and Deployment

**Hugo Configuration** (`netlify.toml`):
- Publish directory: `public/`
- Build command: `hugo`
- Hugo version pinned: `HUGO_VERSION = "0.74.3"`
- Production environment specified

## Consistency Notes

**Inconsistencies Observed:**
1. YAML key naming mixed between snake_case and camelCase (e.g., `client_logos` vs `serviceItem`)
2. Quote usage in YAML inconsistent (some values quoted, some not)
3. Blog file naming uses mixed case with capitals (e.g., `Design-inspiration-...` vs `the-10-biggest-...`)
4. Front matter date fields include timezone but no comments explain +06:00 significance

---

*Convention analysis: 2026-03-01*

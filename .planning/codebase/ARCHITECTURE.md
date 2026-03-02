# Architecture

**Analysis Date:** 2026-03-01

## Pattern Overview

**Overall:** Static Site Generator with Theme-based Templating

**Key Characteristics:**
- Hugo-based static site generation (site compiled to `/public/` directory)
- Layered separation between content (Markdown), data (YAML), templates (HTML/Templating), and theme
- Roxo Hugo theme provides pre-built component system for agency-style websites
- Configuration-driven approach using `config.toml` for site metadata and menu structure
- Content-centric architecture where pages are primarily defined through Markdown front matter

## Layers

**Content Layer:**
- Purpose: Define website pages, blog posts, and portfolio items through Markdown files
- Location: `/content/*/` directories (`about/`, `blog/`, `contact/`, `portfolio/`)
- Contains: Markdown files with YAML front matter defining title, date, categories, images, and metadata
- Depends on: Hugo templating system to render
- Used by: Theme templates to generate HTML pages

**Data Layer:**
- Purpose: Store structured, reusable content (team members, sponsors, statistics, testimonials)
- Location: `/data/` directory with YAML files
- Contains: `clients.yml`, `counter.yml`, `feature.yml`, `team.yml`, `testimonial.yml`, `service.yml`, `gallery.yml`, `contact.yml`
- Depends on: Theme partials for rendering
- Used by: Partials to display dynamic content without duplicating Markdown files

**Template Layer:**
- Purpose: Render content and data into HTML using Hugo's templating engine
- Location:
  - Theme layouts: `/themes/roxo-hugo/layouts/`
  - Theme partials: `/themes/roxo-hugo/layouts/partials/`
  - Project-level shortcodes: `/layouts/shortcodes/`
- Contains: HTML with Go templating syntax, range loops for data iteration, conditional rendering
- Depends on: Content files (via `.` context), Data files (via `.Site.Data.*`), Site configuration (via `.Site.Params.*`)
- Used by: Hugo compilation process to generate static HTML

**Theme Layer:**
- Purpose: Provide reusable component system and styling for the entire site
- Location: `/themes/roxo-hugo/`
- Contains: Layouts, partials, assets (CSS/SCSS/JS), images, static files
- Depends on: Bootstrap 4 framework for responsive grid
- Used by: Main site to inherit templates and styling without duplication

**Configuration Layer:**
- Purpose: Define site-wide settings, navigation menus, and theme parameters
- Location: `/config.toml`
- Contains: baseURL, title, theme selection, menu items with weights, site parameters for banner/portfolio/CTA sections
- Depends on: None
- Used by: All layers to access site metadata and parameters

## Data Flow

**Page Generation Flow:**

1. Hugo reads `/config.toml` to initialize site configuration
2. Content files in `/content/*/` are parsed (YAML front matter + Markdown body)
3. Data files in `/data/` are loaded as `.Site.Data.*` for global access
4. Template resolution:
   - Specific content type template selected (e.g., `portfolio/list.html`)
   - Falls back to default templates in theme
5. Template renders:
   - Accesses page data from front matter (`.Title`, `.Params.*`)
   - Accesses global data from YAML files (`.Site.Data.clients`, `.Site.Data.testimonial`)
   - Accesses site config (`.Site.Params.banner`, `.Site.Menus.nav`)
   - Includes partials for reusable sections (header, footer, hero, etc.)
6. Static HTML written to `/public/` directory

**Example - Portfolio Results Page:**

1. `/content/portfolio/2026-results.md` contains title and shortcode reference
2. Shortcode `/layouts/shortcodes/gdocs.html` embeds an iframe for Google Docs
3. Template `themes/roxo-hugo/layouts/portfolio/single.html` renders the page
4. Portfolio list uses `themes/roxo-hugo/layouts/portfolio/list.html` which iterates through all portfolio items

**Example - Home Page Assembly:**

1. `themes/roxo-hugo/layouts/index.html` defines main block
2. Includes partials in sequence:
   - `partial "hero.html"` (uses `.Site.Params.banner` from config)
   - `partial "counter.html"` (uses `.Site.Data.counter`)
   - `partial "portfolio.html"` (embeds Strava feed)
   - `partial "testimonial.html"` (uses `.Site.Data.testimonial` for team members)
   - `partial "call-to-action.html"` (uses `.Site.Params.call_to_action` from config)

**Static Asset Flow:**

1. Images placed in `/static/images/` are served as-is
2. Theme CSS/JS in `/themes/roxo-hugo/assets/` are processed by Hugo pipeline
3. Resources cached in `/resources/_gen/` after first build

## Key Abstractions

**Partials (Reusable Components):**
- Purpose: Encapsulate UI sections that appear on multiple pages
- Examples: `header.html`, `footer.html`, `hero.html`, `portfolio.html`, `testimonial.html`, `clients.html`, `counter.html`, `call-to-action.html`, `team.html`
- Pattern: Each partial accepts site-wide data (`.Site.Params.*`, `.Site.Data.*`) and renders conditionally based on `enable` flags
- All partials located in `/themes/roxo-hugo/layouts/partials/`

**Content Types:**
- Purpose: Group related content with shared template structure
- Examples: `portfolio`, `blog`, `about`, `contact`
- Pattern: Directory in `/content/` contains Markdown files; Hugo automatically applies template at `/themes/roxo-hugo/layouts/[type]/list.html` or `single.html`
- Front matter defines metadata (title, date, image, category)

**Data Collections (YAML):**
- Purpose: Store repeating data structures without managing multiple Markdown files
- Examples:
  - `clients.yml`: Sponsor logos with links (list of objects with `thumb`, `alt`, `link`)
  - `testimonial.yml`: Team members with photos (list of objects with `thumb`, `name`, `company_position`)
  - `counter.yml`: Statistics display (list with `title`, `number`)
- Pattern: Accessed via `.Site.Data.[filename]` in templates; enables quick updates without touching templates

**Shortcodes (Custom Template Functions):**
- Purpose: Allow Markdown content to include complex HTML without escaping issues
- Examples: `gdocs` shortcode (`/layouts/shortcodes/gdocs.html`) embeds Google Docs iframe
- Pattern: Called in Markdown as `{{< shortcodename param="value" >}}`; renders custom HTML
- Used for: External embeds (Google Sheets for race results), iframes, and dynamic content

**Base Template (Template Inheritance):**
- Purpose: Provide consistent HTML structure across all pages
- Location: `/themes/roxo-hugo/layouts/_default/baseof.html`
- Contains: `<!DOCTYPE>`, `<html>`, `<head>` with header partial, `<body>` with footer partial
- Pattern: Other templates use `{{ define "main" }}...{{ end }}` blocks that replace the `{{ block "main" }}` in baseof
- Ensures: Consistent header navigation and footer across all pages

## Entry Points

**Home Page:**
- Location: `/themes/roxo-hugo/layouts/index.html`
- Triggers: When user visits root URL `/`
- Responsibilities:
  - Render hero banner section with tagline and action buttons
  - Display statistics counter section
  - Embed Strava activity feed (portfolio section)
  - Display team member carousel (testimonials)
  - Display call-to-action section
  - All populated from config params and data files

**Portfolio/Results:**
- Location: `/themes/roxo-hugo/layouts/portfolio/list.html` and `single.html`
- Triggers: User visits `/portfolio/` (list) or `/portfolio/[year]-results/` (individual)
- Responsibilities:
  - List view: Display grid of all race result pages with pagination
  - Single view: Render individual result page with Google Docs spreadsheet embed via shortcode
  - Data source: `/content/portfolio/` Markdown files containing title and shortcode

**About/Sponsors:**
- Location: `/themes/roxo-hugo/layouts/about/list.html`
- Triggers: User visits `/about/`
- Responsibilities:
  - Render team heading and mission statement (from `/content/about/_index.md`)
  - Display list of expertise sectors (from front matter `expertise_sectors`)
  - Include clients partial to display sponsor logos from `/data/clients.yml`
  - Include team member carousel from data
  - Include call-to-action

**Blog:**
- Location: `/themes/roxo-hugo/layouts/blog/list.html` and `single.html`
- Triggers: User visits `/blog/` or individual blog post URL
- Responsibilities:
  - List view: Display blog post cards with pagination
  - Single view: Render full blog post with content

**Contact:**
- Location: `/themes/roxo-hugo/layouts/contact/list.html`
- Triggers: User visits `/contact/`
- Responsibilities: Render contact form (with Formspree integration configuration in `config.toml`)

## Error Handling

**Strategy:** Configuration-driven feature flags with graceful fallback

**Patterns:**
- Enable/disable sections via YAML flags (e.g., `enable: true` in `clients.yml`, `counter.yml`, hero banner params)
- Conditional rendering in partials: `{{ if .enable }}...{{ end }}`
- Missing data: Templates use `{{ with }}...{{ end }}` to safely access optional front matter
- Broken links/missing images: No server-side validation; relies on Hugo's build-time verification
- No custom error pages detected beyond default `/themes/roxo-hugo/layouts/404.html`

## Cross-Cutting Concerns

**Logging:** Not applicable - static site generation; no runtime logging

**Validation:**
- Hugo build time: Templates validate required front matter fields for content types
- Client-side: Bootstrap validation classes applied to forms but no runtime validation logic found
- Front matter schema: Defined implicitly through archetype (`/archetypes/default.md`)

**Navigation/Routing:**
- Static routing based on content directory structure (e.g., `/content/about/` becomes `/about/`)
- Navigation menu defined in `config.toml` with relative URLs (`url: "about"`)
- Header partial iterates through `.Site.Menus.nav` to generate navbar
- Last menu item styled as button (visual treatment for CTA button)

**Image Handling:**
- All images stored in `/static/images/` with subdirectories: `backgrounds/`, `blog/`, `clients/`, `projects/`, `team/`, `testimonial/`
- References use `{{ .Image | absURL }}` filter to convert relative paths to absolute
- Responsive images via Bootstrap grid classes

**Social/External Integration:**
- Strava club feed embedded via iframe in portfolio section (hardcoded URL)
- Google Docs spreadsheet embedded via custom `gdocs` shortcode for race results
- Social links in footer fetched from `config.toml` params (Strava, Instagram, Twitter, Facebook)

---

*Architecture analysis: 2026-03-01*

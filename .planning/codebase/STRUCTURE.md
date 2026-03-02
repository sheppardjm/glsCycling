# Codebase Structure

**Analysis Date:** 2026-03-01

## Directory Layout

```
glsCycling/
├── .planning/                # Planning and documentation directory
│   └── codebase/            # Codebase analysis documents
├── archetypes/              # Hugo content templates/blueprints
├── config.toml              # Site configuration (menus, params, title, theme)
├── content/                 # Content source files (Markdown)
│   ├── about/              # About/Sponsors page
│   ├── blog/               # Blog posts
│   ├── contact/            # Contact page
│   └── portfolio/          # Portfolio/Results pages (race results by year)
├── data/                    # Structured data in YAML format
│   ├── clients.yml         # Sponsor logos and links
│   ├── contact.yml         # Contact form configuration
│   ├── counter.yml         # Statistics display (podiums, events)
│   ├── feature.yml         # Feature highlights
│   ├── gallery.yml         # Gallery configuration
│   ├── service.yml         # Service offerings
│   ├── team.yml            # Team metadata
│   └── testimonial.yml     # Team members with photos (used as testimonials)
├── layouts/                # Project-specific template overrides
│   └── shortcodes/         # Custom shortcodes for content
│       └── gdocs.html      # Google Docs iframe embed shortcode
├── public/                 # Generated static site (output directory)
│   ├── about/
│   ├── blog/
│   ├── contact/
│   ├── portfolio/
│   ├── css/
│   ├── font-awesome/
│   ├── images/
│   ├── js/
│   ├── scss/
│   └── index.html          # Homepage (generated)
├── resources/              # Build cache for processed assets
│   └── _gen/               # Hugo-generated resource cache
├── static/                 # Static assets served as-is
│   └── images/            # All image files
│       ├── backgrounds/   # Hero and section backgrounds
│       ├── blog/          # Blog post images
│       ├── clients/       # Sponsor logos
│       ├── projects/      # Portfolio/results page thumbnails
│       ├── team/          # Team member photos
│       ├── testimonial/   # Team member photos (alias for testimonials)
│       └── *.svg          # Icons (arrow-down, angle-down, to-top)
├── themes/                 # Hugo theme (Git submodule)
│   └── roxo-hugo/         # Roxo Hugo theme (agency template)
│       ├── archetypes/    # Theme default archetypes
│       ├── assets/        # SCSS/CSS/JS source files
│       │   ├── css/
│       │   ├── js/
│       │   └── scss/
│       ├── exampleSite/   # Theme demo/example configuration
│       ├── images/        # Theme documentation images
│       ├── layouts/       # Template files
│       │   ├── _default/  # Default templates
│       │   ├── about/     # About page template
│       │   ├── blog/      # Blog templates
│       │   ├── contact/   # Contact page template
│       │   ├── portfolio/ # Portfolio templates
│       │   ├── partials/  # Reusable component templates
│       │   ├── 404.html   # Not found page
│       │   └── index.html # Home page template
│       ├── static/        # Theme static assets
│       └── theme.toml     # Theme metadata
└── netlify.toml           # Netlify deployment configuration

```

## Directory Purposes

**`.planning/codebase/`:**
- Purpose: Store codebase analysis documents (ARCHITECTURE.md, STRUCTURE.md, CONVENTIONS.md, TESTING.md, CONCERNS.md)
- Contains: Markdown analysis files
- Key files: `STACK.md`

**`archetypes/`:**
- Purpose: Define blueprints for new content created via Hugo CLI (`hugo new`)
- Contains: `default.md` template with YAML front matter structure
- Key files: `default.md`

**`content/`:**
- Purpose: Store all site content in Markdown format with YAML front matter
- Contains: Markdown files organized by content type (about, blog, contact, portfolio)
- Key files: Multiple `_index.md` files define section landing pages; individual `.md` files are individual pages

**`content/about/_index.md`:**
- Purpose: Define Sponsors/About page with team expertise and mission statement
- Front matter contains: `title`, `heading`, `description`, `expertise_title`, `expertise_sectors` array

**`content/portfolio/`:**
- Purpose: Store race results pages, one per year
- Contains: Files named `YYYY-results.md` (e.g., `2026-results.md`, `2025-results.md`)
- Pattern: Each file uses `{{< gdocs >}}` shortcode to embed Google Sheets with race data
- Key files: `_index.md`, `2019-results.md` through `2026-results.md`

**`content/blog/`:**
- Purpose: Store blog post content
- Contains: Markdown files with blog posts (currently minimal - mostly template posts)
- Pattern: Files follow naming convention like `Design-inspiration-the-best-projects-from-December.md`

**`content/contact/_index.md`:**
- Purpose: Define contact page with form heading
- Contains: Minimal content; form handled by Formspree integration (via `config.toml`)

**`data/`:**
- Purpose: Store structured, reusable content in YAML format
- Contains: Multiple YAML files defining site-wide data structures
- Access pattern: Referenced in templates via `.Site.Data.[filename]` (e.g., `.Site.Data.clients`)

**`data/clients.yml`:**
- Purpose: Define sponsor logos, names, and links
- Structure: `enable: true/false`, `title`, `client_logos` array with `thumb` (image path), `alt` (description), `link` (URL)
- Used by: Clients partial `themes/roxo-hugo/layouts/partials/clients.html`

**`data/counter.yml`:**
- Purpose: Define statistics displayed on homepage (podiums, events completed)
- Structure: `enable: true/false`, `heading`, `counter_item` array with `title`, `number`
- Used by: Counter partial `themes/roxo-hugo/layouts/partials/counter.html`

**`data/testimonial.yml`:**
- Purpose: Define team members shown on homepage as carousel/slider
- Structure: `enable: true/false`, `title`, `subtitle`, `testimonial_item` array with `thumb` (photo), `name`, `company_position` (location)
- Used by: Testimonial partial `themes/roxo-hugo/layouts/partials/testimonial.html`

**`layouts/`:**
- Purpose: Project-level template overrides and custom shortcodes
- Contains: `shortcodes/` subdirectory for custom Hugo shortcodes
- Note: This directory can override theme templates, but currently minimal customization

**`layouts/shortcodes/gdocs.html`:**
- Purpose: Render iframe for embedding Google Docs/Sheets
- Parameters: `src` attribute passed as `{{ .Get "src" }}`
- Usage: In Markdown as `{{< gdocs src="https://..." >}}`
- Used in: Portfolio result pages to embed race result spreadsheets

**`public/`:**
- Purpose: Generated static website output (build artifact)
- Contains: Compiled HTML files, CSS, images, all ready for deployment
- Pattern: Directory structure mirrors `content/` structure (e.g., `content/about/` becomes `public/about/index.html`)
- Note: Regenerated on each Hugo build; should not be manually edited

**`resources/`:**
- Purpose: Hugo build cache for processed assets
- Contains: Compiled SCSS files and processed assets in `_gen/` subdirectory
- Lifecycle: Auto-generated; safe to delete (will be regenerated)

**`static/`:**
- Purpose: Static assets served directly without processing
- Contains: Images and other files that bypass Hugo's asset pipeline
- Difference from theme assets: Files here are part of this project, not the theme

**`static/images/`:**
- Purpose: Repository for all image files
- Contains: Organized into subdirectories by use case
- Subdirectories:
  - `backgrounds/`: Hero section and CTA section background images
  - `blog/`: Blog post-specific images
  - `clients/`: Sponsor/client logos (PNG files for each sponsor)
  - `projects/`: Portfolio result page thumbnails
  - `team/`: Team member portraits (distinct from testimonial)
  - `testimonial/`: Team member photos for carousel display (naming: `user-thumb-[number].jpg`)
- Key files: `logo.png`, `logo-footer.png`, `favicon.png`

**`themes/roxo-hugo/`:**
- Purpose: Git submodule containing the Roxo Hugo theme (agency template)
- Contains: Complete theme implementation with layouts, assets, documentation
- Note: This is a Git submodule; changes should be made in the submodule repo, not here

**`themes/roxo-hugo/layouts/`:**
- Purpose: Template files for rendering content
- Structure:
  - `_default/baseof.html`: Base template inherited by all pages
  - `_default/single.html`: Default template for single page content
  - `_default/list.html`: Default template for list pages
  - `index.html`: Home page template
  - `about/list.html`: About/sponsors page template
  - `blog/list.html`, `blog/single.html`: Blog templates
  - `portfolio/list.html`, `portfolio/single.html`: Portfolio/results templates
  - `contact/list.html`: Contact page template
  - `404.html`: Not found page
  - `partials/`: Reusable component templates

**`themes/roxo-hugo/layouts/partials/`:**
- Purpose: Reusable template components included in multiple page templates
- Contains: Individual component templates for sections of pages
- Key files:
  - `header.html`: Navigation navbar (iterates `.Site.Menus.nav`)
  - `footer.html`: Footer with links and social media
  - `hero.html`: Hero banner section (uses `.Site.Params.banner`)
  - `portfolio.html`: Strava feed embed section
  - `testimonial.html`: Team member carousel (uses `.Site.Data.testimonial`)
  - `clients.html`: Sponsor logos grid (uses `.Site.Data.clients`)
  - `counter.html`: Statistics section (uses `.Site.Data.counter`)
  - `call-to-action.html`: Call-to-action section
  - `team.html`: Team member listing
  - `head.html`: HTML head tags

**`themes/roxo-hugo/assets/`:**
- Purpose: Source files for stylesheets and scripts (processed by Hugo)
- Contains: SCSS, CSS, and JavaScript
- Subdirectories:
  - `scss/`: SCSS source files with component-specific partials
  - `css/`: Bootstrap CSS
  - `js/`: JavaScript files (vendor.js, script.js, formhandler.js)

**`config.toml`:**
- Purpose: Main site configuration file
- Contains:
  - `baseURL`: Root URL of the site
  - `languageCode`: Language setting
  - `title`: Site title
  - `theme`: Theme directory name (`roxo-hugo`)
  - `summarylength`: Excerpt length for summaries
  - `[menu]`: Navigation menu items with weights
  - `[params]`: Site-wide parameters (logo, banner, portfolio, CTA, footer, analytics, form)
- Pattern: All params prefixed with `params.` are accessible as `.Site.Params.*` in templates

**`netlify.toml`:**
- Purpose: Netlify deployment configuration
- Contains: Build command and publish directory settings

## Key File Locations

**Entry Points:**
- `config.toml`: Root configuration affecting all pages
- `themes/roxo-hugo/layouts/index.html`: Home page template
- `content/portfolio/_index.md`: Portfolio section landing page

**Configuration:**
- `config.toml`: Site title, theme, menus, parameters for all sections
- `data/*.yml`: Dynamic content configurations for different sections
- `netlify.toml`: Deployment settings

**Core Logic:**
- `themes/roxo-hugo/layouts/_default/baseof.html`: Base page structure
- `themes/roxo-hugo/layouts/*/list.html`: Content type list pages
- `themes/roxo-hugo/layouts/partials/*.html`: Reusable components
- `layouts/shortcodes/gdocs.html`: Custom content embedding

**Content:**
- `content/about/_index.md`: About/sponsors page content
- `content/portfolio/*.md`: Race results pages
- `content/blog/*.md`: Blog posts
- `content/contact/_index.md`: Contact page

**Images:**
- `static/images/`: All images used on site (logos, backgrounds, team photos)

## Naming Conventions

**Files:**
- Content files: kebab-case for multi-word filenames (e.g., `Design-inspiration-the-best-projects-from-December.md`)
- Results pages: `YYYY-results.md` pattern (e.g., `2026-results.md`)
- Data files: snake_case (e.g., `client_logos`, `counter_item`, `testimonial_item`)
- Template files: kebab-case (e.g., `_default`, `call-to-action.html`)
- SCSS files: kebab-case in components subdirectory (e.g., `_navigation.scss`, `_testimonial.scss`)

**Directories:**
- Content sections: lowercase, singular noun (about, blog, contact, portfolio)
- Theme structure: lowercase, semantic (layouts, partials, assets, static)
- Image categories: lowercase, plural (backgrounds, clients, projects, team, testimonial)
- Data files: lowercase snake_case (clients, counter, testimonial, etc.)

**Front Matter:**
- Consistent across content types: `title`, `date`, `type`, `image`, `category`, `draft`
- Section-specific: `heading`, `description`, `expertise_title`, `expertise_sectors` (in about)

## Where to Add New Code

**New Race Results Page:**
- Create file: `/content/portfolio/YYYY-results.md`
- Front matter pattern:
  ```
  ---
  title: "YYYY Race Results"
  date: 2026-01-06T15:44:46+06:00
  type: portfolio
  image: "images/projects/project-thumb-[number].jpg"
  category: ["RESULTS"]
  project_images:
  ---
  ```
- Body: Include `{{< gdocs src="https://docs.google.com/spreadsheets/..." >}}` with link to Google Sheets
- Images: Ensure thumbnail image exists at path specified

**New Sponsor/Client:**
- Edit: `/data/clients.yml`
- Add entry to `client_logos` array with `thumb`, `alt`, `link` properties
- Image: Add PNG file to `/static/images/clients/`

**New Team Member:**
- Edit: `/data/testimonial.yml`
- Add entry to `testimonial_item` array with `thumb`, `name`, `company_position`
- Image: Add JPG file to `/static/images/testimonial/user-thumb-[number].jpg` (follow naming convention)

**New Blog Post:**
- Create file: `/content/blog/Post-Title-Here.md`
- Front matter: Use archetype from `/archetypes/default.md` as template
- Images: Add to `/static/images/blog/`

**Updating Homepage Sections:**
- Banner hero: Edit `params.banner` in `config.toml`
- Counter stats: Edit `/data/counter.yml`
- Call-to-action: Edit `params.call_to_action` in `config.toml`
- Footer links: Edit `params.footer` in `config.toml`

**New Shortcode:**
- Create file: `/layouts/shortcodes/[shortcode-name].html`
- Pattern: Access parameters via `{{ .Get "param-name" }}`
- Usage in Markdown: `{{< shortcode-name param="value" >}}`

**Modifying Theme Components:**
- To override theme partial: Create corresponding file in `/layouts/` (e.g., `/layouts/partials/header.html`)
- To override theme template: Create file matching theme structure in `/layouts/`
- Note: Overrides in project `/layouts/` take precedence over theme

**New Stylesheet:**
- For theme customization: Add to `/themes/roxo-hugo/assets/scss/`
- Theme will process all SCSS files into compiled CSS

## Special Directories

**`public/`:**
- Purpose: Build output directory containing generated HTML
- Generated: Yes (created by Hugo build)
- Committed: No (gitignored)
- Action: Do not manually edit; regenerated on each `hugo` build command
- Deployment: Contents of this directory are deployed to web server

**`resources/_gen/`:**
- Purpose: Hugo cache directory for processed assets
- Generated: Yes (auto-created by Hugo)
- Committed: No (gitignored)
- Action: Do not manually edit; safe to delete (will regenerate)
- Contents: Compiled SCSS output, minified assets

**`themes/roxo-hugo/` (Git Submodule):**
- Purpose: Vendored Hugo theme
- Generated: No (manually maintained)
- Committed: Yes (as Git submodule reference)
- Action: Update via Git submodule commands, not direct edits
- Reference: Points to external repository (Git submodule tracking)

**`.planning/codebase/`:**
- Purpose: Documentation directory for GSD analysis
- Generated: No (created by GSD mapper agent)
- Committed: Yes
- Contents: ARCHITECTURE.md, STRUCTURE.md, CONVENTIONS.md, TESTING.md, CONCERNS.md

---

*Structure analysis: 2026-03-01*

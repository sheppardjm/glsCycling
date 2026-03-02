# External Integrations

**Analysis Date:** 2026-03-01

## APIs & External Services

**Form Handling:**
- Formspree or Netlify Forms - Contact form submission
  - Configuration location: `config.toml` (params.formspreeFormUrl, currently empty)
  - Fallback: `netlify` attribute on contact form indicates Netlify Forms support
  - Form template: `themes/roxo-hugo/layouts/contact/list.html`
  - Handler script: `themes/roxo-hugo/assets/js/formhandler.js`

**Social Media Integration:**
- Strava - Cycling activity tracking embeds
  - Club ID: 822732
  - Embedded widgets: `themes/roxo-hugo/layouts/partials/portfolio.html`
  - URLs: `https://www.strava.com/clubs/822732/latest-rides/...`
  - Display: Latest rides feed (shows last rides and activity summary)

## Data Storage

**Databases:**
- None - Static site only

**File Storage:**
- Local filesystem only
  - Static images: `static/`
  - Public assets: `public/`
  - Theme assets: `themes/roxo-hugo/static/`

**Content Management:**
- YAML data files: `data/` directory
  - `data/clients.yml` - Sponsor information and links
  - `data/testimonial.yml` - Team member profiles (names, locations, photos)
  - `data/contact.yml` - Contact page configuration
  - `data/counter.yml` - Statistics/counters
  - `data/team.yml` - Team information
  - `data/feature.yml` - Feature sections
  - `data/service.yml` - Service descriptions
  - `data/gallery.yml` - Image galleries

## Authentication & Identity

**Auth Provider:**
- None required - Public website
- Contact form (optional email collection only)

## Monitoring & Observability

**Error Tracking:**
- Not configured
- Config placeholder: `config.toml` contains `googleAnalitycsID = "Your ID"` (unconfigured)

**Logs:**
- Hugo build logs only (CLI output during build)
- No runtime logging system

## CI/CD & Deployment

**Hosting:**
- Netlify
  - Build command: `hugo`
  - Publish directory: `public`
  - Automatic builds on Git push

**CI Pipeline:**
- Netlify CI/CD (automatic)
  - Triggers on commits to repository
  - Runs Hugo build command
  - Deploys to `public/` folder
  - No custom CI/CD workflows configured

**Git Integration:**
- Repository: GLS Cycling team website
- Git submodule: `themes/roxo-hugo` (tracked as submodule)
- Netlify connected to GitHub repository

## Environment Configuration

**Required env vars:**
- `HUGO_VERSION` - Set to `0.74.3` in Netlify
- `HUGO_ENV` - Set to `production`
- `HUGO_ENABLEGITINFO` - Set to `true`

**Optional env vars:**
- `formspreeFormUrl` - For Formspree integration (currently empty in config)
- `googleAnalitycsID` - For Google Analytics (placeholder, not configured)

**Secrets location:**
- No secrets currently stored
- Formspree endpoint and Google Analytics ID would go in `config.toml` params section
- Netlify environment variables in dashboard (not in code)

## Webhooks & Callbacks

**Incoming:**
- Formspree/Netlify Forms - Contact form submissions
  - Endpoint: Configured in form `action` attribute (`{{ .Site.Params.formspreeFormUrl }}`)
  - Method: POST
  - Fields: first-name, last-name, email, about-project

**Outgoing:**
- None configured
- Potential: Netlify deploy notifications (if webhook configured on GitHub)

## External Links & References

**Social Media Links:**
- Strava: `https://www.strava.com/clubs/GLSCycling` (club feed widget)
- Instagram: `https://www.instagram.com/glscycling/`
- Twitter: `https://twitter.com/GLSCycling`
- Facebook: `https://www.facebook.com/GLSCycling`

**Sponsor Links:**
All configured in `data/clients.yml`:
- Trailhead Bike Shop: `https://trailheadbikeshop.com`
- Ludington Bay Brewing: `https://ludingtonbaybrewing.com`
- Tailwind Nutrition: `https://www.tailwindnutrition.com/?rstr=142315` (affiliate)
- Wolf Tooth Components: `https://www.wolftoothcomponents.com`
- Ron's Beans: `https://www.ronsbeans.com`
- Highland Training: `https://www.highlandtraining.net`
- Bison Coolers: `https://www.bisoncoolers.com`
- VeloPro: `https://www.velopro.bike`
- Harvey's: `https://www.harveymilling.com`

---

*Integration audit: 2026-03-01*

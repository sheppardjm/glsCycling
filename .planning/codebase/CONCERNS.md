# Codebase Concerns

**Analysis Date:** 2026-03-01

## Tech Debt

**Missing .gitignore Configuration:**
- Issue: No `.gitignore` file exists at repository root, allowing binary and build artifacts to be tracked
- Files: Repository root
- Impact: Large binary executable (`hugo.exe` - 57MB) is committed to version control (added in commit a92ad16), inflating repository size and cloning time. Lock files and build artifacts are also tracked unnecessarily
- Fix approach: Create `.gitignore` with standard Hugo and Node.js patterns; add entries for `hugo.exe`, `.hugo_build.lock`, `node_modules/`, `resources/_gen/`, `public/` (optionally), and OS files like `.DS_Store`

**Binary Executable Tracked in Git:**
- Issue: `hugo.exe` (57MB) was committed to version control in a92ad16
- Files: `/hugo.exe`
- Impact: Significantly bloats repository size; makes cloning slow; executables should not be version controlled; platform-specific binary reduces portability (Windows .exe in macOS repo)
- Fix approach: Remove from git history using `git filter-branch` or `git-filter-repo`; document Hugo version requirement in README or `.tool-versions`; use CI/CD to download correct binary

**Empty Node Package Lockfile:**
- Issue: `package-lock.json` exists but contains empty packages array with no dependencies
- Files: `/package-lock.json`
- Impact: Confusing for developers; suggests incompletely configured build pipeline; may indicate abandoned attempt to use Node.js tooling
- Fix approach: Either populate with actual Node dependencies if intended, or remove entirely; if SCSS compilation or other Node tooling is needed, properly configure and document

## Configuration Issues

**Placeholder Configuration Values Not Replaced:**
- Issue: Critical configuration values in `config.toml` left as placeholders
- Files: `/config.toml` lines 35-37
- Impact:
  - Google Analytics disabled: `googleAnalitycsID = "Your ID"` prevents site analytics
  - Contact form non-functional: `formspreeFormUrl = ""` makes contact form unusable
- Fix approach: Populate with actual service credentials before deployment; use environment variables or separate config file for sensitive values

**Outdated Hugo Version Requirement:**
- Issue: Hugo version specified in `netlify.toml` is 0.74.3 (released January 2020)
- Files: `/netlify.toml` line 6
- Impact: Security updates, bug fixes, and feature improvements in newer Hugo versions not available; potential compatibility issues with current theme; dependency vulnerabilities not patched
- Fix approach: Test and upgrade to latest stable Hugo version (0.120+); ensure theme remains compatible

**Unused/Placeholder Theme Settings:**
- Issue: `config.toml` references theme "roxo-hugo" which is a submoduled third-party agency theme
- Files: `/config.toml` line 4, `/themes/roxo-hugo/`
- Impact: Theme customization is limited to data files and layout overrides; tight coupling to external theme means any updates require careful testing; example site in theme creates confusion
- Fix approach: Document which theme is actually being used; consider whether a purpose-built theme or custom theme would be more maintainable

## Content Management Issues

**Decoupled Data and Content:**
- Issue: Team members, sponsors, and testimonials are in separate YAML data files rather than content frontmatter
- Files: `/data/team.yml`, `/data/clients.yml`, `/data/testimonial.yml`, `/data/counter.yml`
- Impact: Harder to manage multiple data sources; team member images and content are in static files with weak coupling; no metadata versioning; changes to testimonials are bulk YAML edits without clear audit trail
- Fix approach: Consider migrating to Hugo content sections with frontmatter; this would allow better content organization, easier editing, and clearer version control tracking

**Unused Image Assets:**
- Issue: `client-logo-seven.png` exists in `/static/images/clients/` but is not referenced in `data/clients.yml`
- Files: `/static/images/clients/client-logo-seven.png`, `/data/clients.yml`
- Impact: Orphaned asset clutters repository; creates confusion about what sponsors are active; wastes storage
- Fix approach: Audit all static images; remove unused assets; document which sponsors are current

**Hardcoded Google Sheets Iframe URLs:**
- Issue: Race results pages embed Google Sheets via hardcoded iframe URLs in markdown
- Files: `/content/portfolio/2024-results.md`, `/content/portfolio/2025-results.md`, `/content/portfolio/2026-results.md`
- Impact:
  - Results data lives outside version control (in Google Sheets), making historical versions unrecoverable if sheets are deleted or modified
  - Heavy coupling to Google Sheets API; if permissions change or Google deprecates the feature, results pages break
  - Iframe has fixed height (1000px) - not responsive
  - No fallback if Google Sheets is unavailable
- Fix approach:
  - Either commit results data to repository as CSV/JSON alongside markdown
  - Or create a data collection process that syncs Google Sheets to `data/` directory
  - Make iframe height responsive using JavaScript
  - Add error handling/fallback for iframe load failures

**Timezone Configuration:**
- Issue: `config.toml` specifies `dateFormat = "26 FEB 1994"` with no timezone context
- Files: `/config.toml` line 30
- Impact: Dates may display incorrectly for international users; Hugo renders dates in server timezone without explicit specification
- Fix approach: Set explicit `timeZone` in config; use ISO 8601 date format; ensure all dates in content frontmatter include timezone

## Development Workflow Issues

**Generated Files in Version Control:**
- Issue: `public/` directory (5.5MB of generated HTML/CSS/JS) is fully committed to git
- Files: All files in `/public/`
- Impact:
  - Repository bloat: every build generates new HTML files that change, creating large diffs
  - Merge conflicts in generated files when multiple branches update content
  - Obscures actual source code changes with generated output
  - Makes it impossible to distinguish content changes from build changes
- Fix approach: Add `public/` to `.gitignore`; use CI/CD to build and deploy generated files only; update deployment process to build during deployment rather than committing

**Netlify Build Configuration Missing Deploy Hook:**
- Issue: `netlify.toml` runs `hugo` command but has no post-build deployment hooks or environment configuration
- Files: `/netlify.toml`
- Impact: No control over what happens after build; no deployment logging or monitoring; form submissions still won't work (form handler not configured)
- Fix approach: Add Netlify functions for form handling if needed; configure build notifications; add status checks

**No Local Development Documentation:**
- Issue: No README or development guide explaining how to build/run locally
- Files: Missing `/README.md`
- Impact: New contributors can't understand build process; unclear what dependencies are needed; no setup instructions
- Fix approach: Create `/README.md` with: prerequisites (Hugo version), local build commands (`hugo serve`), directory structure explanation, deployment notes

## Data Quality Issues

**Year-Based Content Without Timestamp Strategy:**
- Issue: Race results are organized by year (`2019-results.md` through `2026-results.md`) but frontmatter dates are all in January of each year
- Files: `/content/portfolio/*-results.md`
- Impact:
  - Sort order may be incorrect if dates are used for ordering
  - Misleading timestamps don't reflect when results actually occurred
  - Adding mid-year races would require restructuring
- Fix approach: Use actual race dates in frontmatter; add category/season metadata for filtering; consider if results should be individual pages per race rather than annual summaries

**Counter/Statistics Require Manual Updates:**
- Issue: Statistics in `data/counter.yml` are hardcoded numbers (e.g., "13" podiums in 2024)
- Files: `/data/counter.yml`
- Impact:
  - Must be manually updated annually with each new season
  - No version history of statistics
  - Error-prone; easy to forget updating when adding results
  - Data not sourced from results spreadsheets
- Fix approach: Consider scripting to extract statistics from Google Sheets; or move to results spreadsheet as calculated fields

**Incomplete Team Data:**
- Issue: `data/team.yml` has only one team member (Aaron Barto), but `data/testimonial.yml` lists 26+ team members
- Files: `/data/team.yml`, `/data/testimonial.yml`
- Impact:
  - Team page won't display actual roster
  - Inconsistency in data sources
  - Unclear which file is source of truth
- Fix approach: Consolidate team data; decide whether testimonial.yml should be "team members" or rename to "roster.yml"; ensure single source of truth

## Security Concerns

**Hardcoded External URLs in Config:**
- Issue: Social media and external service URLs are hardcoded in `config.toml`
- Files: `/config.toml` lines 88-98
- Impact:
  - Difficult to manage URLs if services migrate or change
  - No environment-specific configurations for staging vs production
  - If URLs contain analytics tokens, they would be exposed in repo
- Fix approach: Move to environment variables or separate secrets configuration; use different configs for staging/production

**Iframe Embedding Without Sandbox Restrictions:**
- Issue: `layouts/shortcodes/gdocs.html` embeds iframes with `allowfullscreen` but no `sandbox` attribute
- Files: `/layouts/shortcodes/gdocs.html`
- Impact: Iframes can potentially execute scripts or access sensitive browser APIs; no protection against malicious iframe content
- Fix approach: Add `sandbox="allow-same-origin allow-scripts"` attributes; document iframe security implications

**Missing Security Headers:**
- Issue: No netlify.toml configuration for security headers (CSP, X-Frame-Options, etc.)
- Files: `/netlify.toml`
- Impact: Site vulnerable to clickjacking; no Content Security Policy prevents XSS injection; missing HSTS header
- Fix approach: Add `[[headers]]` section in netlify.toml with security headers; test with security scanning tools

## Fragile Areas

**Theme Submodule Dependency:**
- Issue: Site depends on external submoduled theme (`themes/roxo-hugo`) that may no longer be maintained
- Files: `/themes/roxo-hugo/` (submodule), `.gitmodules`
- Impact:
  - If upstream theme is deleted or archived, deployment will fail
  - No control over theme updates; breaking changes could occur
  - Theme is designed for agency portfolios, not cycling team racing results
- Safe modification: Never modify theme files directly; only override through `layouts/` and `static/` directories; test all changes thoroughly
- Test coverage: Manual testing only; no automated tests for theme rendering

**hardcoded Iframe Height:**
- Issue: Google Sheets iframes have fixed `height="1000"` which breaks on mobile
- Files: `/layouts/shortcodes/gdocs.html`
- Impact: Desktop-only experience; results unreadable on phones; poor responsive design
- Safe modification: Add CSS media queries or JavaScript to make responsive; test on multiple breakpoints

**Live Reload Reference in Production:**
- Issue: Generated HTML in `public/portfolio/2026-results/index.html` contains hardcoded `//localhost:1313` URLs and livereload script references
- Files: `/public/portfolio/2026-results/index.html` line 2, 29
- Impact: Production site has localhost references and livereload dependencies; indicates local development build was committed instead of production build
- Safe modification: Ensure build process uses correct baseURL; rebuild before committing; verify production URLs in generated files

## Scaling Limitations

**Large Generated Output:**
- Issue: `public/` directory is 5.5MB with fully generated HTML
- Impact:
  - Slow to clone
  - Slow to upload/sync
  - Exceeds free tier limits on some hosting
- Scaling path: Remove generated files from version control; deploy from CI/CD builds only

**Manual Sponsor/Team Management:**
- Issue: Adding/removing sponsors or team members requires manual edits to multiple YAML files
- Impact: Doesn't scale beyond ~30 people; error-prone; no admin UI
- Scaling path: Build a small CMS (Netlify CMS, Sanity, or custom API) for content management

**Single Google Sheets for Results:**
- Issue: All race results embedded from single Google Sheets per year
- Impact: Single point of failure; sheet sharing permissions critical; no backup strategy
- Scaling path: Use Google Sheets API to sync results to local data files; archive historical results in repository

## Testing Gaps

**No Test Coverage:**
- What's not tested: Generated HTML output, responsive design, link validity, iframe embeds, form submissions
- Files: Entire project (no tests exist)
- Risk: Broken links, missing images, or malformed HTML go undetected; responsive design issues on launch; broken forms impact user conversion
- Priority: Medium - static site but public-facing

**No Automated Build Verification:**
- Issue: Hugo builds but output not validated before deployment
- Impact: Malformed HTML, broken assets, or missing content could go live
- Fix approach: Add GitHub Actions or Netlify CI to run `hugo` with `--logLevel info` and verify build succeeds

**No Link Checker:**
- Issue: External URLs (social media, sponsor sites, Google Sheets) not validated
- Impact: Dead links to sponsors or results sheets; broken navigation impacts user experience
- Fix approach: Add `htmlproofer` or similar tool to CI pipeline

## Missing Critical Features

**No Analytics:**
- Issue: Google Analytics ID placeholder not configured
- Impact: No data on visitor traffic, user behavior, or campaign effectiveness
- Blocks: Can't understand audience or measure marketing success

**Non-functional Contact Form:**
- Issue: Form handling configured with Formspree but URL is empty
- Impact: Contact form on website is non-functional; visitor inquiries lost
- Blocks: User engagement channel broken; potential leads not captured

**No Blog Indexing/Search:**
- Issue: Blog content exists but no search functionality or category indexing
- Impact: Blog posts hard to discover; users can't find relevant articles
- Blocks: Content marketing strategy limited

## Maintenance & Documentation

**No Version Tracking:**
- Issue: No CHANGELOG, version tags, or release notes
- Impact: Can't track what changed between seasons; historical context lost
- Fix approach: Use git tags for season releases; maintain CHANGELOG.md

**Insufficient Commit Messages:**
- Issue: Commits like "Update website for 2026 season" lack detail about what changed
- Impact: Hard to understand what changed and why; limits ability to revert selectively
- Fix approach: Use conventional commits; include issue numbers and detailed descriptions

**Theme Maintenance Costs:**
- Issue: Theme was last updated Dec 8, 2024; upstream may have newer features/security patches
- Impact: Potential security vulnerabilities; missing features; styling inconsistencies with modern web
- Priority: Monitor for updates monthly

---

*Concerns audit: 2026-03-01*

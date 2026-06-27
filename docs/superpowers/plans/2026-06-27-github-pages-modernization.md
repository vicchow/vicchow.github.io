# GitHub Pages Modernization Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Modernize this GitHub Pages repository to current GitHub guidance with reliable Actions-based deployments, correct metadata/assets, and a maintainable static site baseline.

**Architecture:** Keep the repository static-first and deploy using GitHub Pages Actions as the single release path. Normalize shared document metadata and asset references across all entry pages, then apply accessibility/performance hardening and operational files. Use small, reviewable PRs with validation at each milestone.

**Tech Stack:** Static HTML/CSS/JS, GitHub Pages, GitHub Actions, optional HTML/CSS validation and Lighthouse.

---

## File Structure and Responsibilities

- Modify: `_config.yml`
  - Clarify static-site intent and Pages settings; remove conflicting theme dependency.
- Create: `.github/workflows/pages.yml`
  - Official Actions pipeline for build/deploy to GitHub Pages.
- Modify: `site.webmanifest`
  - Correct icon URLs and meaningful app metadata.
- Modify: `browserconfig.xml`
  - Correct Microsoft tile icon path.
- Modify: `index.html`
  - Standardized head metadata and consistent icon/manifest declarations.
- Modify: `browser/index.html`
  - Same standardized metadata baseline.
- Modify: `sample/index.html`
  - Standardized metadata plus secure external asset loading.
- Modify: `userstyles/index.html`
  - Standardized metadata and accessibility cleanup.
- Modify: `font-size/index.html`
  - Standardized metadata and accessibility cleanup for controls.
- Modify: `css/styles.css`
  - Shared design tokens and accessibility defaults.
- Create: `404.html`
  - GitHub Pages-friendly not found page.
- Create: `robots.txt`
  - Crawl directives suitable for this public static site.

---

### Task 1: Lock Deployment Strategy and GitHub Pages Config

**Files:**
- Modify: `_config.yml`

- [ ] **Step 1: Update repository config to static-first pages settings**

Set `_config.yml` to a minimal, explicit configuration. Keep only keys that are needed for Pages behavior.

Expected content example:

```yaml
# Static site deployed by GitHub Pages Actions.
title: vicchow.github.io

# Keep Jekyll processing minimal; do not couple to a remote theme.
markdown: kramdown
```

- [ ] **Step 2: Verify config syntax and commit**

Run: `ruby -e "require 'yaml'; YAML.load_file('_config.yml'); puts 'OK'"`
Expected: `OK`

Run:

```bash
git add _config.yml
git commit -m "chore: simplify pages config for static-first deployment"
```

Expected: one commit with only `_config.yml`.

---

### Task 2: Add Official GitHub Pages Actions Deployment

**Files:**
- Create: `.github/workflows/pages.yml`

- [ ] **Step 1: Add GitHub Pages workflow using official actions**

Create workflow with these requirements:
- Trigger on push to default branch and manual dispatch.
- Permissions: pages write, id-token write.
- Concurrency group for pages deployment.
- Checkout, configure-pages, upload-pages-artifact, deploy-pages.
- Upload repo root as artifact for static publishing.

Reference structure:

```yaml
name: Deploy GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/configure-pages@v5
      - uses: actions/upload-pages-artifact@v3
        with:
          path: .
      - id: deployment
        uses: actions/deploy-pages@v4
```

- [ ] **Step 2: Validate workflow file and commit**

Run: `rg "actions/(checkout|configure-pages|upload-pages-artifact|deploy-pages)@" .github/workflows/pages.yml`
Expected: four matches with pinned major versions.

Run:

```bash
git add .github/workflows/pages.yml
git commit -m "ci: add official github pages deployment workflow"
```

Expected: one commit with only workflow file.

---

### Task 3: Correct Manifest and Browser Config Asset Paths

**Files:**
- Modify: `site.webmanifest`
- Modify: `browserconfig.xml`

- [ ] **Step 1: Fix manifest metadata and icon URLs**

In `site.webmanifest`:
- Set non-empty `name` and `short_name`.
- Update icon src values to existing files under `favicons/`.
- Keep display/theme/background values aligned with site branding.

Expected icon paths:
- `/favicons/android-chrome-192.png`
- `/favicons/android-chrome-512.png`

- [ ] **Step 2: Fix browserconfig tile icon path**

In `browserconfig.xml` change tile logo path to:
- `/favicons/mstile-150.png`

- [ ] **Step 3: Validate JSON/XML and commit**

Run: `python3 -m json.tool site.webmanifest >/dev/null && echo OK-manifest`
Expected: `OK-manifest`

Run: `xmllint --noout browserconfig.xml && echo OK-browserconfig`
Expected: `OK-browserconfig`

Run:

```bash
git add site.webmanifest browserconfig.xml
git commit -m "fix: align manifest and browserconfig icon paths"
```

Expected: one commit touching only metadata asset files.

---

### Task 4: Standardize Head Metadata Across Entry Pages

**Files:**
- Modify: `index.html`
- Modify: `browser/index.html`
- Modify: `sample/index.html`
- Modify: `userstyles/index.html`
- Modify: `font-size/index.html`

- [ ] **Step 1: Apply common head baseline to each page**

Ensure each file includes:
- `<!doctype html>` and `html lang="en"`.
- `<meta charset="utf-8">`
- viewport meta.
- description meta specific to page purpose.
- canonical URL path for the page.
- favicon and manifest links using consistent root-relative paths.
- optional Open Graph title/description for share previews.

- [ ] **Step 2: Confirm no head regressions**

Run:

```bash
rg -n "meta charset|meta name=\"description\"|rel=\"manifest\"|rel=\"canonical\"" index.html browser/index.html sample/index.html userstyles/index.html font-size/index.html
```

Expected: all five files show the baseline tags.

- [ ] **Step 3: Commit metadata normalization**

Run:

```bash
git add index.html browser/index.html sample/index.html userstyles/index.html font-size/index.html
git commit -m "feat: standardize metadata across entry pages"
```

Expected: one commit containing only these five pages.

---

### Task 5: Harden External Dependencies and Loading in Sample Page

**Files:**
- Modify: `sample/index.html`

- [ ] **Step 1: Replace protocol-relative third-party URLs with HTTPS**

Update highlight.js CSS/JS links from `//...` to `https://...`.

- [ ] **Step 2: Add loading hardening where applicable**

For external script:
- Keep execution order correct for `hljs.highlightAll()`.
- Add integrity/crossorigin attributes if exact artifact hash is fixed.
- Otherwise pin explicit version and document trust boundary in HTML comment.

- [ ] **Step 3: Validate highlight behavior and commit**

Run: `rg -n "cdnjs.cloudflare.com|highlightAll" sample/index.html`
Expected: HTTPS links and one initialization call.

Run:

```bash
git add sample/index.html
git commit -m "chore: harden sample page external asset loading"
```

Expected: one-file commit.

---

### Task 6: Improve Shared CSS Baseline for Accessibility and Maintainability

**Files:**
- Modify: `css/styles.css`

- [ ] **Step 1: Add foundational design tokens and defaults**

Add or normalize:
- color, spacing, and typography tokens.
- readable content width and vertical rhythm.
- visible focus styles using `:focus-visible`.
- improved link contrast/hover behavior.
- motion preference guard with `@media (prefers-reduced-motion: reduce)`.

- [ ] **Step 2: Ensure pages remain readable after baseline update**

Manual checks:
- [ ] home page legible.
- [ ] sample code blocks still readable.
- [ ] userstyles page list links still clear.
- [ ] browser size page remains simple and unbroken.

- [ ] **Step 3: Commit shared style baseline**

Run:

```bash
git add css/styles.css
git commit -m "feat: modernize shared css baseline for accessibility"
```

Expected: one-file commit.

---

### Task 7: Add Pages Operational Files

**Files:**
- Create: `404.html`
- Create: `robots.txt`

- [ ] **Step 1: Add custom 404 page**

`404.html` should include:
- clear not-found message.
- link back to site root.
- same favicon/manifest baseline used elsewhere.
- lightweight styling or shared stylesheet reference.

- [ ] **Step 2: Add robots directives**

Create `robots.txt` with:
- `User-agent: *`
- `Allow: /`
- optional sitemap line if sitemap is later added.

- [ ] **Step 3: Commit operational files**

Run:

```bash
git add 404.html robots.txt
git commit -m "chore: add pages operational files (404, robots)"
```

Expected: one commit with two new files.

---

### Task 8: End-to-End Verification Before Merge

**Files:**
- Verify: repository-wide changes from Tasks 1-7

- [ ] **Step 1: Validate internal links and static assets**

Run:

```bash
rg -n "href=|src=" index.html browser/index.html sample/index.html userstyles/index.html font-size/index.html | head -200
```

Expected: all asset paths resolve to existing files.

- [ ] **Step 2: Run quick local static smoke test**

Run:

```bash
python3 -m http.server 4173
```

Open pages and verify manually:
- `/`
- `/browser/`
- `/sample/`
- `/userstyles/`
- `/font-size/`
- `/404.html`

Expected: no broken layout and no missing favicon/manifest requests.

- [ ] **Step 3: Verify Pages deployment status after push**

Checks in GitHub:
- Pages source set to GitHub Actions.
- latest workflow run green.
- deployed URL serves updated metadata and assets.

- [ ] **Step 4: Final release commit (if squashing locally is not used)**

Run:

```bash
git log --oneline -n 12
```

Expected: clear, task-scoped commit history matching this plan.

---

## Risks and Mitigations

- Risk: Theme/Jekyll assumptions break if hidden Jekyll features are relied upon.
  - Mitigation: Keep `_config.yml` minimal and test all page routes before merge.
- Risk: CDN dependency changes or outages affect `sample` page.
  - Mitigation: Use pinned HTTPS URLs and optionally self-host assets.
- Risk: Metadata drift across pages over time.
  - Mitigation: Treat Task 4 baseline as required for every new page and add a PR checklist item.

## Acceptance Criteria

- GitHub Pages deploys from Actions successfully on default branch pushes.
- Manifest and browserconfig paths resolve to existing icons.
- All entry pages share consistent modern head metadata.
- External URLs in HTML are HTTPS and intentionally pinned.
- Shared CSS baseline provides visible focus and improved readability.
- 404 and robots files are present and functional.

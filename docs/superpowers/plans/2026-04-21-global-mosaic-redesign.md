# Global Mosaic Website Redesign — Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Redesign eabeam.github.io from stock al-folio to the "Global Mosaic" template — a warm, photo-forward, light-mode academic site that leads with geography and field presence.

**Architecture:** Keep Jekyll + jekyll-scholar BibTeX pipeline. Replace the al-folio visual layer (SCSS, layouts, includes) with custom CSS matching the approved mockup. The homepage becomes a single-page showcase (hero, affiliations strip, publications, photo break, works in progress, teaching, CV download). Inner pages (publications, working, teaching, cv) retain their own URLs with new styling. Bootstrap/MDB CDNs are dropped in favor of custom CSS Grid/Flexbox.

**Tech Stack:** Jekyll 4.4, jekyll-scholar (BibTeX), custom SCSS, Google Fonts (Bricolage Grotesque + Lora), CSS Grid, no framework dependencies.

**Reference mockup:** `/Users/ebeam/Desktop/template-proposals/template2-global-mosaic.html`

---

## File Map

### Files to create
- `assets/img/hero-field.jpg` — hero photo (field/research context)
- `assets/img/photo-break.jpg` — full-width photo break between sections
- `_sass/_mosaic.scss` — all new component styles (replaces `_base.scss` role for custom components)

### Files to heavily modify (rewrite)
- `_includes/header.html` — new nav (custom, no Bootstrap)
- `_includes/footer.html` — new footer (flex, no Bootstrap)
- `_includes/head.html` — new fonts, drop Bootstrap/MDB/FontAwesome CDNs
- `_layouts/default.html` — remove Bootstrap container, new wrapper
- `_layouts/about.html` — complete rewrite: hero, strip, all homepage sections
- `_sass/_variables.scss` — new color palette
- `_sass/_themes.scss` — light-only, new CSS custom properties
- `_sass/_base.scss` — strip down to essentials (typography, links, basics)
- `_sass/_layout.scss` — new layout primitives
- `assets/css/main.scss` — add `_mosaic` import

### Files to lightly modify
- `_config.yml` — disable dark mode, progress bar; update max_width
- `_pages/about.md` — update frontmatter for new layout
- `_pages/publications.md` — may need minor layout class adjustments
- `_pages/working.md` — may need minor layout class adjustments
- `_pages/teaching.md` — may need minor layout class adjustments

### Files unchanged
- `_bibliography/papers.bib` — content untouched
- `_data/coauthors.yml` — content untouched
- `_data/cv.yml` — content untouched
- `_layouts/bib.html` — bibliography rendering (restyled via CSS only)
- `_layouts/cv.html` — CV rendering (restyled via CSS only)
- `Gemfile` — no dependency changes

---

## Task 1: Branch Setup

**Files:** repo root

- [ ] **Step 1: Create and switch to redesign branch**

```bash
cd ~/Dropbox/Github/eabeam.github.io
git checkout -b redesign/global-mosaic
```

- [ ] **Step 2: Verify clean state**

```bash
git status
```

Expected: clean working tree on `redesign/global-mosaic`

- [ ] **Step 3: Commit plan file**

```bash
git add docs/superpowers/plans/2026-04-21-global-mosaic-redesign.md
git commit -m "docs: add global mosaic redesign implementation plan"
```

---

## Task 2: Source Field Photos

**Files:**
- Create: `assets/img/hero-field.jpg`
- Create: `assets/img/photo-break.jpg`

Emily has field research across Philippines, Bangladesh, Malawi, Zimbabwe, Brazil, and the US. The hero image and photo break need real photos — either personal field photos or appropriately licensed stock.

- [ ] **Step 1: Identify hero photo**

The hero takes up the left half of the viewport (~520px tall). Requirements:
- Landscape or square orientation, minimum 1200px wide
- Field/research context: a scene from one of the research countries
- Works well with a subtle teal color wash overlay
- Not a portrait of Emily (that's the profile pic's job)
- Good options: market scene, school setting, community, landscape

Ask Emily: "Do you have field photos from your research sites? I need two: (1) a hero image ~1200x600px and (2) a full-width landscape ~1400x300px for the photo break between sections. If not, we can use placeholder images and swap later."

- [ ] **Step 2: Identify photo-break image**

The photo break is a full-width band (~280px tall). Requirements:
- Wide landscape, minimum 1400px wide
- Different country/context than the hero if possible
- Works well slightly desaturated and darkened

- [ ] **Step 3: Place images**

Copy selected photos to the assets directory:

```bash
# After Emily provides photos:
cp /path/to/hero-photo.jpg ~/Dropbox/Github/eabeam.github.io/assets/img/hero-field.jpg
cp /path/to/break-photo.jpg ~/Dropbox/Github/eabeam.github.io/assets/img/photo-break.jpg
```

If no photos available yet, create placeholder references that will be swapped later. Use the Unsplash URLs from the mockup as temporary placeholders in the template.

- [ ] **Step 4: Optimize images**

```bash
# Resize to reasonable web sizes if originals are very large
sips --resampleWidth 1400 assets/img/hero-field.jpg
sips --resampleWidth 1600 assets/img/photo-break.jpg
```

- [ ] **Step 5: Commit**

```bash
git add assets/img/hero-field.jpg assets/img/photo-break.jpg
git commit -m "feat: add field photos for homepage hero and photo break"
```

If using Unsplash placeholders, skip this commit — the URLs go directly in the layout.

---

## Task 3: Config and Head Updates

**Files:**
- Modify: `_config.yml`
- Modify: `_includes/head.html`

- [ ] **Step 1: Update `_config.yml`**

Change these settings:

```yaml
# Layout
navbar_fixed: true
footer_fixed: false  # was: true — new footer is not fixed

# Dimensions
max_width: 1100px  # was: 800px — wider for new layout

# Optional Features — disable what we no longer use
enable_darkmode:            false  # was: true
enable_progressbar:         false  # was: true
enable_masonry:             false  # was: true
enable_medium_zoom:         false  # was: true
enable_navbar_social:       false
```

Also update `footer_text`:

```yaml
footer_text: ""  # we handle footer content in the template now
```

- [ ] **Step 2: Update `_includes/head.html`**

Replace the font and CSS CDN imports. Remove Bootstrap CSS, MDB CSS, FontAwesome, Academicons, and Roboto. Add Bricolage Grotesque + Lora from Google Fonts.

Find and replace the block from `<!-- Bootstrap & MDB -->` through the Roboto `<link>` with:

```html
    <!-- Fonts -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,300;12..96,400;12..96,500;12..96,700&family=Lora:ital,wght@0,400;0,500;0,600;1,400&display=swap" rel="stylesheet">
```

Remove the Dark Mode block entirely (the `{% if site.enable_darkmode %}` ... `{% endif %}` block that loads dark theme CSS and JS).

Keep: the `<!-- Styles -->` block that loads `main.css`, the `<link rel="canonical">`, and the metadata include.

- [ ] **Step 3: Verify head.html is clean**

Read the file back and confirm:
- No Bootstrap CSS CDN
- No MDB CSS CDN
- No FontAwesome CDN
- No Academicons CDN
- No Roboto font link
- No dark mode CSS/JS
- Google Fonts link for Bricolage Grotesque + Lora is present
- `main.css` link is still present

- [ ] **Step 4: Commit**

```bash
git add _config.yml _includes/head.html
git commit -m "feat: update config and head for Global Mosaic theme

Disable dark mode, progress bar, masonry. Widen max_width to 1100px.
Replace Bootstrap/MDB/FontAwesome CDNs with Google Fonts (Bricolage Grotesque + Lora)."
```

---

## Task 4: SCSS Foundation — Variables, Themes, Layout

**Files:**
- Modify: `_sass/_variables.scss`
- Modify: `_sass/_themes.scss`
- Modify: `_sass/_layout.scss`
- Modify: `assets/css/main.scss`

- [ ] **Step 1: Rewrite `_sass/_variables.scss`**

Replace entire file contents with the Global Mosaic palette:

```scss
/*******************************************************************************
 * Global Mosaic Theme — Variables
 ******************************************************************************/

// Core palette
$bg:            #fcfaf7;
$white:         #ffffff;
$ink:           #1e1e1e;
$ink-soft:      #4a4a4a;
$ink-faint:     #8a8a8a;

// Accent colors
$teal:          #1a7a6d;
$teal-bg:       #e6f3f0;
$terracotta:    #c75b39;
$terracotta-bg: #fae8e1;
$ochre:         #b38b2d;
$ochre-bg:      #f8f0d8;
$indigo:        #4a5a8a;
$indigo-bg:     #e8ecf4;
$sage:          #5a7a52;

// Borders
$rule:          #e5e2dc;
$rule-light:    #efece6;

// Code
$code-bg-color-light: rgba($teal, 0.06);
```

- [ ] **Step 2: Rewrite `_sass/_themes.scss`**

Replace entire file contents — light mode only, new CSS custom properties:

```scss
/*******************************************************************************
 * Global Mosaic Theme — Light mode only
 ******************************************************************************/

:root {
  --bg: #{$bg};
  --white: #{$white};
  --ink: #{$ink};
  --ink-soft: #{$ink-soft};
  --ink-faint: #{$ink-faint};
  --teal: #{$teal};
  --teal-bg: #{$teal-bg};
  --terracotta: #{$terracotta};
  --terracotta-bg: #{$terracotta-bg};
  --ochre: #{$ochre};
  --ochre-bg: #{$ochre-bg};
  --indigo: #{$indigo};
  --indigo-bg: #{$indigo-bg};
  --sage: #{$sage};
  --rule: #{$rule};
  --rule-light: #{$rule-light};

  // Legacy mappings for bib.html and other al-folio templates that use these
  --global-bg-color: #{$bg};
  --global-code-bg-color: #{$code-bg-color-light};
  --global-text-color: #{$ink};
  --global-text-color-light: #{$ink-faint};
  --global-theme-color: #{$teal};
  --global-hover-color: #{$teal};
  --global-footer-bg-color: #{$bg};
  --global-footer-text-color: #{$ink-faint};
  --global-footer-link-color: #{$teal};
  --global-distill-app-color: #{$ink-faint};
  --global-divider-color: #{$rule};
  --global-card-bg-color: #{$white};
}
```

- [ ] **Step 3: Rewrite `_sass/_layout.scss`**

Replace entire file with new layout primitives:

```scss
/*******************************************************************************
 * Global Mosaic Theme — Layout
 ******************************************************************************/

* { margin: 0; padding: 0; box-sizing: border-box; }

html { scroll-behavior: smooth; }

body {
  font-family: 'Lora', Georgia, serif;
  background: var(--bg);
  color: var(--ink);
  line-height: 1.7;
  font-size: 17px;
  -webkit-font-smoothing: antialiased;
}

// Content containers
.container-wide {
  max-width: 1100px;
  margin: 0 auto;
  padding-left: 2.5rem;
  padding-right: 2.5rem;
}

section {
  max-width: 1100px;
  margin: 0 auto;
  padding: 4.5rem 2.5rem;
}

// Inner page wrapper (for publications, teaching, cv, working pages)
.page-content {
  max-width: 1100px;
  margin: 0 auto;
  padding: 6rem 2.5rem 4rem;
}

// Responsive
@media (max-width: 768px) {
  .container-wide,
  section,
  .page-content {
    padding-left: 1.5rem;
    padding-right: 1.5rem;
  }
}
```

- [ ] **Step 4: Update `assets/css/main.scss`**

Add `_mosaic` to the import list:

```scss
---
# Only the main Sass file needs front matter (the dashes are enough)
---
@charset "utf-8";

// Dimensions
$max-content-width: {{ site.max_width }};

@import
  "variables",
  "themes",
  "layout",
  "base",
  "mosaic"
;
```

- [ ] **Step 5: Create empty `_sass/_mosaic.scss` placeholder**

```scss
/*******************************************************************************
 * Global Mosaic Theme — Components
 * All custom component styles for the Global Mosaic redesign
 ******************************************************************************/

// Component styles will be added in subsequent tasks
```

- [ ] **Step 6: Commit**

```bash
git add _sass/_variables.scss _sass/_themes.scss _sass/_layout.scss assets/css/main.scss _sass/_mosaic.scss
git commit -m "feat: SCSS foundation — new palette, light-only theme, layout primitives"
```

---

## Task 5: Strip Down `_base.scss`

**Files:**
- Modify: `_sass/_base.scss`

The current `_base.scss` is 715 lines of al-folio styling. We need to keep the parts that style elements used by jekyll-scholar (bibliography rendering) and the CV, but remove Bootstrap-dependent styles and replace typography/link styles.

- [ ] **Step 1: Rewrite `_base.scss`**

Replace the entire file. Keep only essential base element styles and bibliography/CV compatibility. Everything else moves to `_mosaic.scss`:

```scss
/*******************************************************************************
 * Base element styles — Global Mosaic
 ******************************************************************************/

// Typography basics
p, h1, h2, h3, h4, h5, h6, em, div, li, span, strong {
  color: var(--ink);
}

h1, h2, h3, h4, h5, h6 {
  font-family: 'Bricolage Grotesque', sans-serif;
}

hr {
  border: none;
  border-top: 1px solid var(--rule);
  margin: 2rem 0;
}

a {
  color: var(--teal);
  text-decoration: none;
  transition: color 0.25s;
  &:hover {
    color: darken(#1a7a6d, 8%);
    text-decoration: underline;
  }
}

figure, img {
  max-width: 100%;
}

blockquote {
  background: var(--bg);
  border-left: 3px solid var(--teal);
  margin: 1.5em 0;
  padding: 0.75em 1.25em;
  font-size: 1.05rem;
  color: var(--ink-soft);
}

table {
  width: 100%;
  border-collapse: collapse;
  td, th {
    color: var(--ink);
    padding: 0.5rem 0.75rem;
    border-bottom: 1px solid var(--rule-light);
  }
  th {
    font-family: 'Bricolage Grotesque', sans-serif;
    font-weight: 600;
    font-size: 0.85rem;
    text-align: left;
  }
}

// Bibliography/publications compatibility
// These classes are used by jekyll-scholar's bib.html template
.publications {
  .year {
    font-family: 'Bricolage Grotesque', sans-serif;
    font-size: 1.4rem;
    font-weight: 700;
    color: var(--ink);
    margin-top: 2rem;
    margin-bottom: 1rem;
    letter-spacing: -0.02em;
  }

  .row {
    display: flex;
    gap: 1rem;
    margin-bottom: 1.5rem;
    padding-bottom: 1.5rem;
    border-bottom: 1px solid var(--rule-light);
  }

  .col-sm-2 {
    flex: 0 0 60px;
    &.abbr .badge {
      font-family: 'Bricolage Grotesque', sans-serif;
      font-size: 0.65rem;
      font-weight: 700;
      letter-spacing: 0.04em;
      text-transform: uppercase;
      background: var(--teal-bg);
      color: var(--teal);
      padding: 0.2rem 0.5rem;
      border-radius: 3px;
      display: inline-block;
      a { color: inherit; text-decoration: none; }
    }
    &.preview img {
      width: 60px;
      border-radius: 4px;
    }
  }

  .col-sm-8 {
    flex: 1;
    .title {
      font-family: 'Lora', serif;
      font-weight: 600;
      font-size: 1rem;
      line-height: 1.45;
      margin-bottom: 0.2rem;
    }
    .author {
      font-family: 'Bricolage Grotesque', sans-serif;
      font-size: 0.82rem;
      color: var(--ink-faint);
      em { font-style: normal; font-weight: 600; color: var(--ink); }
      a { color: var(--teal); text-decoration: none; }
    }
    .periodical {
      font-size: 0.9rem;
      color: var(--ink-soft);
      em { font-style: italic; }
    }
    .links {
      margin-top: 0.5rem;
      .btn {
        font-family: 'Bricolage Grotesque', sans-serif;
        font-size: 0.7rem;
        font-weight: 600;
        letter-spacing: 0.03em;
        text-transform: uppercase;
        color: var(--teal);
        background: var(--teal-bg);
        border: 1px solid transparent;
        padding: 0.2rem 0.6rem;
        border-radius: 3px;
        margin-right: 0.4rem;
        cursor: pointer;
        text-decoration: none;
        display: inline-block;
        &:hover {
          border-color: var(--teal);
        }
      }
    }
  }

  // Hidden abstract/bibtex blocks
  .abstract, .bibtex {
    &.hidden { display: none; }
    margin-top: 0.75rem;
    padding: 1rem;
    background: var(--teal-bg);
    border-radius: 4px;
    font-size: 0.9rem;
    line-height: 1.65;
    color: var(--ink-soft);
  }

  .bibtex pre {
    margin: 0;
    font-size: 0.78rem;
    background: transparent;
  }
}

// Code blocks
pre {
  background: var(--teal-bg);
  padding: 1rem 1.25rem;
  border-radius: 4px;
  overflow-x: auto;
  font-size: 0.85rem;
}

code {
  font-size: 0.88em;
  background: rgba(0,0,0,0.04);
  padding: 0.15em 0.35em;
  border-radius: 3px;
}

pre code {
  background: none;
  padding: 0;
}

// CV page compatibility
.cv {
  h2 {
    font-size: 1.3rem;
    font-weight: 700;
    margin-top: 2rem;
    margin-bottom: 1rem;
    padding-bottom: 0.5rem;
    border-bottom: 2px solid var(--ink);
  }

  .card {
    background: var(--white);
    border: 1px solid var(--rule);
    border-radius: 6px;
    padding: 1.25rem 1.5rem;
    margin-bottom: 0.75rem;
  }

  .badge {
    font-family: 'Bricolage Grotesque', sans-serif;
    font-size: 0.7rem;
    font-weight: 600;
    background: var(--teal-bg);
    color: var(--teal);
    padding: 0.15rem 0.5rem;
    border-radius: 3px;
  }
}

// Additional info in bib entries
.additional-info {
  margin-top: 0.25rem;
  font-size: 0.88rem;
  color: var(--ink-soft);
}
```

- [ ] **Step 2: Verify the SCSS compiles**

```bash
cd ~/Dropbox/Github/eabeam.github.io
bundle exec jekyll build --trace 2>&1 | tail -5
```

Expected: build succeeds (may have layout warnings, that's fine — we haven't updated layouts yet).

- [ ] **Step 3: Commit**

```bash
git add _sass/_base.scss
git commit -m "feat: rewrite base styles for Global Mosaic theme

Strip 715-line al-folio _base.scss to essentials. Keep bibliography/CV
compatibility classes. New typography with Bricolage Grotesque + Lora."
```

---

## Task 6: Component Styles (`_mosaic.scss`)

**Files:**
- Modify: `_sass/_mosaic.scss`

This is all the custom component CSS from the mockup, adapted for Jekyll.

- [ ] **Step 1: Write `_sass/_mosaic.scss`**

Copy the full component CSS from the mockup (`template2-global-mosaic.html` lines 42–617) into this file, converting from plain CSS to SCSS. The full content is the CSS from the mockup's `<style>` block, starting from `/* === NAV === */` through the responsive media query. Convert CSS custom properties references to use the SCSS variables where they're static, but keep `var()` references for the CSS custom properties defined in `_themes.scss`.

Key sections to include (copy directly from mockup, adapting as SCSS):
- Nav styles (`.nav-inner`, `.site-name`, `.nav-links`, `.nav-btn`)
- Hero styles (`.hero`, `.hero-photo-side`, `.hero-text-side`)
- Affiliation strip (`.strip-bar`, `.strip-inner`, `.affil-chip`, `.geo-tag`, `.geo-dot`)
- Section headers (`.section-head`, `.tag`)
- Research mosaic (`.research-mosaic`, `.r-card`, `.badge`, `.links-row`)
- Photo break (`.photo-break`)
- Publication timeline (`.pub-timeline`, `.pub-year`, `.pub-title`, `.pub-venue`)
- Teaching grid (`.teach-grid`, `.teach-item`, `.teach-feature`)
- CV section (`.cv-section`)
- Footer (the new `footer` styles)
- Animations (`@keyframes rise`, stagger delays)
- Responsive breakpoints (`@media (max-width: 768px)`)

Additionally add styles for inner pages:

```scss
// === INNER PAGE STYLES ===
// Used by page.html layout for publications, teaching, working, cv pages

.page-header {
  margin-bottom: 2.5rem;
  padding-bottom: 1.5rem;
  border-bottom: 1px solid var(--rule);

  h1 {
    font-family: 'Bricolage Grotesque', sans-serif;
    font-size: 2rem;
    font-weight: 700;
    letter-spacing: -0.02em;
    text-transform: capitalize;
  }

  .page-description {
    font-size: 0.95rem;
    color: var(--ink-soft);
    margin-top: 0.5rem;
  }
}

// Inner page content inherits body font (Lora)
.page-body {
  h2 {
    font-family: 'Bricolage Grotesque', sans-serif;
    font-size: 1.4rem;
    font-weight: 700;
    margin-top: 2.5rem;
    margin-bottom: 1rem;
    letter-spacing: -0.01em;
  }

  h3 {
    font-size: 1.1rem;
    font-weight: 600;
    margin-top: 1.5rem;
    margin-bottom: 0.75rem;
  }

  ul, ol {
    padding-left: 1.25rem;
    margin-bottom: 1rem;
  }

  li {
    margin-bottom: 0.5rem;
    line-height: 1.7;
  }

  p {
    margin-bottom: 1rem;
  }

  a {
    text-decoration: underline;
    text-decoration-color: rgba(26,122,109,0.3);
    text-underline-offset: 3px;
    &:hover {
      text-decoration-color: var(--teal);
    }
  }
}

// Mobile hamburger menu
.nav-toggle {
  display: none;
  background: none;
  border: none;
  cursor: pointer;
  padding: 0.5rem;

  span {
    display: block;
    width: 22px;
    height: 2px;
    background: var(--ink);
    margin: 5px 0;
    transition: all 0.3s;
  }
}

@media (max-width: 768px) {
  .nav-toggle { display: block; }

  .nav-links {
    display: none;
    position: absolute;
    top: 100%;
    left: 0;
    right: 0;
    background: var(--bg);
    border-bottom: 1px solid var(--rule);
    flex-direction: column;
    padding: 1rem 2.5rem;
    gap: 0.75rem;

    &.open { display: flex; }
  }
}
```

- [ ] **Step 2: Verify build**

```bash
cd ~/Dropbox/Github/eabeam.github.io
bundle exec jekyll build --trace 2>&1 | tail -5
```

Expected: successful build.

- [ ] **Step 3: Commit**

```bash
git add _sass/_mosaic.scss
git commit -m "feat: add Global Mosaic component styles

Nav, hero, affiliations strip, research mosaic, publications timeline,
teaching grid, CV section, animations, responsive breakpoints."
```

---

## Task 7: Header and Footer Rewrites

**Files:**
- Modify: `_includes/header.html`
- Modify: `_includes/footer.html`

- [ ] **Step 1: Rewrite `_includes/header.html`**

Replace entire file with the new nav. Keep the Liquid logic for generating nav links dynamically:

```html
<header>
  <nav>
    <div class="nav-inner">
      <a href="{{ '/' | relative_url }}" class="site-name">{{ site.first_name }} {{ site.last_name }}</a>

      <button class="nav-toggle" aria-label="Toggle navigation" onclick="this.nextElementSibling.classList.toggle('open')">
        <span></span><span></span><span></span>
      </button>

      <ul class="nav-links">
        {%- assign sorted_pages = site.pages | sort: "nav_order" -%}
        {%- for p in sorted_pages -%}
          {%- if p.nav and p.autogen == nil -%}
        <li><a href="{{ p.url | relative_url }}"{% if page.url == p.url %} class="active"{% endif %}>{{ p.title }}</a></li>
          {%- endif -%}
        {%- endfor -%}
        <li><a href="https://thinkingwithagents.github.io" class="nav-btn">Thinking with Agents</a></li>
      </ul>
    </div>
  </nav>
</header>
```

- [ ] **Step 2: Rewrite `_includes/footer.html`**

Replace entire file:

```html
<footer>
  <span>&copy; {{ site.time | date: '%Y' }} {{ site.first_name }} {{ site.last_name }} &middot; University of Vermont</span>
  <div class="f-links">
    <a href="mailto:{{ site.email | encode_email }}">Email</a>
    <a href="https://scholar.google.com/citations?user=REPLACE_WITH_ID">Google Scholar</a>
    <a href="https://github.com/{{ site.github_username }}">GitHub</a>
  </div>
</footer>
```

Note: The Google Scholar link needs Emily's actual Scholar profile ID. Check her current site or ask.

- [ ] **Step 3: Commit**

```bash
git add _includes/header.html _includes/footer.html
git commit -m "feat: rewrite header and footer for Global Mosaic

Custom nav with Thinking with Agents button. Clean footer with
email, Scholar, GitHub links. No Bootstrap dependency."
```

---

## Task 8: Default Layout

**Files:**
- Modify: `_layouts/default.html`

- [ ] **Step 1: Rewrite `_layouts/default.html`**

Remove Bootstrap container wrapper and old script includes. Keep jQuery for jekyll-scholar's abstract/bibtex toggle functionality:

```html
<!DOCTYPE html>
<html lang="{{ site.lang }}">

  <head>
  {%- if page.redirect -%}
    <meta http-equiv="refresh" content="3; url={{ site.baseurl }}/" />
  {%- endif -%}
  {% include head.html %}
  </head>

  <body>
    {%- include header.html %}

    {{ content }}

    {%- include footer.html %}

    <!-- jQuery (needed for jekyll-scholar abstract/bibtex toggles) -->
    <script src="https://cdn.jsdelivr.net/npm/jquery@{{ site.jquery.version }}/dist/jquery.min.js" integrity="{{ site.jquery.integrity }}" crossorigin="anonymous"></script>
    <script>
      // Abstract and bibtex toggle for publications
      $(document).ready(function() {
        $('a.abstract').click(function() {
          $(this).parent().siblings('.abstract.hidden').slideToggle(200);
          return false;
        });
        $('a.bibtex').click(function() {
          $(this).parent().siblings('.bibtex.hidden').slideToggle(200);
          return false;
        });
      });
    </script>
    {% if site.enable_math %}
    {% include scripts/mathjax.html %}
    {% endif %}
  </body>
</html>
```

- [ ] **Step 2: Commit**

```bash
git add _layouts/default.html
git commit -m "feat: simplify default layout

Remove Bootstrap/MDB JS, masonry, progress bar scripts.
Keep jQuery for publication toggle and MathJax for equations."
```

---

## Task 9: Homepage Layout (`about.html`)

**Files:**
- Modify: `_layouts/about.html`
- Modify: `_pages/about.md`

This is the biggest task — the entire homepage experience.

- [ ] **Step 1: Rewrite `_layouts/about.html`**

Replace entire file with the Global Mosaic homepage. This uses a mix of hardcoded structure (hero, strip, sections) and Liquid content insertion:

```html
---
layout: default
---

<!-- HERO -->
<header class="hero">
  <div class="hero-photo-side">
    <img src="{{ '/assets/img/hero-field.jpg' | relative_url }}" alt="Field research">
  </div>
  <div class="hero-text-side">
    <p class="eyebrow">Development Economist</p>
    <h1>{{ site.first_name }} {{ site.last_name }}</h1>
    <div class="bio">
      {{ content }}
    </div>
  </div>
</header>

<!-- AFFILIATION + GEOGRAPHY STRIP -->
<div class="strip-bar">
  <div class="strip-inner">
    <div class="affil-chips">
      <a href="https://www.uvm.edu/gund" class="affil-chip">Gund Institute</a>
      <a href="https://www.iza.org/" class="affil-chip">IZA</a>
      <a href="https://www.povertyactionlab.org/" class="affil-chip">J-PAL</a>
    </div>
    <div class="geo-markers">
      <span class="geo-tag"><span class="geo-dot ph"></span> Philippines</span>
      <span class="geo-tag"><span class="geo-dot bd"></span> Bangladesh</span>
      <span class="geo-tag"><span class="geo-dot mw"></span> Malawi</span>
      <span class="geo-tag"><span class="geo-dot zw"></span> Zimbabwe</span>
      <span class="geo-tag"><span class="geo-dot br"></span> Brazil</span>
      <span class="geo-tag"><span class="geo-dot us"></span> US</span>
    </div>
  </div>
</div>

<!-- PUBLICATIONS — rendered via jekyll-scholar -->
<section id="publications">
  <div class="section-head">
    <p class="tag">Published Work</p>
    <h2>Publications</h2>
  </div>
  <div class="publications">
    {%- for y in page.pub_years %}
    <h2 class="year">{{ y }}</h2>
    {% bibliography -f papers -q @*[year={{y}}]* %}
    {%- endfor %}
  </div>
</section>

<!-- PHOTO BREAK -->
<div class="photo-break">
  <img src="{{ '/assets/img/photo-break.jpg' | relative_url }}" alt="Research context">
</div>

<!-- WORKS IN PROGRESS — from page content via data or markdown -->
<section id="research">
  <div class="section-head">
    <p class="tag">Current Research</p>
    <h2>Works in Progress</h2>
  </div>
  {% if page.works_in_progress %}
  <div class="research-mosaic">
    {%- for project in page.works_in_progress %}
    <div class="r-card{% if forloop.first %} wide{% endif %}">
      <span class="badge badge-{{ project.status_class | default: 'ip' }}">{{ project.status }}</span>
      <h3>{{ project.title }}</h3>
      <p class="coauthors">{{ project.coauthors }}</p>
      {% if project.description %}<p class="card-desc">{{ project.description }}</p>{% endif %}
      <div class="geo-row">
        {%- for country in project.countries %}
        <span class="geo-dot {{ country }}"></span>
        {%- endfor %}
      </div>
      <div class="links-row">
        {%- for link in project.links %}
        <a href="{{ link.url }}">{{ link.label }}</a>
        {%- endfor %}
      </div>
    </div>
    {%- endfor %}
  </div>
  {% endif %}
</section>

<!-- TEACHING -->
<section id="teaching">
  <div class="section-head">
    <p class="tag">Teaching</p>
    <h2>Courses &amp; Resources</h2>
  </div>
  <div class="teach-grid">
    {%- if page.courses %}
    {%- for course in page.courses %}
    <div class="teach-item">
      <p class="code">{{ course.code }}</p>
      <h3>{{ course.name }}</h3>
      <div class="sems">
        {%- for sem in course.semesters %}
        <a href="{{ sem.url }}">{{ sem.label }}</a>
        {%- endfor %}
      </div>
    </div>
    {%- endfor %}
    {%- endif %}

    <div class="teach-feature">
      <div class="feat-text">
        <h3>Thinking with Agents</h3>
        <p>An open resource on AI tools, critical thinking, and practical skills for economics students and researchers.</p>
      </div>
      <a href="https://thinkingwithagents.github.io" class="feat-btn">Explore &rarr;</a>
    </div>
  </div>
</section>

<!-- CV -->
<div class="cv-section" id="cv">
  <h2>Curriculum Vitae</h2>
  <a href="{{ '/assets/pdf/beam_cv.pdf' | relative_url }}">Download CV (PDF)</a>
</div>
```

- [ ] **Step 2: Rewrite `_pages/about.md` frontmatter**

Replace the frontmatter with structured data for the new layout. Keep the markdown content (bio text) but restructure the YAML to feed the template:

```yaml
---
layout: about
title: about
permalink: /
subtitle: Associate Professor of Economics, <a href='http://www.uvm.edu'>University of Vermont</a>

# Publications years (for jekyll-scholar on homepage)
pub_years: [2026, 2023, 2021, 2020, 2016]

# Works in progress (structured data for mosaic cards)
works_in_progress:
  - title: "Lowering Barriers to Remote Education: Experimental Impacts on Parental Responses and Learning"
    coauthors: "with Priya Mukherjee & Laia Navarro-Sola"
    description: "How reducing barriers to remote education technologies affects parental engagement and student learning in developing contexts."
    status: "Working Paper"
    status_class: "wp"
    countries: [bd]
    links:
      - label: "Paper (PDF)"
        url: "/assets/pdf/BeamMukherjeeNavarro-Sola_2025_WP_RemoteEd.pdf"
      - label: "IZA Discussion Paper"
        url: "https://docs.iza.org/dp15596.pdf"

  - title: "Improving Childcare Quality through Social Franchising"
    coauthors: "with Anne Fitzpatrick & Emy Reimao"
    status: "Cond. Accepted · JDE"
    status_class: "ca"
    countries: [mw]
    links:
      - label: "Pre-Results"
        url: "https://afosterri.org/jdepreresults/wp-content/uploads/2025/06/fitzpatrick-beam-reimao-childcare-quality-social-francising-DEVE-eb7f41ac1dc5c16cb897ce8cad6fdeb0.pdf"
      - label: "Registry"
        url: "https://www.socialscienceregistry.org/trials/11747"
      - label: "Policy Brief"
        url: "https://g2lm-lic.iza.org/wp-content/uploads/2025/12/GLMLIC-Policy-Brief_082.pdf"
      - label: "QuICK Project"
        url: "https://quick-twi.netlify.app/"

  - title: "Gender Norms at Work: Impacts on Women's Hiring and Workplace Experiences in Bangladesh"
    coauthors: "with Joshua Merfeld & Naveen Wickremeratne"
    status: "Cond. Accepted · JDE"
    status_class: "ca"
    countries: [bd]
    links:
      - label: "Pre-Results"
        url: "https://afosterri.org/jdepreresults/wp-content/uploads/2025/06/beam-merfeld-wickremeratne-improving-gender-norms-in-the-workpla-fae81fdd6c5501c2ce989054fa0522ab.pdf"
      - label: "Registry"
        url: "https://www.socialscienceregistry.org/trials/13473"

  - title: "Hiring Discrimination Against Transgender Job Applicants in the US Labor Market"
    coauthors: "with Ivy Stanton"
    status: "In Progress"
    status_class: "ip"
    countries: [us]
    links:
      - label: "Registry"
        url: "https://www.socialscienceregistry.org/trials/13199"

  - title: "Supporting Early Education to Increase Higher Education Enrollment in the Philippines"
    coauthors: "with Noam Angrist, Dean Karlan, Rene Marlon Panti & Christopher Udry"
    status: "In Progress"
    status_class: "ip"
    countries: [ph]
    links:
      - label: "IPA Project"
        url: "https://poverty-action.org/supporting-early-education-increase-higher-education-enrollment-philippines"

  - title: "Bridging the Soft Skills Gap Through Technology"
    coauthors: "with Ricardo Dahis, Ursula Mello & Laia Navarro-Sola"
    status: "In Progress"
    status_class: "ip"
    countries: [br]
    links: []

# Courses (structured data for teaching grid)
courses:
  - code: "ECON 3500"
    name: "Econometrics & Applications"
    semesters:
      - label: "F22"
        url: "https://ec200s22.netlify.app/"
      - label: "F21"
        url: "https://ec200f21.netlify.app/"
      - label: "F20"
        url: "https://ec200f20.netlify.app/"
  - code: "ECON 1450"
    name: "Using Data for Economic Policy"
    semesters:
      - label: "S21"
        url: "https://eabeam.github.io/EC137.S21/"
  - code: "ECON 2400"
    name: "Economic Development"
    semesters:
      - label: "F21"
        url: "https://eabeam.github.io/EC140.F21/"
---

Associate Professor of Economics at the <a href="https://www.uvm.edu/cas/economics">University of Vermont</a>. I design and run **randomized impact evaluations** with governments and NGOs — building evidence on what works in employment, education, and social inclusion across six countries.
```

Note: The bio text is shortened for the hero. The full bio stays on the about page (or could be expanded).

- [ ] **Step 3: Verify local build**

```bash
cd ~/Dropbox/Github/eabeam.github.io
bundle exec jekyll serve --trace
```

Open http://localhost:4000 in browser. Check:
- Hero renders with photo + bio
- Affiliation strip shows
- Publications render via jekyll-scholar
- Research mosaic cards display
- Teaching grid with Thinking with Agents callout
- CV download button
- Nav works
- Footer renders

- [ ] **Step 4: Commit**

```bash
git add _layouts/about.html _pages/about.md
git commit -m "feat: Global Mosaic homepage layout

Split hero with field photo, affiliation/geography strip,
publications via jekyll-scholar, research mosaic cards,
teaching grid with Thinking with Agents callout, CV download."
```

---

## Task 10: Inner Page Layout

**Files:**
- Modify: `_layouts/page.html`

The inner pages (publications, works in progress, teaching) use `page.html`. Apply the new styling wrapper.

- [ ] **Step 1: Rewrite `_layouts/page.html`**

```html
---
layout: default
---

<div class="page-content">
  <div class="page-header">
    <h1>{{ page.title }}</h1>
    {% if page.description %}<p class="page-description">{{ page.description }}</p>{% endif %}
  </div>

  <div class="page-body">
    {{ content }}
  </div>
</div>
```

- [ ] **Step 2: Verify inner pages**

```bash
cd ~/Dropbox/Github/eabeam.github.io
bundle exec jekyll serve --trace
```

Check:
- http://localhost:4000/publications/ — publications render with new styles
- http://localhost:4000/works-in-progress/ — works in progress page looks good
- http://localhost:4000/teaching/ — teaching page looks good
- http://localhost:4000/cv/ — CV page renders

- [ ] **Step 3: Commit**

```bash
git add _layouts/page.html
git commit -m "feat: restyle inner page layout for Global Mosaic"
```

---

## Task 11: Cleanup and Polish

**Files:** various

- [ ] **Step 1: Remove unused JS files**

```bash
cd ~/Dropbox/Github/eabeam.github.io
rm assets/js/theme.js assets/js/dark_mode.js assets/js/masonry.js assets/js/zoom.js
```

Keep `assets/js/common.js` only if referenced. Check:

```bash
grep -r "common.js" _includes/ _layouts/
```

If not referenced, remove it too.

- [ ] **Step 2: Remove unused script includes**

```bash
rm _includes/scripts/bootstrap.html _includes/scripts/masonry.html _includes/scripts/misc.html _includes/scripts/progressBar.html
```

Keep `_includes/scripts/mathjax.html` (used for math) and `_includes/scripts/jquery.html` (may still be useful).

- [ ] **Step 3: Test full build**

```bash
cd ~/Dropbox/Github/eabeam.github.io
bundle exec jekyll build --trace
```

Verify no errors.

- [ ] **Step 4: Serve and visual review**

```bash
bundle exec jekyll serve
```

Full checklist:
- [ ] Homepage hero renders correctly
- [ ] Nav links work (scroll to sections on homepage, navigate to inner pages)
- [ ] "Thinking with Agents" button in nav links to external site
- [ ] Affiliation chips link correctly
- [ ] Geography dots appear with correct colors
- [ ] Publications render via BibTeX with abstract/bibtex toggle
- [ ] Photo break displays
- [ ] Research mosaic cards show with correct status badges
- [ ] Teaching grid renders, semester links work
- [ ] Thinking with Agents callout in teaching section
- [ ] CV download links to PDF
- [ ] Footer links work
- [ ] Mobile responsive (resize browser)
- [ ] Inner pages styled consistently

- [ ] **Step 5: Commit cleanup**

```bash
git add -A
git commit -m "chore: remove unused al-folio JS and script includes"
```

---

## Task 12: Final Review and Merge Readiness

- [ ] **Step 1: Review all changes**

```bash
git log --oneline redesign/global-mosaic ^main
git diff main...redesign/global-mosaic --stat
```

- [ ] **Step 2: Push branch**

```bash
git push -u origin redesign/global-mosaic
```

- [ ] **Step 3: Decide on merge**

Options:
1. **Create PR** for review before merging to main
2. **Merge directly** if satisfied with local preview
3. **Deploy preview** — push to a separate branch that GitHub Pages can serve for preview

Recommend: Create a PR so you can preview the deployed version before it goes live.

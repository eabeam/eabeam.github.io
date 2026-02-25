# Site Editing & Local Preview Guide

This is your quick-start guide to edit and preview your academic website locally. The site is built with **Jekyll** (using the **al-folio** theme) and hosted on GitHub Pages.

---

## Site Structure

```
.
├── _config.yml          # Site configuration (title, name, social links, etc.)
├── _pages/              # Main content pages (edit these to update your site)
│   ├── about.md         # Home page
│   ├── cv.md            # CV/Resume page
│   ├── publications.md  # Publications page
│   ├── teaching.md      # Teaching page
│   └── working.md       # Working papers page
├── _data/               # Structured data (YAML)
│   ├── cv.yml           # CV content (education, experience, etc.)
│   ├── coauthors.yml    # Coauthor name formatting
│   └── repositories.yml # GitHub repo listings
├── _bibliography/       # Bibliographies
│   └── papers.bib       # BibTeX file for publications
├── assets/              # Static files (images, PDFs, etc.)
│   ├── img/             # Images (profile picture, etc.)
│   └── pdf/             # PDFs (CV, papers, etc.)
└── _includes/           # HTML components (rarely need editing)
```

---

## Quick Editing

### Edit a Page
1. Open the `.md` file in `_pages/` you want to change
   - Example: `_pages/about.md` is your home page
2. Edit the content below the front matter (the `---` section at the top)
3. Save the file
4. Preview locally (see below)

### Edit the Config
- `_config.yml` controls site-wide settings:
  - `title`, `first_name`, `last_name`, `email`
  - Social media links
  - Theme options

### Add a CV from YAML
- Edit `_data/cv.yml` to update education, experience, skills
- The page at `_pages/cv.md` will render it automatically

### Add Publications
- Add entries to `_bibliography/papers.bib` (BibTeX format)
- Marked entries with `selected={true}` will show on the about page
- The `_pages/publications.md` page shows all publications

---

## Preview Locally

You have two options:

### Option 1: Docker (Easiest, recommended)

If you have Docker installed:

```bash
cd /Users/ebeam/Dropbox/Github/eabeam.github.io
docker-compose up
```

Then open: **http://localhost:8080**

The site will auto-reload as you edit files.

To stop: `Ctrl+C`

### Option 2: Native Jekyll (requires Ruby + gems)

If you prefer running Jekyll directly:

```bash
cd /Users/ebeam/Dropbox/Github/eabeam.github.io

# Install dependencies (first time only)
bundle install

# Start the server
bundle exec jekyll serve
```

Then open: **http://localhost:4000**

The site will auto-reload as you edit files.

To stop: `Ctrl+C`

---

## Common Tasks

### Update Your Profile Picture
1. Place image in `assets/img/`
2. Update the filename in `_pages/about.md` (the `image:` field in the front matter)

### Add a CV PDF
1. Place PDF in `assets/pdf/`
2. Link to it in a page or from `_config.yml`

### Change Site Title or Name
- Edit `_config.yml`:
  - `title`, `first_name`, `last_name`, `email`

### Publish Changes
```bash
git add .
git commit -m "Update: [describe changes]"
git push
```

The site will build and deploy automatically (takes ~1 minute).

---

## Front Matter Basics

Every `.md` page starts with YAML front matter (between `---` lines):

```yaml
---
layout: about          # Page layout template
title: about           # Page title
permalink: /           # URL path
subtitle: Your subtitle
news: false            # Show news section?
selected_papers: false # Show selected papers?
social: false          # Show social icons?
---
```

Common layouts:
- `about` — home page
- `page` — standard content page
- `post` — blog post

---

## Troubleshooting

**Port already in use?**
- Docker: change port in `docker-compose.yml` (first `8080:8080`)
- Jekyll: add `--port 4001` to the serve command

**Changes not showing up?**
- Ctrl+C to stop the server
- Clear browser cache (Cmd+Shift+Delete)
- Restart the server

**Ruby/bundler issues?**
- Use Docker instead (Option 1)
- Or check that you have Ruby 2.6+ installed: `ruby --version`

---

## More Info

- **al-folio theme**: https://github.com/alshedivat/al-folio
- **Jekyll docs**: https://jekyllrb.com/docs/
- **Markdown reference**: https://www.markdownguide.org/

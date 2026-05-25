# CLAUDE.md — zcshiner.github.io

This file documents the project for use in AI-assisted working sessions. It covers the technical setup, file structure, conventions, and common tasks so Claude can assist effectively without re-discovering context each session.

---

## Project Overview

This is Zach Shiner's personal portfolio website. It showcases professional work, patents, and contact information.

- **GitHub repo:** https://github.com/zcshiner/zcshiner.github.io
- **Live site:** https://zcshiner.com (custom domain; currently redirecting from a Wix site — migration in progress)
- **GitHub Pages URL:** https://zcshiner.github.io
- **Owner/author:** Zach Shiner (email@example.com)

Zach is responsible for all content. Claude assists with technical implementation: styling, layout, new features/pages, and debugging.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Static site generator | [Jekyll](https://jekyllrb.com/) |
| Dependency management | `github-pages` gem (~> 232), which pins Jekyll and all plugins to GitHub Pages-compatible versions |
| Theme | `jekyll/minima` remote theme, pinned to commit `c6e74bb` |
| Markdown processor | kramdown |
| Plugins | `jekyll-feed` (generates `/feed.xml`) |
| Hosting | GitHub Pages (auto-build on push to `main`) |
| Local dev | Jekyll running inside a custom Docker container |

The `github-pages` gem controls all plugin and Jekyll versions. **Do not add plugins that aren't on the [GitHub Pages supported plugins list](https://pages.github.com/versions/).** Unsupported plugins will build locally but silently fail on GitHub Pages.

---

## Local Development

### Docker image

A custom Docker image is used to match the GitHub Pages environment. It is defined in `Dockerfile` and uses `ruby:3.4.8-alpine` with the gems from `Gemfile` installed via Bundler. The image follows the approach documented at [rockstorm101/jekyll-docker](https://github.com/rockstorm101/jekyll-docker/tree/master).

**Build the image (only needed once, or after Gemfile changes):**
```bash
docker build -t jekyll-z .
```

**Serve the site locally:**
```bash
docker run --rm \
    -v ${PWD}:/srv/jekyll \
    -u $(id -u):$(id -g) \
    -p 4000:4000 \
    jekyll-z serve --host 0.0.0.0
```

The site is then available at **http://localhost:4000**.

Jekyll watches for file changes and rebuilds automatically. The `_site/` directory is the build output — never edit files there directly.

### Notes
- The `-u $(id -u):$(id -g)` flag runs Jekyll as the current user to avoid file permission issues with the volume mount.
- If you change `Gemfile`, rebuild the Docker image before serving.
- `_site/` is gitignored — it is generated on build and should not be committed.

---

## File Structure

```
zcshiner.github.io/
├── _config.yml          # Site-wide configuration (title, theme, plugins, nav, etc.)
├── Gemfile              # Ruby gem dependencies (github-pages gem)
├── Gemfile.lock         # Locked gem versions
├── Dockerfile           # Custom Jekyll Docker image definition
│
├── index.md             # Home page (uses `page` layout, has custom inline CSS)
├── projects.md          # Projects page (uses `home` layout to auto-list posts)
├── patents.md           # Patents page (static table of issued patents)
├── contact.md           # Contact page (simple mailto link)
│
├── _layouts/            # Local overrides of Minima theme layouts
│   ├── home.html        # Projects listing — renders featured image when post.image is set
│   └── post.html        # Individual post page — renders featured image when page.image is set
│
├── _posts/              # Project write-ups and blog posts (Markdown)
│   └── YYYY-MM-DD-title.md
│
├── assets/
│   └── main.scss        # Global stylesheet — imports minima, adds post image and other shared styles
│                        # IMPORTANT: Minima links /assets/main.css, so this must be assets/main.scss
│
├── images/              # Site images (referenced as /images/filename.jpg)
│
└── _site/               # !! BUILD OUTPUT — do not edit, gitignored !!
```

### Key directories to add if needed
- `_includes/` — override Minima's partials (e.g., `header.html`, `footer.html`)
- `_sass/` — override or extend Minima's stylesheets with additional partials

---

## Site Architecture

### Navigation

Controlled by `header_pages` in `_config.yml`. The current nav order is:

1. Home (`index.md`)
2. Projects (`projects.md`)
3. Patents (`patents.md`)
4. Email Me (`contact.md`)

To add a page to the nav, create the `.md` file and add it to the `header_pages` list in `_config.yml`.

### Posts → Projects page

`projects.md` uses the `home` layout, which automatically lists Jekyll posts. Each project is a Markdown file in `_posts/` with:

```yaml
---
layout: post
title: "Project Title"
date: YYYY-MM-DD HH:MM:SS +0000
categories: projects
image: /images/filename.jpg      # optional — featured image in listing and at top of post
description: "Short summary"     # optional — shown as subtitle on the project card
project_type: "Software"         # optional — label shown on the project card
location: "City, State"          # optional — shown with a 📍 pin on the project card
---
```

Posts are listed in reverse chronological order. The permalink pattern is `/projects/:title` (set in `_config.yml` under `collections.posts.permalink`).

### Collections config

```yaml
collections:
  posts:
    output: true
    permalink: /projects/:title
```

---

## Theme & Styling

The site uses the `jekyll/minima` remote theme pinned to commit `c6e74bb`:

```yaml
remote_theme: "jekyll/minima@c6e74bb"
```

### Current approach: inline CSS

Style overrides are currently written as `<style>` blocks directly in individual page files (see `index.md` and `patents.md`). This is fine for targeted, page-specific overrides.

### Proper Minima overrides (if needed)

To override theme layouts, includes, or styles globally, create the corresponding file locally — Jekyll will use the local version instead of the theme's:

- **Layouts:** `_layouts/page.html`, `_layouts/home.html`, etc.
- **Includes:** `_includes/header.html`, `_includes/footer.html`, etc.
- **Sass/CSS:** Create `assets/css/style.scss` with:
  ```scss
  ---
  ---
  @import "minima";
  // your overrides below
  ```
  Then add partials to `_sass/` (e.g., `_sass/custom.scss` and `@import "custom"` in style.scss).

To inspect what the theme provides, browse the source at: https://github.com/jekyll/minima/tree/c6e74bb

---

## GitHub Pages Deployment

Pushing to the `main` branch triggers an automatic build and deploy on GitHub Pages. There is no manual deploy step.

### Custom domain

The target custom domain is **zcshiner.com**. To configure this:
1. Add a `CNAME` file to the repo root containing `zcshiner.com`
2. Configure DNS with the domain registrar to point to GitHub Pages (see [GitHub Pages custom domain docs](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site))
3. Enable "Enforce HTTPS" in the repo's GitHub Pages settings

The current Wix redirect will need to be removed once the DNS cutover is complete.

### Plugin constraints

Only plugins on the [GitHub Pages supported list](https://pages.github.com/versions/) will run in production. Currently active: `jekyll-feed`. Any new plugin must be on that list or it will be silently ignored on deploy.

---

## Common Tasks

### Add a new project post
1. Create `_posts/YYYY-MM-DD-project-name.md`
2. Include front matter: `layout: post`, `title`, `date`, `categories: projects`
3. Optionally add any of these fields to the front matter:
   - `image: /images/filename.jpg` — appears above the post title in the Projects listing and at the top of the post page
   - `description: "Short summary"` — shown as a subtitle on the project card
   - `project_type: "Software"` — label displayed on the project card
   - `location: "City, State"` — shown with a 📍 pin on the project card
4. Drop any image file in `images/`
5. Write content in Markdown below the front matter
6. The post will automatically appear on the Projects page

### Add a new nav page
1. Create `pagename.md` with `layout: page`, `title`, and optionally `permalink`
2. Add the filename to `header_pages` in `_config.yml`

### Change site metadata
Edit `_config.yml` — `title`, `author`, `description`, `url`, `linkedin_username`, `github_username`

### Add an image
Drop the file in `images/` and reference it as `/images/filename.jpg` in Markdown or HTML

### Rebuild the Docker image
Run `docker build -t jekyll-z .` from the project root after any `Gemfile` change

---

## Known TODOs / In Progress

- [ ] Replace placeholder contact email in `contact.md` (currently `email@example.com`)
- [ ] Clean up or replace the placeholder `_posts/2026-02-08-welcome-to-jekyll.md` post
- [ ] Migrate custom domain from Wix to GitHub Pages (`CNAME` file + DNS update)
- [ ] Populate real project content in `_posts/`

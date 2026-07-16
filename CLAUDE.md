# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal homepage and blog for Benedict Weichselbaum, built with **Jekyll 3.9** and deployed via **GitHub Pages** (repo name `benedictweichselbaum.github.io`). The custom domain is set in `CNAME` (`benedict-weichselbaum.de`). The `minima` gem is still listed in the `Gemfile`/`_config.yml`, but the site uses its **own custom layouts, includes and CSS** — minima's layouts and styles are fully overridden and effectively unused.

## Commands

```bash
bundle install                 # install gems (first run / after Gemfile changes)
bundle exec jekyll serve       # local dev server with live reload at http://localhost:4000
bundle exec jekyll build       # build static site into _site/
```

There are no tests or linters. Deployment is automatic: pushing to the default branch triggers a GitHub Pages build. `_site/` is the generated output — it is committed here but gitignored elsewhere; do not hand-edit it, it is overwritten on build.

## Architecture

Standard Jekyll conventions drive everything — there is no custom build logic. All presentation lives in this repo's own theme files.

- **Layouts** (`_layouts/`): `default` is the HTML skeleton (pulls in head/header/footer); `home`, `page`, `post`, and `blog` extend it. `home` renders the hero (driven by front matter — `hero_role`, `hero_tagline`, `skills`, `photo`, etc.), the intro prose, and the latest-posts cards. `blog` lists every post (used by `writing.markdown`).
- **Includes** (`_includes/`): `head.html` (meta, SEO, feed, favicons, and the pre-paint theme script), `header.html` (nav + light/dark toggle), `footer.html`.
- **Styling**: a single hand-written **`assets/css/main.css`** — a design-token system using CSS custom properties. Light is the `:root` default; dark is `:root[data-theme="dark"]` plus a `prefers-color-scheme` media-query fallback for no-JS. A small inline script in `head.html` sets `data-theme` before first paint (reads `localStorage.theme`, else the OS preference) to avoid a flash; the nav toggle persists the choice. **No external fonts/CDNs** — deliberately, to keep the German Impressum GDPR-clean (a system font stack is used).
- **Content pages** are top-level Markdown files (`index.markdown` → `home`; `cv`, `contact`, `imprint` → `page`; `writing` → `blog`). Front matter selects the `layout` and optional `lead`.
- **Blog posts** live in `_posts/` named `YYYY-MM-DD-title.markdown` with `layout: post`. Front matter sets `title`, `date`, `categories` (space-separated — they become the URL path, e.g. `categories: photography` → `/photography/2024/07/05/...`) and a `description` (used for the post-card blurb and the SEO meta description).
- **`_config.yml`** holds site settings and plugins: `jekyll-feed` (RSS at `/feed.xml`), `jekyll-seo-tag`, `jekyll-sitemap`. Changes here require restarting the dev server — it is not hot-reloaded.
- **`resources/`** holds images, with per-post subfolders (e.g. `resources/post_flightradar/`).

## Conventions

- **Write posts in pure Markdown** — this is a hard product requirement. Use `![alt](/resources/...)` for images; `.post-content img` styles them responsively (rounded, bordered). Do not reintroduce inline `<div style=...>` image wrappers.
- When adding a post, create the dated file in `_posts/`, put its images under `resources/post_<name>/`, and include a `description` in the front matter.
- The CV (`cv.markdown`) uses light structural HTML with the theme's classes (`.timeline`, `.skill-card`, `.chip`, etc.) — reuse those classes rather than inventing new markup.

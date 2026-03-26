# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Local development server
hugo server

# Build the site
hugo

# Create a new post (English)
hugo new content/english/posts/YYYY-MM-DD-slug.md

# Create a new post (Polish)
hugo new content/polski/posts/YYYY-MM-DD-slug.md
```

## Architecture

This is a **Hugo static site** for Slawomir Jasinski's personal blog, deployed to GitHub Pages automatically on push to `main` via GitHub Actions.

**Bilingual setup:** Two languages are configured in `config.toml`:
- English (`/`) → content in `content/english/`
- Polish (`/pl/`) → content in `content/polski/`

**Theme:** `themes/anatole` (git submodule). Customizations live in `layouts/` (overrides) and `assets/css/custom.css`.

**Content frontmatter pattern** (from existing posts):
```yaml
---
title: Post Title
author: slav
type: post
date: 2024-01-01T09:00:00+00:00
url: /2024/slug-here
thumbnail: /images/2024/01/image.jpg
description: Short description for SEO.
categories:
 - Blog
 - Work
---
```

The `url` field overrides Hugo's default permalink — match the pattern `/YYYY/slug` for English posts.

**Custom shortcodes** in `layouts/shortcodes/`: `embed.html`, `gist.html`, `video.html`.

**Static assets** (images, PDFs) go in `static/` and are referenced with absolute paths starting from `/`.

**Built output** lives in `public/` — this is what gets deployed, but it's committed to the repo as the deploy target for GitHub Pages.

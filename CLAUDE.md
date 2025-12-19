# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Jekyll-based blog deployed to GitHub Pages. It uses the Minima theme with custom layouts and styling.

## Development Commands

### Local Development
```bash
# Install dependencies
bundle install

# Serve the site locally with live reload
bundle exec jekyll serve

# Serve with drafts visible
bundle exec jekyll serve --drafts

# Build the site (output to _site/)
bundle exec jekyll build
```

### Deployment
Deployment is automated via GitHub Actions (`.github/workflows/jekyll-gh-pages.yml`). Pushing to the `main` branch triggers a build and deployment to GitHub Pages.

## Project Structure

- `_config.yml` - Jekyll configuration (site title, author, theme, plugins, permalink structure)
- `_layouts/` - HTML templates
  - `default.html` - Base layout with header/nav, footer, and SEO tags
  - `post.html` - Blog post layout with metadata and tags
- `_posts/` - Blog posts in Markdown format
  - Filename format: `YYYY-MM-DD-title-with-hyphens.md`
  - Front matter required: `layout`, `title`, `date`
  - Optional: `author`, `tags`, `excerpt`
- `_includes/` - Reusable HTML snippets
- `_sass/` - Sass stylesheets
- `assets/css/` - Compiled CSS
- `assets/images/` - Image files
- `index.html` - Homepage that lists all posts with excerpts

## Creating New Blog Posts

1. Create a file in `_posts/` with format: `YYYY-MM-DD-title.md`
2. Add front matter at the top:
```yaml
---
layout: post
title: "Your Post Title"
date: YYYY-MM-DD HH:MM:SS
tags: [tag1, tag2]
---
```
3. Write content in Markdown below the front matter

## Adding Images to Posts

When adding images to blog posts:

1. Place image files in `assets/images/` directory
2. Use HTML `<img>` tag (not Markdown syntax) to ensure proper width control
3. Always include `style="max-width: 100%; height: auto;"` to prevent overflow on smaller screens

Example:
```html
<img src="/assets/images/your-image.png" alt="Descriptive alt text" style="max-width: 100%; height: auto;">
```

## Theme and Styling

- Uses Minima theme (version ~> 2.5) as base
- Custom layouts override theme defaults
- Custom CSS in `assets/css/main.css`
- SEO plugin (`jekyll-seo-tag`) and RSS feed (`jekyll-feed`) are enabled

## Plugins

- `jekyll-feed` - RSS feed generation
- `jekyll-seo-tag` - SEO meta tags
- `jekyll-paginate` - Post pagination (configured for 5 posts per page)

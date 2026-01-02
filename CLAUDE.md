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
date: YYYY-MM-DD
tags: [tag1, tag2]
excerpt: "A compelling 1-2 sentence summary that appears in post lists and SEO descriptions."
image: /assets/images/descriptive-filename.webp
---
```
3. Write content in Markdown below the front matter

**Important Notes**:
- Use date-only format (YYYY-MM-DD) without time in the front matter
- Always include an `excerpt` - it appears in the post list and improves SEO
- Always include an `image` - it serves as the thumbnail in post lists

## Adding Images to Posts

### Image Preparation

1. **Convert to WebP format** for optimal performance:
```bash
cwebp input-image.png -o assets/images/descriptive-name.webp -q 85
```

2. **Use descriptive filenames** that relate to the content (e.g., `locks-exclusive-promise.webp`, `bff-architecture.webp`, not `unnamed.webp` or `image1.webp`)

3. **Place image files** in `assets/images/` directory

### Adding Images to Post Content

1. Use HTML `<img>` tag (not Markdown syntax) to ensure proper width control
2. Always include `style="max-width: 100%; height: auto;"` to prevent overflow on smaller screens
3. Write descriptive alt text for accessibility and SEO

Example:
```html
<img src="/assets/images/your-image.webp" alt="Descriptive explanation of what the image shows" style="max-width: 100%; height: auto;">
```

### Adding Thumbnail Images

Add the `image` field to your post's front matter:
```yaml
image: /assets/images/your-image.webp
```

This image will:
- Display as the thumbnail in post lists on the homepage
- Be used for social media previews (Open Graph, Twitter Cards)
- Improve the visual appeal of your blog

## Theme and Styling

- Uses Minima theme (version ~> 2.5) as base
- Custom layouts override theme defaults
- Custom CSS in `assets/css/main.css`
- SEO plugin (`jekyll-seo-tag`) and RSS feed (`jekyll-feed`) are enabled

## Plugins

- `jekyll-feed` - RSS feed generation
- `jekyll-seo-tag` - SEO meta tags
- `jekyll-paginate` - Post pagination (configured for 5 posts per page)

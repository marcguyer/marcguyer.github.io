# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This repository contains a personal website/blog for Marc Guyer, built with the Hugo static site generator. The site is accessible at https://marc.guyer.me.

## Site Structure

- The site uses Hugo with multiple themes:
  - Primary theme: "beautifulhugo"
  - Additional theme: "hugo-cloak-email" (for email address protection)
- Content is organized in markdown files within the `content/` directory
- Blog posts are stored in `content/posts/`

## Hugo Commands

To work effectively with this Hugo site, here are the key commands:

- **Start local development server:**
  ```
  hugo server -D
  ```

- **Build the site for production:**
  ```
  hugo
  ```

- **Create a new post:**
  ```
  hugo new posts/my-new-post-name.md
  ```

- **Create a new page:**
  ```
  hugo new page-name.md
  ```

## Site Configuration

The site configuration is in `config.toml` with these notable settings:

- Base URL: marc.guyer.me
- Themes: hugo-cloak-email and beautifulhugo
- Git integration is enabled
- Disqus comments are configured with shortname "marcguyer"
- Google Analytics is enabled

## Content Guidelines

When working with the content:

- Markdown files in `content/` directory use Hugo frontmatter
- Code highlighting is enabled with code fences using Monokai style
- The site supports custom shortcodes:
  - `cloakemail` for protecting email addresses (custom JavaScript-based implementation)
  - `githubfile` for embedding GitHub repository files with syntax highlighting
  - `collapse` for collapsible content sections

## Custom Layouts and Overrides

The site uses custom layouts in the `layouts/` directory:

- Custom shortcodes in `layouts/shortcodes/`
- Custom partials in `layouts/partials/` for Google Analytics, favicons, and head sections
- Post-specific layouts in `layouts/posts/`

## Static Assets

- Custom CSS files are stored in `static/css/` and configured in `config.toml`
- Site favicon and logo use Gravatar with hash `e96773ada2643a59cf32d6e1407a7cc8`
- The `public/` directory is generated and should not be edited directly

## Theme Architecture

The site uses a dual-theme approach:
1. `hugo-cloak-email` theme component (loaded first) provides email cloaking functionality
2. `beautifulhugo` theme (primary theme) provides the main site layout and styling

When making theme customizations, prefer overriding layouts in the root `layouts/` directory rather than modifying theme files directly.

## Deployment

The site is published to GitHub Pages (inferred from the repository name marcguyer.github.io). Changes pushed to the main branch will be automatically deployed.
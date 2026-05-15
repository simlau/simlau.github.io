# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal portfolio/consulting website for Simon B. Laursen, hosted on GitHub Pages at `simlau.dk`. Built with Jekyll using a fully custom layout — no theme. The site is a single-page layout deployed automatically by GitHub Pages on every push to `main`.

## Local Development

Local serving requires Ruby 3+ with Bundler. The macOS system Ruby (2.6) cannot install the required gems. Install a modern Ruby first:

```bash
brew install ruby
# then add brew ruby to PATH, then:
bundle install
bundle exec jekyll serve
```

GitHub Pages builds and deploys automatically on push — local serving is optional.

## Architecture

- `_config.yml` — Jekyll config: site title, description, and the `jekyll-sitemap` plugin. No theme specified.
- `_layouts/default.html` — The entire site: CSS, sidebar, and all six content sections (About, Experience, Skills, Education, Conferences, Contact). **This is the only file to edit for content or style changes.**
- `index.md` — Minimal front-matter trigger (`layout: default`, `title`). Contains no content.
- `CNAME` — Custom domain `simlau.dk` for GitHub Pages.
- `favicon.ico` — Site favicon, referenced in the layout `<head>`.

## Content Updates

All page content lives in `_layouts/default.html`. To update an experience entry, skill tag, or any copy — edit that file directly. Sections are clearly commented (`<!-- ABOUT -->`, `<!-- EXPERIENCE -->`, etc.).

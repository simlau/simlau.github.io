# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal portfolio/consulting website for Simon B. Laursen, hosted on GitHub Pages at `simlau.dk`. Built with Jekyll using a fully custom layout — no theme. The site has two pages, English at `/` and Danish at `/da/`, deployed automatically by GitHub Pages on every push to `main`.

## Where the content comes from

The content is generated from `cv.yaml` in `../cv` (the CV's single source of truth) by `cvtool site push`, which writes, commits, and pushes two files here:

- `_data/cv.json` — both pages' content (`en`, `da`), already resolved: dates, titles, technology lines, labels.
- `Simon-B-Laursen-CV.pdf` — the full CV, linked from both pages.

Never edit these two files by hand; edit `cv.yaml` (its `short` block and the entries' `short` texts) and run, in `../cv`:

```sh
go run ./cmd/cvtool site diff   # what would change here
go run ./cmd/cvtool site push   # asks y/N, commits here, pushes (publishes simlau.dk)
```

`site push` refuses while this repo has uncommitted changes or is not on `main`. Local commits (e.g. layout changes) are listed in the diff and pushed together.

## Local Development

Local serving requires Ruby 3+ with Jekyll and `jekyll-sitemap`. To build with the current data without committing it:

```sh
go run ./cmd/cvtool site diff --dump ../simlau.github.io   # in ../cv; writes _data/cv.json and the PDF here, untracked
jekyll build -s . -d /tmp/site                              # then delete the two dumped files again
```

GitHub Pages builds and deploys automatically on push — local serving is optional.

## Architecture

- `_config.yml` — Jekyll config: site title, the `jekyll-sitemap` plugin, and `include` for the CV PDF (all other PDFs stay excluded, e.g. the letter of recommendation).
- `assets/site.css` — all CSS. Keep it out of the layout: see Security headers.
- `_layouts/default.html` — CSS and structure for both pages. It renders everything from `site.data.cv[page.lang]` with Liquid; every string goes through `| escape` (JSON-LD uses `| jsonify`). **Edit this file for structure and style only.**
- `index.md` (`lang: en`) and `da/index.md` (`lang: da`, `permalink: /da/`) — front matter only.
- `og-image-v5.png` — share image with the title; regenerate it by hand if the title in `cv.yaml` changes.
- `CNAME` — Custom domain `simlau.dk` for GitHub Pages.
- `favicon.ico` — Site favicon, referenced in the layout `<head>`.

## Security headers

GitHub Pages cannot set headers, so they are set in Cloudflare (zone simlau.dk, Rules → Transform Rules → Response Header, rule "Security headers", hosts `simlau.dk` and `www.simlau.dk` only; `foundry.` and `jeppe.` are other apps in the same zone). securityheaders.com: A+ (2026-11-26).

The CSP is `default-src 'none'; script-src 'self'; style-src 'self'; img-src 'self'; base-uri 'none'; form-action 'none'; frame-ancestors 'none'; upgrade-insecure-requests`. That means:

- No inline `<style>`, `style="…"`, `<script>` with code, or `on…=` handlers. CSS goes in `assets/site.css`. JSON-LD (`type="application/ld+json"`) is fine.
- No external fonts, images, scripts, or analytics without widening the CSP in Cloudflare first.
- HSTS has no `includeSubDomains`: `link.simlau.dk` (smtp2go click tracking) is DNS-only and may use plain HTTP.

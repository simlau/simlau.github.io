# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

Personal portfolio/consulting website for Simon B. Laursen, hosted on GitHub Pages at `simlau.dk`. Built with Jekyll using a fully custom layout — no theme. The site has two pages, English at `/` and Danish at `/da/`, deployed automatically by GitHub Pages on every push to `main`.

## Where the content comes from

The content is generated from `cv.yaml` in `../CV-Normal-Long` (the CV's single source of truth) by `cvtool site push`, which writes, commits, and pushes two files here:

- `_data/cv.json` — both pages' content (`en`, `da`), already resolved: dates, titles, technology lines, labels.
- `Simon-B-Laursen-CV.pdf` — the full CV, linked from both pages.

Never edit these two files by hand; edit `cv.yaml` (its `short` block and the entries' `short` texts) and run, in `../CV-Normal-Long`:

```sh
go run ./cmd/cvtool site diff   # what would change here
go run ./cmd/cvtool site push   # asks y/N, commits here, pushes (publishes simlau.dk)
```

`site push` refuses while this repo has uncommitted changes or is not on `main`. Local commits (e.g. layout changes) are listed in the diff and pushed together.

## Local Development

Local serving requires Ruby 3+ with Jekyll and `jekyll-sitemap`. To build with the current data without committing it:

```sh
go run ./cmd/cvtool site diff --dump ../simlau.github.io   # in ../CV-Normal-Long; writes _data/cv.json and the PDF here, untracked
jekyll build -s . -d /tmp/site                              # then delete the two dumped files again
```

GitHub Pages builds and deploys automatically on push — local serving is optional.

## Architecture

- `_config.yml` — Jekyll config: site title, the `jekyll-sitemap` plugin, and `include` for the CV PDF (all other PDFs stay excluded, e.g. the letter of recommendation).
- `_layouts/default.html` — CSS and structure for both pages. It renders everything from `site.data.cv[page.lang]` with Liquid; every string goes through `| escape` (JSON-LD uses `| jsonify`). **Edit this file for structure and style only.**
- `index.md` (`lang: en`) and `da/index.md` (`lang: da`, `permalink: /da/`) — front matter only.
- `og-image-v4.png` — share image with the title; regenerate it by hand if the title in `cv.yaml` changes.
- `CNAME` — Custom domain `simlau.dk` for GitHub Pages.
- `favicon.ico` — Site favicon, referenced in the layout `<head>`.

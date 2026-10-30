# fahmi-nugroho.github.io

Personal site: portfolio, writing, projects. Astro, static, deployed to GitHub Pages on push to
`main`.

## Writing a post

Create `src/content/writing/<slug>.md`:

```md
---
title: One machine, two Git identities
description: One sentence that appears in listings and RSS.
date: 2026-10-05
tags: ['git', 'ssh']
lang: en          # 'id' for an Indonesian post
draft: true       # remove or set false to publish
---

Body in Markdown.
```

Commit and push. The GitHub Action at `.github/workflows/deploy.yml` builds and deploys; a commit
made through the GitHub web UI deploys the same way.

## Local development

```sh
npm install
npm run dev      # http://localhost:4321
npm run build    # static output in dist/
```

## Analytics

Analytics is off until `GOATCOUNTER_CODE` in `src/layouts/Base.astro` is set to a GoatCounter site
code.

## Adding a project

Create `src/content/projects/<slug>.md` with `title`, `description`, `stack`, `repo`, and `order`.
Projects appear on `/projects/` sorted by `order`.

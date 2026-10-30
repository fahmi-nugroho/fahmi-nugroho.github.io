# fahmi-nugroho.github.io

Personal site: portfolio, writing, projects. Astro, static, deployed to GitHub Pages on push to
`main`.

## The short version

1. Edit or add a file (see the table below).
2. Check it locally with `npm run dev` (http://localhost:4321).
3. Commit and push to `main`. The GitHub Action builds and deploys in about a minute.

| I want to change…           | Edit this                                                  |
| --------------------------- | ---------------------------------------------------------- |
| A post                      | `src/content/writing/<slug>.md`                            |
| A project                   | `src/content/projects/<slug>.md`                           |
| The About page              | `src/pages/about.astro`                                    |
| The homepage text           | `src/pages/index.astro`                                    |
| Nav links, footer links     | `src/layouts/Base.astro` (`nav` array, `<footer>`)         |
| Analytics                   | `GOATCOUNTER_CODE` in `src/layouts/Base.astro`             |
| Colours, fonts, spacing     | `src/styles/global.css` (variables at the top)             |
| Favicon                     | `public/favicon.svg`                                       |

## Posts (`src/content/writing/`)

### Add

Create `src/content/writing/<slug>.md`. The file name is the URL: `my-post.md` becomes
`/writing/my-post/`.

```md
---
title: One machine, two Git identities
description: One sentence that appears in listings and RSS.
date: 2026-10-05
updated: 2026-10-12   # optional; shown beside the date
tags: ['git', 'ssh']  # lowercase; each tag gets its own /tags/<tag>/ page automatically
lang: en              # 'id' for an Indonesian post
draft: true           # remove it or set false to publish
---

Body in Markdown. Fenced code blocks get syntax highlighting and a Copy button.
```

`title`, `description` and `date` are required. The build fails with a clear message if one is
missing.

### Edit

Change the file and push. When you fix something substantial in an old post, add `updated:` with
today's date so readers can see it is current.

### Hide or delete

- **Hide for now:** set `draft: true`. The post disappears from the site, the RSS feed and the
  sitemap, and the file stays in the repo.
- **Delete for good:** delete the file. Its URL then shows the 404 page. A tag page disappears once
  its last post is gone.

### What shows where

- The homepage shows the 3 newest posts. `/writing/` lists all of them, grouped by year.
- Dates are shown in UTC, so a date you write is the date readers see.

## Projects (`src/content/projects/`)

### Add

Create `src/content/projects/<slug>.md`:

```md
---
title: Journal
description: What it is and the one decision worth knowing about.
stack: ['Next.js', 'TypeScript', 'PostgreSQL']
repo: https://github.com/fahmi-nugroho/journal-project
demo: https://example.com   # optional; adds a "Live" link
order: 1                    # lowest number is listed first
---
```

`title`, `description` and `repo` are required. The text below the front matter is not shown on the
site; only the fields above are.

### Edit, reorder, delete

- **Edit:** change the fields and push.
- **Reorder:** change `order`. The homepage shows the 2 projects with the lowest `order`;
  `/projects/` shows all of them.
- **Delete:** delete the file.

## About page

`src/pages/about.astro` is a normal page, not a content file. Edit the text between the `<Base>`
tags: paragraphs are `<p>`, the skills list is the `<ul>`, and the links are in the last paragraph.
Keep the `title` and `description` on the `<Base ...>` line up to date, since they appear in search
results and link previews.

## Other pages

- **Homepage text:** the intro (`<h1 class="lede">`) and the line under it are in
  `src/pages/index.astro`.
- **Add a new page:** create `src/pages/<name>.astro`, copy the shape of `about.astro`, and add a
  link to the `nav` array in `src/layouts/Base.astro` if it should appear in the menu.
- **Footer links** (email, GitHub, LinkedIn) are in `src/layouts/Base.astro`.

## Editing without a computer

You can edit any file in the GitHub web editor (press `.` on the repo page, or use the pencil icon).
A commit made there deploys the same way. You can't preview it first, so keep web edits small.

## Local development

```sh
npm install
npm run dev      # http://localhost:4321, reloads as you save
npm run build    # static output in dist/; run this to catch errors before pushing
npm run preview  # serve dist/ locally, including the 404 page
```

## Deploying

Every push to `main` runs `.github/workflows/deploy.yml`. Watch it under the repo's **Actions** tab.
If a build fails, the live site stays on the last good version, and the error is in the Action log.
In the repo's Settings under Pages, the source must be set to **GitHub Actions**.

## Analytics

Analytics uses GoatCounter. The site code is `GOATCOUNTER_CODE` in `src/layouts/Base.astro`; set it
to an empty string to turn analytics off.

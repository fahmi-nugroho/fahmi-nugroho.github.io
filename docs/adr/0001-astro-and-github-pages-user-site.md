# Astro static site, published as a GitHub Pages user site

The site is portfolio plus writing, written by one person, and updated rarely. We chose Astro with
static output over a React framework, a hand-written HTML page, and other generators, because
posts are Markdown files and the output is plain HTML with no runtime JavaScript. We publish from
a repo named `fahmi-nugroho.github.io` so the site owns the bare domain root: GitHub Pages ties the
root URL to that repo name, and a project-site subpath would put `/github-personal-website/` in
every URL on a page meant to be a personal address.

## Consequences

- The 2021 static site and its repo are retired; this repo is the only source of truth, and its
  history starts at the rebuild rather than carrying the old files forward.
- Adding anything dynamic (a contact form, comments, a CMS) needs a third-party service, because
  Pages serves files only.
- Renaming the repo later changes the site URL, so a custom domain is the escape hatch if the
  URL ever needs to be stable independently of GitHub.

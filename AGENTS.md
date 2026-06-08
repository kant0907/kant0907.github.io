# AGENTS.md

## What this branch is

`gh-pages` of `kant0907/kant0907.github.io`. It contains **only the pre-built
static output** of a Jekyll site using the [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy)
theme (v7.5.0, Jekyll 4.4.1). There is one commit, "deploy: 初始部署站点".

There is no source here:

- no `_config.yml`, no `_data/`, no `_includes/`, no `_layouts/`, no `_sass/`
- no `_posts/*.md` or any Markdown — every post is already an `index.html`
- no `Gemfile`, no `package.json`, no lockfile
- no `.github/` workflows, no pre-commit config, no `.cursorrules`, no
  `opencode.json`

`.nojekyll` is present at the repo root, so GitHub Pages is told to serve the
files as-is and **must not** run Jekyll over them.

## What this means for edits

- This is a **deploy artifact**. Editing HTML/CSS/JS in place will be silently
  overwritten the next time the source is built and pushed. Author content in
  the real Jekyll source tree (likely a separate `main` branch or a different
  repo) and regenerate from there.
- Do **not** try `bundle exec jekyll serve`, `npm run …`, `pytest`, or any
  build/lint/test command in this branch — there is nothing to run.
- Do **not** regenerate the search index, sitemap, RSS feed, or service worker
  by hand. They are produced by the Chirpy build (`assets/js/data/search.json`,
  `sitemap.xml`, `feed.xml`, `sw.min.js` + `assets/js/data/swconf.js`).

## Layout worth knowing

- Posts live at `posts/<slug>/index.html` (pretty permalinks, nested per
  post — Chirpy default).
- `categories/` and `tags/` are also nested directories, one folder per term.
- `archives/index.html` is the chronological archive.
- `about/index.html` is a standalone page.
- `assets/css/jekyll-theme-chirpy.css` is the compiled theme stylesheet
  (do not hand-edit; re-build the theme).
- `assets/js/dist/*.min.js` are pre-bundled theme scripts; `assets/js/data/`
  holds generated data files (`search.json`, `mathjax.js`, `swconf.js`).
- `assets/lib/` vendors third-party JS/CSS (mermaid, mathjax, fontawesome,
  glightbox, tocbot, simple-jekyll-search, dayjs, clipboard, loading-attribute-
  polyfill). They are refreshed by `assets/lib/update.sh`, not by hand.

## Site identity (verified from `index.html` and `posts/hello-world/index.html`)

- Canonical URL: `https://kant0907.github.io/`
- Site name / title: `TestBlog`
- Default language: `zh-CN` (Simplified Chinese) — `html lang="zh-CN"`,
  `og:locale: zh_CN`. Keep new pages consistent with this.
- Theme color tags already declare light/dark variants — don't drop them.

## When the user asks to "add a post" or "change the theme"

Confirm with the user where the source Jekyll project lives before doing
anything. The right answer is almost always "edit the source repo and rebuild",
not "edit a file in this branch".

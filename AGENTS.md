# AGENTS.md

## What this repo is

Source Jekyll site for `kant0907/kant0907.github.io` on the `main` branch.
Uses the [Chirpy](https://github.com/cotes2020/jekyll-theme-chirpy) theme
(v7.5.0, Jekyll 4.4.1). GitHub Pages is **not** served from a `gh-pages`
branch — it is built and deployed by
[`.github/workflows/pages-deploy.yml`](.github/workflows/pages-deploy.yml) on
every push to `main`.

This means the repo is the **source of truth**. Editing files here and
pushing is the normal way to change the site.

## What this means for edits

- You can edit `_posts/*.md`, `_config.yml`, theme overrides, assets, etc.
  directly on `main` and push.
- Do **not** hand-edit anything inside `_site/` — it is a build output that
  is regenerated on every workflow run and is git-ignored.
- Do **not** regenerate the search index, sitemap, RSS feed, or service
  worker by hand. They are produced by `bundle exec jekyll build` in CI.
  Their canonical locations in the build output are `assets/js/data/`,
  `sitemap.xml`, `feed.xml`, and `sw.min.js`.

## Layout worth knowing

- `_posts/YYYY-MM-DD-<slug>.md` — published posts. Front matter is required
  (see existing posts for the shape).
- `_drafts/` — drafts. Not built unless `--drafts` is passed; safe staging
  area for in-progress posts.
- `_data/`, `_tabs/`, `_plugins/` — theme/site-config dirs.
- `_site/` — generated site, ephemeral, do not edit.
- `assets/` — user assets (images etc.). The theme's vendored libs live
  under `assets/lib/` and are refreshed by `assets/lib/update.sh`, not by
  hand.
- `.github/workflows/pages-deploy.yml` — build + `htmlproofer` + deploy to
  GitHub Pages. Triggers on push to `main`.

## Site identity (verified from `_config.yml` and existing posts)

- Canonical URL: `https://kant0907.github.io/`
- Site name / title: `TestBlog`
- Default language: `zh-CN` (Simplified Chinese). Keep new pages consistent
  with this. Mixed Chinese/English post bodies are fine.
- Timezone: `Asia/Shanghai`. Use `+0800` in front-matter dates.
- Existing reading notes use `categories: [Reading, Notes]` and 2–4 short
  `tags:`. Match that style unless the post clearly belongs elsewhere.

## Publishing workflow (content → live site)

End-to-end process for shipping a new post. There is no local Jekyll build
step in this repo's flow — the workflow builds in CI, and verification is
done against the live GitHub Pages URL.

### 1. Confirm content

- Author in `_drafts/YYYY-MM-DD-<slug>.md` first. Jekyll skips drafts by
  default, so it is a safe staging area.
- Front matter shape (required keys):
  ```yaml
  ---
  title: <post title>
  date: YYYY-MM-DD HH:MM:SS +0800
  categories: [Reading, Notes]   # match existing posts
  tags: [slug, book, fiction]    # 2–4 short tags
  description: <one-line summary, used in meta and listings>
  ---
  ```
- File name pattern: `YYYY-MM-DD-<slug>.md`. The slug determines the public
  URL: `/posts/<slug>/`.
- For long-form reading notes, follow the structure of
  `_posts/2026-06-08-hillbilly-elegy-reading-notes-day-1.md` (sections,
  bolded terms, short quotes from the source material).

### 2. Publish

- Move the file from `_drafts/` to `_posts/`. On PowerShell:
  ```powershell
  Move-Item -LiteralPath "_drafts\<file>.md" -Destination "_posts\<file>.md"
  ```
- Verify the file is on disk at the new path before committing.

### 3. Commit and push

- Inspect state before committing:
  ```bash
  git status
  git diff
  ```
- Stage only the intended files:
  ```bash
  git add _posts/<file>.md
  ```
- Commit with a short message matching the repo's style. Existing
  convention is `post: <short description>` (e.g. `post: Project Hail Mary
  chapter 1 notes`).
- Push to `main`:
  ```bash
  git push origin main
  ```
- If the push fails with
  `Failed to connect to 127.0.0.1:33210 ...`, the local proxy (set in
  `git config http.proxy`) is down. Either start the VPN/Clash/etc. that
  provides it and retry, or temporarily unset the proxy for this repo:
  ```bash
  git config --unset http.proxy
  git config --unset https.proxy
  ```
  Re-set it after the push if the unset was only meant to be temporary.

### 4. Wait for the workflow and verify the live site

- Watch the run: `gh run list --limit 1` or open the Actions tab. The
  workflow runs `htmlproofer` and may fail on broken internal links (e.g.
  a renamed post slug). If it fails, read the log, fix the link, and push
  again.
- Once the run is `completed / success`, verify the live site by fetching
  three URLs:
  1. The post page: `https://kant0907.github.io/posts/<slug>/`
  2. The homepage: `https://kant0907.github.io/`
  3. The new tag page (if you added a new tag):
     `https://kant0907.github.io/tags/<tag>/`
- On the post page, confirm: title, date, author, reading time, body
  rendered, categories and tags at the bottom.
- On the homepage, confirm the post appears as the **first** card and the
  sidebar lists the new tag and category.
- **Hard refresh the browser** (`Ctrl+Shift+R`) or open a private window
  before declaring it done. The PWA service worker caches the homepage
  and will keep serving the old version for a while after deploy. The
  new post's URL is fetched fresh on first visit, so a missing homepage
  post + visible post URL is the classic symptom of a stale sw cache, not
  a deploy failure.

## Common pitfalls

- **PWA cache hides new content.** Clear site data
  (DevTools → Application → Storage → Clear site data), unregister the
  service worker, or just use a private window to bypass it.
- **`htmlproofer` fails in CI.** Check the run log for the offending URL.
  Usually an old internal link to a renamed post. Fix and push.
- **Push fails with proxy error.** See step 3.
- **404 on a new post URL after a successful deploy.** Confirm the file
  is in `_posts/`, the date in the filename matches the date in the front
  matter, the slug in the filename matches the URL you are checking, and
  the workflow has actually completed (not still queued / failed).
- **Editing `_site/` directly.** It looks tempting because it is what
  GitHub Pages serves, but any change there is wiped on the next
  workflow run. Edit the source and let CI rebuild.

---
title: "Website & Blog Maintenance Guide"
date: 2025-06-01
author: "Frederik Beck"
tags: ["Website"]
summary: "How to maintain the ICCC website and Hugo blog — editing pages, writing posts, testing locally, and deploying via GitHub."
---

This guide explains how to update the ICCC website and blog. No deep coding knowledge needed — most tasks are simple text edits.

---

## Part 1: Main Website

The main website lives at [github.com/iccycling/iccycling.github.io](https://github.com/iccycling/iccycling.github.io). It is a **static HTML site** — no build step, no server, no database.

### Structure

```
iccycling.github.io/
├── index.html        ← Homepage
├── road.html         ← Road cycling
├── offroad.html      ← Off-road / MTB
├── club.html          ← About the club (incl. Get in Touch, Committee link)
├── other.html        ← Other disciplines
├── privacy.html      ← Privacy & Legal
├── main.css          ← All styles
├── main.js            ← Nav, mobile menu, WhatsApp popup behaviour
├── feed.js            ← Fetches news & blog from Hub, renders the homepage widget
├── sitemap.xml        ← For search engines
├── robots.txt          ← For search engine crawlers
├── favicon.ico        ← Browser tab icon
├── og-image.jpg       ← Default social media preview image (1200×630px)
├── fonts/              ← Locally hosted fonts (Syne, Inter)
└── images/             ← All images used on the site
```

### Making Changes

1. Download the repository as a ZIP from GitHub, or clone it with Git
2. Open the relevant `.html` file in any text editor (VSCode recommended)
3. Edit the content directly — headings, text, image paths
4. Open the file in a browser to check it looks right
5. Upload the changed file back to GitHub

The site updates automatically within a minute or two after you push to GitHub.

### Images

Replace images in the `images/` folder and update the `src` attribute in the HTML accordingly. Use web-optimised JPGs — aim for under 300KB per image. [squoosh.app](https://squoosh.app) is a good free tool for this.

### Fonts

All fonts are hosted locally in the `fonts/` folder — no external requests are made. Do not delete these files. The following files must be present:

```
fonts/
├── syne-700.woff2
├── syne-800.woff2
├── inter-300.woff2
├── inter-400.woff2
└── inter-500.woff2
```

If fonts need to be replaced or updated, download them from [gwfh.madebymike.de](https://gwfh.madebymike.de/fonts) and update the `@font-face` declarations at the top of `main.css` accordingly.

### Favicon

The favicon is `favicon.ico` in the root folder. To replace it, generate a new one from an SVG or PNG at [favicon.io](https://favicon.io/favicon-converter/) and drop it in the root.

### Social Media Preview Image

`og-image.jpg` in the root is the default image shown when any page is shared on social media (Twitter/X, LinkedIn, WhatsApp etc.). It should be **1200×630px**. Replace it to update the preview across the whole site.

### feed.js — Connecting to the Blog

`feed.js` fetches live news and blog posts from the Hub and displays them on the homepage. The URL is hardcoded at the top of the file:

```javascript
const FEED_URL = 'https://iccycling.github.io/iccc-hub/feed/index.json';
```

**If the blog repository is ever renamed**, this URL must be updated to match the new path. The same applies to the blog links in the footer of `index.html`.

The homepage widget shows each item's tags as small pill badges. If you ever change the tag styling, update it in **both** `main.css` (`.tag-pill`, `.tag-row`) and the Hub's `blog.css` — they're intentionally kept identical so tags look the same everywhere.

---

## Part 2: Hugo Blog (Hub)

The blog lives at [github.com/iccycling/iccc-hub](https://github.com/iccycling/iccc-hub). It uses **Hugo** — a static site generator that turns Markdown files into HTML pages.

### Installing Hugo (Windows)

```powershell
winget install Hugo.Hugo.Extended
```

Check it worked:

```powershell
hugo version
```

You need **Hugo Extended** (not the standard version).

### Repository Structure

```
iccc-hub/
├── hugo.toml                          ← Site config (baseURL, title, params)
├── content/
│   ├── posts/                         ← Blog posts
│   │   └── my-post/
│   │       ├── index.md               ← Post content (must be named index.md)
│   │       └── images/
│   │           └── photo.jpg
│   ├── news/                          ← News items
│   │   └── my-news/
│   │       └── index.md
│   ├── committee/                     ← Committee member profiles
│   │   └── jane-doe/
│   │       ├── index.md
│   │       └── photo.jpg              ← named "photo.*", found automatically
│   ├── authors/                       ← Author profiles (optional, for blog bylines)
│   │   └── my-name/
│   │       └── index.md
│   └── feed.md                        ← Triggers feed.json generation (don't touch)
├── layouts/
│   ├── baseof.html                    ← Base template: nav, footer, meta tags
│   ├── index.html                     ← Hub homepage (news/blog widget)
│   ├── committee/
│   │   ├── list.html                  ← Committee overview grid
│   │   └── single.html                ← Individual committee member page
│   ├── _default/
│   │   ├── list.html                  ← Shared list template (posts AND news)
│   │   ├── single.html                ← Shared single template (posts AND news)
│   │   ├── term.html                  ← Tag archive page (e.g. /tags/road/)
│   │   └── taxonomy.html              ← Same as term.html, for older Hugo versions
│   └── partials/
│       ├── entry-card.html            ← One stacked list entry (reused everywhere)
│       └── share-bar.html             ← Social share icons on single pages
├── static/
│   ├── blog.css                       ← All styles
│   ├── favicon.ico                    ← Browser tab icon
│   ├── og-image.jpg                   ← Default social media preview (1200×630px)
│   ├── fonts/                         ← Locally hosted fonts (do not delete)
│   ├── ICCC-Hub.svg                   ← Logo (nav)
│   └── ICCC-white.svg                 ← Logo (footer)
└── public/                            ← Generated output (don't edit manually)
```

Posts and News intentionally share the same templates (`_default/list.html` and `_default/single.html`) — they're visually identical, the only difference is that News gets the greyed-out "expired" treatment automatically once `expires_on` passes. If a page looks wrong for one section but fine for the other, this is the file to check.

### hugo.toml — Important Settings

```toml
baseURL = "https://iccycling.github.io/iccc-hub/"
enableRobotsTXT = true
disableKinds = ["RSS"]
```

- **If the repository is ever renamed**, update `baseURL` to match the new path. Otherwise all links, images, and CSS will break.
- `enableRobotsTXT` and the (mostly empty) `disableKinds` list are what make tag pages, `sitemap.xml`, and `robots.txt` work. Don't add `"taxonomy"`, `"term"`, or `"sitemap"` back into `disableKinds` — that breaks the tag pages and search engine files respectively.
- RSS is intentionally off. To enable it, just remove `"RSS"` from the list — Hugo generates the feed automatically, no extra config needed.

### Writing a Blog Post

Create a new folder under `content/posts/` and add an `index.md`:

```
content/posts/my-ride-report/
├── index.md
└── images/
    └── photo.jpg
```

Frontmatter template:

```yaml
---
title: "My Ride Report"
date: 2025-06-01
author: "Your Name"
tags: ["Road"]
summary: "One sentence shown in the blog list."
---

Post content here in Markdown...
```

> **Important:** `date` must be today or in the past. Posts with a future date will not appear until that date arrives.

`tags` can have as many entries as make sense — all are shown as pill badges, and each links to a page listing everything with that tag. `author` only becomes a clickable link if a matching profile exists under `content/authors/` — otherwise it's shown as plain text, so generic authors like "ICCC" don't need a profile.

To use a custom social media preview image for a post, add `og_image`:

```yaml
og_image: "images/preview.jpg"
```

If omitted, the default `og-image.jpg` from `static/` is used.

See the formatting guide post for full Markdown syntax, images, and video embedding.

### Writing a News Item

Same structure, under `content/news/`. One optional extra field:

**`expires_on`** — the item stays visible but appears greyed out in the news list after this date:

```yaml
expires_on: "2025-09-27"
```

There used to be a separate `event_date` field for event announcements — it's been removed to keep things simple. For an event, just put the date into the title:

```yaml
---
title: "**26 Sept** — Welcome Back Ride"
date: 2026-02-25
expires_on: "2026-09-27"
---
```

Titles run through Markdown formatting, so `**bold**` works there.

### Adding an Author Page

Create a folder under `content/authors/` named with the author's name in lowercase with hyphens:

```
content/authors/jane-smith/
└── index.md
```

Frontmatter template:

```yaml
---
title: "Jane Smith"
bio: "A sentence or two about yourself."
avatar: "avatar.jpg"
linkedin: "https://linkedin.com/in/yourprofile"
email: "jane@example.com"
---
```

All fields except `title` are optional — `avatar` needs an image file placed in the same folder (falls back to a plain initial if omitted), `email` shows a "Get in touch" mail link. Note everything sits **inside** the `---` frontmatter block, including `bio` and `linkedin` — a stray value placed after the closing `---` is treated as page content, not a field, and won't work.

The author's name in post frontmatter must match exactly:

```yaml
author: "Jane Smith"
```

### Adding a Committee Member

Create a folder under `content/committee/`, name it after the person, and drop a photo alongside `index.md` — no need to reference the filename anywhere, it's found automatically as long as it starts with "photo." (`photo.jpg`, `photo.png`, etc.):

```
content/committee/jane-doe/
├── index.md
└── photo.jpg
```

Frontmatter template:

```yaml
---
title: "Jane Doe"
role: "President"
date: 2026-01-01
---

Free text about Jane here — Markdown, same as a blog post.
```

- `role` — shown under the name, both on the overview and the individual page
- `date` — controls the order on the overview grid. **Newest date shown first** — to put someone at the top (e.g. the President), give them the latest date
- No photo? Falls back automatically to a plain initial, same as author profiles

### Testing Locally

```powershell
# In the iccc-hub folder:
hugo server
```

Open `http://localhost:1313/iccc-hub/` in your browser. Hugo picks up changes automatically — no restart needed.

> `hugo server` only serves files over HTTP. Don't open the HTML files directly in the browser.

### Building for Deployment

```powershell
hugo
```

This generates all HTML into the `public/` folder. You normally don't need to do this manually — GitHub Actions handles it automatically on every push.

---

## Part 3: Deployment via GitHub Actions

Both repositories use **GitHub Actions** to build and deploy automatically.

### How It Works

1. You push a change to GitHub (edit a file, add a post)
2. GitHub Actions runs `hugo` automatically
3. The output is deployed to GitHub Pages
4. The live site updates within 1–2 minutes

### Checking Deployment Status

Go to your repository on GitHub → click **Actions** tab. You'll see a list of recent deployments — green tick means success, red cross means something went wrong. Click on a failed run to see the error log.

### GitHub Actions Workflow File

The workflow is at `.github/workflows/deploy.yml`. You shouldn't need to touch it, but it looks like this in outline:

```yaml
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: '0.157.0'
          extended: true
      - run: hugo
      - uses: peaceiris/actions-gh-pages@v4
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

Worth checking every so often whether a newer Hugo version is available and bumping `hugo-version` here — nothing on this site relies on cutting-edge features, so upgrades should be low-risk.

---

## Quick Reference

| Task | What to do |
|---|---|
| Edit a page on the main site | Edit the `.html` file, push to GitHub |
| Add a blog post | Create `content/posts/my-post/index.md`, push |
| Add a news item | Create `content/news/my-news/index.md`, push |
| Add an author | Create `content/authors/my-name/index.md`, push |
| Add a committee member | Create `content/committee/my-name/index.md` + a `photo.*`, push |
| Test locally | `hugo server` → open `localhost:1313/iccc-hub/` |
| Check if deploy worked | GitHub → Actions tab |
| Update blog styles | Edit `static/blog.css` |
| Update main site styles | Edit `main.css` |
| Replace favicon | Drop new `favicon.ico` in root / `static/` |
| Replace social preview | Replace `og-image.jpg` (1200×630px) |
| Rename blog repository | Update `baseURL` in `hugo.toml` and `FEED_URL` in `feed.js` |

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
├── club.html         ← About the club
├── other.html        ← Other disciplines
├── privacy.html      ← Privacy & Legal
├── main.css          ← All styles
├── main.js           ← Nav behaviour
├── feed.js           ← Fetches news & blog from Hub
└── images/           ← All images used on the site
```

### Making Changes

1. Download the repository as a ZIP from GitHub, or clone it with Git
2. Open the relevant `.html` file in any text editor (VSCode recommended)
3. Edit the content directly — headings, text, image paths
4. Open the file in a browser to check it looks right
5. Upload the changed file back to GitHub

The site updates automatically within a minute or two after you push to GitHub.

### Images

Replace images in the `images/` folder and update the `src` attribute in the HTML accordingly. Use web-optimised JPGs — aim for under 300KB per image.

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
├── hugo.toml                      ← Site config (baseURL, title)
├── content/
│   ├── posts/                     ← Blog posts
│   │   └── my-post/
│   │       ├── index.md           ← Post content (must be named index.md)
│   │       └── images/
│   │           └── photo.jpg
│   ├── news/                      ← News items
│   │   └── my-news/
│   │       └── index.md
│   ├── authors/                   ← Author profiles
│   │   └── my-name/
│   │       └── index.md
│   └── feed.md                    ← Triggers feed.json generation (don't touch)
├── layouts/                       ← HTML templates (don't touch unless needed)
├── static/
│   ├── blog.css                   ← All styles
│   ├── ICCC.svg                   ← Logo (nav)
│   └── ICCC-white.svg             ← Logo (footer)
└── public/                        ← Generated output (don't edit manually)
```

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

See the formatting guide post for full Markdown syntax, images, and video embedding.

### Writing a News Item

Same structure, under `content/news/`. Add `expires_on` if the news has a cutoff date — it will appear greyed out in the list afterwards but stays accessible:

```yaml
---
title: "Welcome Fair — Find Us at the Stalls"
date: 2025-09-25
expires_on: "2025-09-27"
---
```

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

---

## Quick Reference

| Task | What to do |
|---|---|
| Edit a page on the main site | Edit the `.html` file, push to GitHub |
| Add a blog post | Create `content/posts/my-post/index.md`, push |
| Add a news item | Create `content/news/my-news/index.md`, push |
| Test locally | `hugo server` → open `localhost:1313` |
| Check if deploy worked | GitHub → Actions tab |
| Update styles | Edit `static/blog.css` (Hub) or `main.css` (main site) |

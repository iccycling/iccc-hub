---
title: "How to create a News Post"
date: 2025-06-20
expires_on: "2025-09-20"
author: "Frederik Beck"
summary: "How to create a News Post"
tags: ["News"]
---

# How to Create a News Post

News posts work the same as blog posts, just under `content/news/` instead of `content/posts/`. They use the exact same layout — the only difference is the optional `expires_on` field (see below).

## 1. Create the folder
```
content/news/my-news-item/
├── index.md
└── news.jpg
```

## 2. Add Frontmatter
```yaml
---
title: "Your News Title"
date: 2025-09-20
author: "Your Name"
summary: "One sentence shown in the news list."
tags: ["News"]
expires_on: "2025-10-05"
---
```

- `date` — publication date, must be today or in the past
- `author` — shown in the byline. If a matching profile exists under `content/authors/`, the name becomes a link automatically — otherwise it's just plain text, so don't worry about creating one for e.g. "ICCC" as a generic author
- `tags` — shown as small pill badges above the title, and are clickable on the post itself (e.g. `/tags/news/`). Add as many as make sense: `tags: ["News", "Race Results"]`
- `expires_on` — optional. Once this date passes, the item stays visible but appears greyed out in the news list

### Got rid of: `event_date`

We used to have a separate `event_date` field for event announcements, shown in its own styled badge. It added a field to remember and a special case in the templates for not much benefit — so it's gone. If a news post is about a specific event, just put the date straight into the title, optionally in bold:

```yaml
title: "**26 Sept** — Welcome Back Ride"
```

Titles are run through Markdown formatting, so `**bold**` works there too.

## 3. Write content

Normal Markdown — headings, lists, bold text, links. For images use the `fig` shortcode:
```
{{</* fig src="news.jpg" caption="Newspaper (Source: Unsplash)" */>}}
```

{{< fig src="news.jpg" caption="Newspaper (Source: Unsplash)" >}}
```
{{</* fig src="news2.jpg" caption="Smartphone (Source: Unsplash)" */>}}
```

{{< fig src="news2.jpg" caption="Smartphone (Source: Unsplash)" >}}

## 4. Test and deploy
```powershell
hugo server
```

Open `http://localhost:1313/iccc-hub/news/` to check. Push to GitHub to deploy.

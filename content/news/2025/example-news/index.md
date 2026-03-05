---
title: "How to create a News Post"
date: 2025-06-20
expires_on: "2025-09-20"
author: "Frederik Beck"
summary: "How to create a News Post"
tags: ["News"]
---

# How to Create a News Post

News posts work the same as blog posts, just under `content/news/` instead of `content/posts/`.

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
expires_on: "2025-10-05"
event_date: "20. September 2025"
---
```

- `date` — publication date, must be today or in the past
- `expires_on` — optional, item appears greyed out after this date
- `event_date` — optional, shown prominently when the news is about a specific event. Always a quoted string in your preferred format.

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
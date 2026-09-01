# blog

Personal blog, built with [Hugo](https://gohugo.io). No theme, no dependencies —
the layouts and the single stylesheet live in this repo.

## Running locally

```sh
hugo server -D          # http://localhost:1313, drafts included
```

## Writing

```sh
hugo new content posts/my-post.md
```

The stub comes from `archetypes/default.md` and starts as `draft: true`.
Flip it to `false` to publish.

```yaml
---
title: "My post"
date: 2026-08-31T22:00:00-03:00
draft: false
summary: "One line, used in meta description."
tags: ["go", "notes"]
---
```

## Building

```sh
hugo --gc --minify      # outputs to public/
```

## Layout of the repo

```
hugo.toml               site config: title, bio, social links, taxonomies
assets/css/main.css     the entire stylesheet (light/dark via prefers-color-scheme)
layouts/
  baseof.html           page skeleton
  home.html             bio + links + latest posts
  section.html          /posts/, grouped by year
  posts/page.html       a single post
  page.html             standalone pages (about)
  taxonomy.html         /tags/
  term.html             /tags/<tag>/
  rss.xml               feed
  partials/             head, header, footer, post-list
content/
  _index.md             the bio shown on the home page
  about.md
  posts/                one markdown file per post
static/favicon.svg
```

## Before publishing

Set `baseURL` in `hugo.toml` to the real domain (it's `https://example.com/` for now),
and fill in `[params]` — `author`, `description`, `github`, `linkedin`, `source`.

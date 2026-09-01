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

## Deploy

Hosted on Cloudflare Workers (static assets), deployed on every push to `main`.

Settings in the Cloudflare dashboard:

**Build command**

```sh
curl -sL https://github.com/gohugoio/hugo/releases/download/v0.165.0/hugo_0.165.0_linux-amd64.tar.gz | tar -xz hugo && ./hugo --gc --minify
```

**Deploy command**

```sh
npx wrangler deploy
```

Hugo is not preinstalled in the Workers build image, so the build command pulls
a pinned binary instead of relying on a `HUGO_VERSION` variable. To upgrade Hugo,
change both occurrences of the version in that URL. (`compatibility_date` in
`wrangler.jsonc` is unrelated to the Hugo version — leave it alone.)

`wrangler.jsonc` declares the site as an assets-only Worker: no script, it just
serves `./public`. `not_found_handling: "404-page"` makes it serve `404.html`,
which comes from `layouts/404.html`.

Custom domain: `me.iscodandrade.dev`. DNS lives at Hostinger — a `CNAME` record
points the subdomain at the Worker's `.workers.dev` hostname. Cloudflare issues
the TLS certificate automatically.

`baseURL` in `hugo.toml` must match the custom domain, otherwise canonical URLs,
the sitemap and the RSS feed will point at the wrong host.

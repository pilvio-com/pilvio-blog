# Pilvio Blog Content

Blog posts and media for [blog.pilvio.com](https://blog.pilvio.com). Content is written in Markdown with YAML frontmatter, automatically synced to Pilvio S3 on push.

## Repository Structure

```
posts/          # Markdown blog posts
media/          # Images used in posts
```

## Publishing

1. Add or edit a `.md` file in `posts/`
2. Add images to `media/` if needed
3. Push to `main` — the GitHub Action automatically syncs content to S3

The sync processes Markdown into pre-built JSON (syntax highlighting, image URL resolution, reading time calculation) and uploads to S3. The blog frontend fetches this JSON directly.

Manual sync can be triggered via `workflow_dispatch` in the Actions tab.

## Post Format

Every post needs YAML frontmatter:

```yaml
---
title: "Post Title"                    # Required
author: "Author Name"                  # Required
publishedAt: "15. november 2024"       # Required (Estonian date format)
category: "Kubernetes"                 # Required
tags: ["kubernetes", "devops"]         # Required
featured: false                        # Required (only one post should be true)
excerpt: "Brief description..."        # Optional (auto-generated from content)
readingTime: "8 min"                   # Optional (auto-calculated)
imageUrl: "https://..."                # Optional (external URL)
slug: "custom-slug"                    # Optional (auto-generated from title)
---
```

### Content

- Write in Estonian. Technical terms can stay in English.
- Use Estonian date format: `"15. november 2024"`
- Headings start from `##` (h2) — the title is rendered as h1
- Code blocks with language tags get syntax highlighting (highlight.js)

### Images

- Place files in `media/` and reference with relative paths: `/media/image.png`
- Relative paths are resolved to GitHub raw URLs during sync
- External URLs (e.g., Unsplash) also work in `imageUrl`
- Recommended size: **1200x675px** (16:9 aspect ratio)

## GitHub Action Secrets

The sync workflow (`.github/workflows/sync-to-s3.yml`) requires these secrets:

| Secret | Description |
|--------|-------------|
| `S3_ENDPOINT` | S3 endpoint (e.g., `https://s3.pilw.io`) |
| `S3_BUCKET` | Bucket name (e.g., `pilvio-blog`) |
| `S3_ACCESS_KEY_ID` | S3 access key |
| `S3_SECRET_ACCESS_KEY` | S3 secret key |
| `GH_PAT` | GitHub PAT with access to the blog app repo |

## Example Post

```markdown
---
title: "Kubernetes alustamine: Praktilised nõuanded algajatele"
author: "Mart Tamm"
publishedAt: "15. november 2024"
category: "Kubernetes"
tags: ["kubernetes", "devops", "konteinerid", "algajatele"]
featured: false
excerpt: "Õpi Kubernetes põhialused ja kuidas luua oma esimest klastrit."
imageUrl: "/media/kubernetes_intro_1.png"
---

## Sissejuhatus

Kubernetes on muutunud tänapäeva konteinerite orkestreerimise standardiks...

## Kokkuvõte

Selles artiklis käsitlesime...
```

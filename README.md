# Amir's Docs

Personal technical notes published as a static site with [Hugo](https://gohugo.io/) and [Hextra](https://imfing.github.io/hextra/).

**Live site:** https://amirmahdikahdouii.github.io/Docs/

## Writing a new document

1. Create a `.md` file under `content/<section>/` (e.g. `content/golang/concurrency/my-topic.md`)
2. Add front matter at the top:

```yaml
---
title: "My Topic"
weight: 1
---
```

3. Write your content in Markdown below the front matter
4. Commit and push to `main` — GitHub Actions deploys automatically

## Local preview

Requires [Hugo Extended](https://gohugo.io/installation/) (v0.139+).

```bash
# Fetch theme dependencies
hugo mod get -u

# Start dev server
hugo server -D

# Open http://localhost:1313/Docs/
```

## Build

```bash
hugo --minify
```

Output is written to `public/`.

## GitHub Pages setup

See [docs/GITHUB_PAGES_SETUP.md](docs/GITHUB_PAGES_SETUP.md) for one-time GitHub repository settings.

## Project structure

```
content/          # All markdown documents
static/images/    # Images referenced as /images/...
layouts/          # Custom Hugo templates (GitHub alert support)
hugo.toml         # Site configuration
```

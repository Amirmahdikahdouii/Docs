# GitHub Pages Setup Guide

Follow these steps once after pushing the Hugo site to your repository.

## Prerequisites

- Repository: [github.com/Amirmahdikahdouii/Docs](https://github.com/Amirmahdikahdouii/Docs)
- Default branch: `main`
- The `.github/workflows/pages.yml` workflow is present on `main`

## Step 1: Enable GitHub Pages (GitHub Actions source)

1. Open your repository on GitHub
2. Go to **Settings** → **Pages** (left sidebar)
3. Under **Build and deployment**:
   - **Source:** select **GitHub Actions** (not "Deploy from a branch")
4. Save — no branch or folder selection is needed

## Step 2: Trigger the first deployment

The workflow runs automatically on every push to `main`. To deploy immediately:

1. Go to the **Actions** tab
2. Select **Deploy Hugo site to GitHub Pages**
3. Click **Run workflow** → **Run workflow** (manual trigger)

Or push any commit to `main`.

## Step 3: Verify the deployment

1. In **Actions**, open the latest workflow run
2. Wait for both **build** and **deploy** jobs to complete (green checkmarks)
3. Open the deployment URL shown in the deploy job, or visit:

   **https://amirmahdikahdouii.github.io/Docs/**

The first deploy may take 2–5 minutes. Later deploys are usually faster.

## Step 4: Optional settings

### Enforce HTTPS

Under **Settings → Pages**, ensure **Enforce HTTPS** is checked (on by default for `*.github.io`).

### Custom domain

Not configured for this site. The default URL is used:

`https://amirmahdikahdouii.github.io/Docs/`

## Publishing new documents

Every push to `main` that changes content triggers a new deployment.

1. Add or edit a `.md` file under `content/`
2. Include front matter:

```yaml
---
title: "Page Title"
weight: 1
---
```

3. Commit and push to `main`
4. Watch the **Actions** tab for the deploy workflow
5. Site updates in about 1–3 minutes after a successful run

## Troubleshooting

### Site returns 404

- Confirm **Pages → Source** is **GitHub Actions**, not a branch
- Confirm the latest workflow run succeeded
- Wait a few minutes after the first successful deploy

### Build fails in Actions

- Open the failed run and read the **Build** job log
- Common fixes:
  - Do **not** run `hugo mod get -u` unless you also upgrade Hugo in `.github/workflows/pages.yml` (Hextra requires Hugo >= 0.146.0)
  - Keep `go.mod` / `go.sum` pinned to a compatible Hextra version
  - Fix broken Markdown or invalid front matter in `content/`

### Images not loading

- Images must live in `static/images/`
- Reference them in Markdown as `/images/filename.png` (leading slash, no `static/` prefix)

### Local preview works but GitHub Pages does not

- Check `baseURL` in `hugo.toml` matches your Pages URL:
  `https://amirmahdikahdouii.github.io/Docs/`
- Rebuild and redeploy after changing `baseURL`

## Local development

```bash
# Install Hugo Extended: https://gohugo.io/installation/
hugo mod get -u
hugo server -D
# http://localhost:1313/Docs/
```

## Workflow overview

| Event | Action |
|-------|--------|
| Push to `main` | Build Hugo site → upload artifact → deploy to GitHub Pages |
| Manual dispatch | Same as above, from Actions tab |

No `gh-pages` branch is required. Deployment uses GitHub's official Pages artifact API.

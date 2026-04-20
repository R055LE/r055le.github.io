# r055le.github.io

Personal site and blog. Hugo + [hugo-clarity](https://github.com/chipzoller/hugo-clarity), deployed to GitHub Pages.

Live at **[r055le.github.io](https://r055le.github.io/)**.

## What's here

- Writing on DevSecOps, SRE, platform engineering, and the invisible work that keeps production running.
- Career notes and career-adjacent posts — layoffs, portfolio thinking, the visibility problem.
- Occasional deep dives tied to the labs listed on [my GitHub profile](https://github.com/R055LE).

## Stack

| | |
|---|---|
| Static site generator | [Hugo](https://gohugo.io/) (extended, pinned to `v0.160.1`) |
| Theme | [hugo-clarity](https://github.com/chipzoller/hugo-clarity) (git submodule) |
| Hosting | GitHub Pages |
| CI | GitHub Actions — see [`.github/workflows/deploy.yml`](.github/workflows/deploy.yml) |

Deploys automatically on push to `main`.

## Local development

```bash
git clone --recurse-submodules https://github.com/R055LE/r055le.github.io.git
cd r055le.github.io
hugo server -D
```

`-D` includes drafts. Site serves at `http://localhost:1313`.

If you forgot `--recurse-submodules`:

```bash
git submodule update --init --recursive
```

## Layout

```
content/
  _index.md        landing page
  about.md
  projects.md
  post/            published posts
drafts/            work-in-progress, not committed to content/
config/_default/   Hugo configuration
assets/            theme overrides and custom assets
static/            images, files served as-is
```

## Writing workflow

1. Draft in `drafts/` or directly in `content/post/` with `draft: true` in frontmatter.
2. Preview locally with `hugo server -D`.
3. Flip `draft: false`, commit, push — Actions builds and deploys.

## License

Content (posts, images) © R055LE, all rights reserved.
Site configuration and theme overrides are MIT unless noted otherwise.

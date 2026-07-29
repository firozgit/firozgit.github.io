# firozshaik.com

Quarto site. Source in `.qmd`, rendered to `_site/`, deployed to GitHub Pages by
GitHub Actions on every push to `main`.

## One placeholder left

GitHub (`firozgit`) and LinkedIn (`shaikfiroz`) are wired in. Still outstanding:

| Placeholder | Where | What |
|---|---|---|
| `FIROZ_X` | `_includes/head.html` | your X handle, once claimed |

Then in `_includes/head.html`, uncomment the `rel="me"` links as each account
comes online, and add to the `sameAs` array — Google Scholar and ORCID first,
since those are what disambiguate you from the other Firoz Shaiks in search.

## Local

Install Quarto from <https://quarto.org/docs/get-started/>, or:

```powershell
:: Windows
winget install --id Posit.Quarto
```

```bash
# macOS
brew install quarto
```

Then, one time:

```powershell
:: Windows
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

```bash
# macOS / Linux
python3 -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

Every time:

```bash
quarto preview                   # live reload at localhost:4200
```

`execute.freeze: auto` means a post's Python runs once, and the output is cached
in `_freeze/`. **Commit `_freeze/`.** Re-runs only happen when you edit the code
in that post, which keeps preview fast and CI deterministic.

## Deploy, in this order

The order matters — configuring DNS before GitHub knows about the domain leaves
a window where someone else can claim a subdomain.

1. **Name the repo `firozgit.github.io`** and push. A user-site repo serves
   from `/`, so paths work whether you visit the custom domain or the
   github.io URL.
2. **Settings → Pages → Source: GitHub Actions.**
3. **Settings → Pages → Custom domain:** `firozshaik.com`. Save.
4. **Settings → Pages → Verify domain ownership.** GitHub gives you a TXT
   record. Add it. This is what prevents subdomain takeover.
5. **DNS at your registrar:**

   | Type | Name | Value |
   |---|---|---|
   | A | `@` | `185.199.108.153` |
   | A | `@` | `185.199.109.153` |
   | A | `@` | `185.199.110.153` |
   | A | `@` | `185.199.111.153` |
   | AAAA | `@` | `2606:50c0:8000::153` |
   | AAAA | `@` | `2606:50c0:8001::153` |
   | AAAA | `@` | `2606:50c0:8002::153` |
   | AAAA | `@` | `2606:50c0:8003::153` |
   | CNAME | `www` | `firozgit.github.io` |

   Delete any A record your registrar pre-populated. On Cloudflare, leave these
   **DNS-only** (grey cloud) — proxying interferes with GitHub's certificate
   issuance.
6. **Wait, then tick Enforce HTTPS.** Certificate provisioning can take up to
   24 hours.

The `CNAME` file in this repo is listed under `project.resources`, so it gets
copied into `_site/` on every render and the custom domain survives redeploys.

## Adding a post

```bash
mkdir -p writing/02-matrix-py
$EDITOR writing/02-matrix-py/index.qmd
```

Front matter:

```yaml
---
title: "..."
description: "One sentence. This is what shows in the listing and in link previews."
date: 2026-08-19
categories: [pure python gpt]
draft: true        # remove to publish
---
```

`draft: true` keeps it out of the listing and out of the RSS feed while you work.
Posts are discovered automatically from the `writing/` directory — no index to
maintain.

Keep the series *code* in a separate repo with a tag per post, so readers can
`git checkout post-04` and get the state of the world at that point. Link to the
tag from each post.

## Design notes

So future changes stay coherent rather than accreting.

**Palette.** Ground is a pale slate-mint (`#edf2f1`), ink is petrol
(`#14262b`) — deliberately not the cream-and-terracotta that every generated
portfolio uses. The accent pair is viridian `#17705f` and madder `#a03242`,
used as **pass** and **fail**. On a site about evaluation, a green/red accent
carries meaning, so it's allowed to be the accent. Don't use them decoratively.

**Type.** Inverted from the usual serif-display-over-sans-body: everything
machine-adjacent — headings, code, labels, nav — is IBM Plex Mono or Plex Sans,
and long-form prose is Newsreader. The posts are meant to be read at length, so
the prose gets the reading face and the machinery gets the engineered one.

**Signature.** The `assert-run` block on the homepage, styled as pytest output.
It's the one bold element; everything else is intentionally quiet. It's earned
rather than decorative — the assertion is the actual unit of your work, since OCB
is 6,719 of them. If you add another loud element, remove this one.

**Tokens** live in `theme.scss` / `theme-dark.scss` and are re-exported as CSS
custom properties (`--ink`, `--pass`, `--rule`, ...) for `styles.css`. Add new
colors there, not inline.

## Layout

```
_quarto.yml                       site config, nav, theme wiring
theme.scss / theme-dark.scss      design tokens + Bootstrap overrides
styles.css                        assert-run, eyebrows, listings, papers
_includes/head.html               fonts, schema.org Person, rel=me
index.qmd                         homepage
writing.qmd                       post listing + RSS
papers.qmd  about.qmd
writing/01-gpt-in-pure-python/    one directory per post
CNAME                             firozshaik.com
.github/workflows/publish.yml     render + deploy
```

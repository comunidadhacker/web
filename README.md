# Comunidad Hacker Crea Juegos — public site

**This directory is generated. Do not edit it by hand.**

Every file here is produced by `npm run sitio` in the *game* repository
(`sistemas-planetarios`, the one that builds Crea Planetas). That script clears this
directory (everything except `.git`) and rewrites it from:

| Source in the game repo | What it becomes |
| --- | --- |
| `play/ficha-es.md`, `play/ficha-en.md` | the game pages, both languages |
| `play/comunidad.md` | the studio landing page |
| `play/privacidad.html` | `privacidad.html` and `privacidad-play.html` |
| `store/screenshots/` + `capturas.json` | the galleries and their measured physics captions |
| `store/`, `public/*.svg` | icons, feature graphics, logos |

So a typo on this site is fixed **in `play/` in the game repo**, not here. The same prose is
what gets pasted into Google Play Console, which is the whole point: the listing and the
website cannot drift apart because they are the same text.

## Pages

- `index.html`
- `en/index.html`
- `crea-planetas/index.html`
- `crea-planetas/en.html`
- `privacidad.html`
- `prensa.html`
- `privacidad-play.html` — the privacy policy as a **single self-contained file**, byte for
  byte the one in the game repo. This is the URL to paste into Play Console: it has no CSS,
  no images and no navigation, so it keeps working even if the rest of the site is
  reorganized or removed.

## Publishing on GitHub Pages

This repo *is* the site: Pages serves it from the branch root, so no build step and no
Actions workflow are needed.

```bash
# once, inside this directory
git init -b main
git add -A
git commit -m "Sitio de Comunidad Hacker Crea Juegos"
git remote add origin git@github.com:<usuario>/<repo>.git
git push -u origin main
```

Then in **Settings → Pages**, set *Source* to "Deploy from a branch", branch `main`,
folder `/ (root)`. The site appears at `https://<usuario>.github.io/<repo>` within a minute or two.

To update it after changing the prose in the game repo:

```bash
npm run sitio                  # in the game repo; rewrites this directory
cd sitio && git add -A && git commit -m "Actualizar sitio" && git push
```

### `.git` lives here and survives regeneration

`npm run sitio` clears this directory and rewrites it, **including this README**, but it
deliberately skips `.git`: it deletes entry by entry instead of removing the directory, so
keeping the site's clone right here is safe and the published history cannot be destroyed by
running the generator. Verified — the commit history survives a regeneration.

### `.nojekyll` is not optional

Without it GitHub runs everything through Jekyll, which silently drops any file or directory
whose name starts with `_`. There are none right now; the day there is one, the symptom is a
404 with no visible cause.

## The URL Google Play needs

Play requires the privacy policy at a **public https address**. Once this is live, that is:

    https://<usuario>.github.io/<repo>/privacidad-play.html

Paste it into Play Console under *Policy → App content → Privacy policy*. Use the standalone
file rather than `privacidad.html` so the declaration cannot break if the site's layout
changes.

## Open Graph and canonical URLs

Both need the site's absolute address, which this repo cannot know. Generate with it set and
the pages get `<link rel="canonical">` plus the `og:*` tags that make a shared link unfurl
with an image:

```bash
SITIO_BASE=https://<usuario>.github.io/<repo> npm run sitio
```

Without it those tags are omitted entirely rather than written half-wrong — a partial `og:`
block renders as a broken preview card, which is worse than a plain link.

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
folder `/ (root)`.

### The custom domain: `comhackcreajuegos.com`

`CNAME` is generated into this directory on every build, containing just `comhackcreajuegos.com`. That file
has to be **in the repository**, not only in the Pages settings screen: configure it only in the
UI and the first deploy without the file wipes it, dropping the site back to `*.github.io` with
the wrong certificate.

At the DNS provider for `comhackcreajuegos.com`, for an **apex** domain (no subdomain) add four `A` records
on `@`:

    185.199.108.153
    185.199.109.153
    185.199.110.153
    185.199.111.153

and, for IPv6, four `AAAA` records on `@`:

    2606:50c0:8000::153
    2606:50c0:8001::153
    2606:50c0:8002::153
    2606:50c0:8003::153

Keep the `A` records even if you add `AAAA`; GitHub recommends both because IPv6-only
resolution is still not universal. If the provider supports `ALIAS`/`ANAME`, one of those
pointing at `<usuario>.github.io` replaces all eight. Optionally add a `CNAME` record for
`www` pointing at `<usuario>.github.io`, and Pages will redirect between the two by itself.

Check it with `dig comhackcreajuegos.com +noall +answer -t A` before blaming the site. Then tick **Enforce
HTTPS** in Settings → Pages; if it is greyed out, DNS has not propagated yet or the `CNAME`
file does not match the configured domain exactly.

To update the site after changing the prose in the game repo:

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

    https://comhackcreajuegos.com/privacidad-play.html

Paste it into Play Console under *Policy → App content → Privacy policy*. Use the standalone
file rather than `privacidad.html` so the declaration cannot break if the site's layout
changes.

## The contact email appears on ONE page, deliberately

`privacidad.html` (and its standalone twin) carry the address, because a privacy policy with no
way to reach anyone is useless and Play publishes it on the listing anyway. **No other page does**
— the press page links to the policy instead. Every extra copy is one more address for the
crawlers that scrape HTML for `mailto:`, and it opens no new channel.

The generator reports this on every run: it greps the other pages and prints either
`correo: solo en la politica…` or the pages where it leaked. If you add a page, keep it that way.

## Open Graph and canonical URLs

Generated with `https://comhackcreajuegos.com` by default, so the pages carry `<link rel="canonical">` plus the
`og:*` tags that make a shared link unfurl with an image. To build a throwaway copy with no
absolute URLs (and no `CNAME`), pass an empty base:

```bash
SITIO_BASE= npm run sitio
```

Those tags are then omitted entirely rather than written half-wrong — a partial `og:` block
renders as a broken preview card, which is worse than a plain link.

# CLAUDE.md

Guidance for Claude Code working in this repository.

## What this is

The public marketing/landing site for **storieswithdjj.com** — deliberately a separate,
**public** repo from the private production repo (`stories-with-DJJ`, sibling directory under
`/Users/home/Documents/Scripts/Projects/`). A landing page has zero sensitive content, so it
doesn't need to live inside the private repo; keeping it separate also means this repo's commit
history, issues, etc. are safe to ever make fully public without a review pass.

**Cross-repo relationship**: the production repo owns brand identity as source of truth —
`stories-with-DJJ/BRAND.md` documents the locked logo variants, brand colors, and the SWDJJ
font family in full (derivation, licensing, letter-color mapping methodology). This repo just
*consumes* those already-decided assets. Check `BRAND.md` before changing anything visual here;
don't redecide brand questions in this repo.

## Current state (as of 2026-08-10, first ship)

A single coming-soon page: hero image (the owner's own Pixelmator mockup, `hero.png`) + "Coming
Soon" + a one-line tagline, set in the SWDJJ Color font. Nothing else built yet — no nav, no
social links, no real content pages. Real next step, not done yet: the owner is revising
`hero.png` in Pixelmator to fix a size mismatch (the wordmark inside the image currently reads
smaller than the live "Coming Soon" text next to it) — once a new `hero.png` lands, the live
`.coming-soon` CSS font-size may need adjusting to match its new proportions. Current value:
`clamp(1.8rem, 5vw, 3rem)` (viewport-responsive, caps at 48px on desktop widths).

## Hosting: GitHub Pages — why, not Firebase

Both were real options (see `stories-with-DJJ/CLAUDE.md`'s session notes for the full comparison
if it resurfaces). Decided on GitHub Pages because for a static site there's effectively zero
migration cost later — moving to Firebase (or anywhere) if a real backend need ever materializes
is just copying the same files + repointing DNS, no rebuild. No reason to pay complexity now for
a speculative "maybe someday" need. Revisit only if a real, concrete backend requirement shows up
(a working email-signup form, a store, etc.) — not before.

Deploy mechanism: **`CNAME` file in repo root** (containing `storieswithdjj.com`) is what tells
GitHub Pages to serve this custom domain instead of the default `carcar9626.github.io/
storieswithdjj-web/` URL. Pages itself was enabled via `gh api -X POST repos/carcar9626/
storieswithdjj-web/pages` (branch `main`, path `/`) — a plain push to `main` triggers a rebuild
automatically, no CI config needed.

## DNS (directnic.com, NOT this repo's concern to change, but the record here for reference)

Root domain (`storieswithdjj.com`, blank/`@` name) needs exactly **4 A records**, GitHub Pages'
fixed IPs:
```
185.199.108.153
185.199.109.153
185.199.110.153
185.199.111.153
```
`www.storieswithdjj.com` needs one **CNAME** record: `www` -> `carcar9626.github.io`.

**Real gotcha hit 2026-08-10**: directnic's domain came with pre-existing parking-page A records
(`104.143.9.210`, `104.143.9.211`) that were NOT automatically removed when the 4 GitHub records
were added — ended up with 6 A records total, which would nondeterministically resolve to either
the real site or directnic's parking page depending on which IP a resolver picked. Had to be
manually deleted. If DNS ever looks flaky again on this domain, check for stray leftover A
records first, don't assume it's a GitHub Pages or propagation issue.

**HTTPS**: not enforced yet as of first ship — GitHub needs to auto-verify the domain and issue
a Let's Encrypt cert once DNS is confirmed live, which isn't instant. Once available, "Enforce
HTTPS" becomes checkable in **Settings -> Pages** on this repo. Check periodically until it's on;
don't leave the site on HTTP long-term.

## Fonts — compiled outputs live here, source/build tooling lives in the OTHER repo

`SWDJJ.ttf` (plain) and `SWDJJ-Color.ttf` (COLRv0 color font, one flat brand color baked per
letter, no shading) are committed here as-is — these are build *outputs*. The actual source
(Nunito's own variable font, SIL OFL-licensed), the corpus-driven letter->color assignment
script, and the fontTools build script all live in the private repo at
`stories-with-DJJ/art_reference/logos/fonts/` (gitignored there like all of `art_reference/`, so
not visible in that repo's own git history — the files exist on disk, just not version-controlled
there either). If the font ever needs rebuilding (e.g. to add the punctuation/digit color support
logged as a known gap in `BRAND.md`), start from that folder, not from scratch here.

## Local preview — a real cross-repo dependency, not a mistake

This repo has no `.claude/launch.json` of its own. Local preview currently works through the
**production repo's** launch config (`stories-with-DJJ/.claude/launch.json`, entry named
`web-preview`, serves this directory on port 8767 via `python -m http.server --directory
<path-to-this-repo>`). This is a known quirk (the preview tooling used to build this site looks
for a launch config in a fixed project root, not wherever a new repo happens to be) — not a
long-term architectural stance. Fine to leave as-is for a single static page; revisit if this
repo grows enough to want independent local tooling.

## What's explicitly NOT decided yet

- No real content beyond the coming-soon page — no character bios, no episode links, no proper
  nav. Don't build ahead of what's been asked for.
- No email set up for `contact@storieswithdjj.com` yet (owner was going to check directnic's own
  panel for included forwarding first, then consider Cloudflare Email Routing as a free
  forward-to-existing-Gmail fallback). Not this repo's concern even once set up — email routing
  is a DNS/registrar-level thing, doesn't touch any file here.
- No analytics, no cookie/consent banner — not needed yet for a page with no tracking and no
  forms; add only if that changes.

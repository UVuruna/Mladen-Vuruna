# CLAUDE.md — Mladen Vuruna

Project-specific guidance for Claude Code. **Inherits ALL rules from the
monorepo root [CLAUDE.md](../../CLAUDE.md)** (mandatory workflow, Priorities,
Rules #1–#18, markdown guidelines, version/commit system) — read that first;
only project facts and deltas live here. The build/release pipeline does NOT
apply (this is a website).

---

## Project Facts

- **Product:** personal portfolio website for a Serbian writer and artist —
  live at [mladenvuruna.com](https://mladenvuruna.com). Book excerpts with
  page-flip animation, essays, art gallery, visitor analytics, IP-gated admin
  panel, contact/comments.
- **Stack:** PHP 8.x, Vanilla JS, HTML5, CSS3, SQLite; WebP images (converted
  from PDF).
- **Config home (Rule #4):** DB paths, image quality, admin IPs and tunables
  live in `config.php` (gitignored in production).

## Project Deltas & Web-Specific Rules

These are the site's expression of the root rules plus web-only requirements:

- **Modern UI (Rule #16):** the public site must follow [DESIGN.md](../../DESIGN.md)
  — no dated gray/blocky look.
- **Performance is priority A (root Priorities):** lazy-load below-fold images
  (`loading="lazy"`), WebP everywhere, preload critical fonts/above-fold assets,
  generate responsive sizes. Image sizes: thumbnail 400px/60%, medium 800px/75%,
  full 1200px/85% (in `config.php`).
- **Mobile-first:** write mobile styles first, add desktop via `min-width`
  media queries. Most visitors are on phones.
- **Component-based CSS/JS:** each component has its own CSS and JS file; a page
  loads only what it needs (`$styles`/`$scripts` arrays) — no single giant
  `style.css`.
- **No external CDN/API — download everything locally.** JS libraries →
  `assets/libraries/`, fonts → `assets/fonts/`, CSS libs → `assets/css/lib/`.
  If a local copy is impossible (keyed API, real-time service), ASK first and
  explain why. Reason: CDNs fail, slow the page, or disappear.
- **Security:** always prepared statements (never string interpolation into
  SQL), `htmlspecialchars(..., ENT_QUOTES, 'UTF-8')` on all output, hashed
  passwords (`password_hash`).
- **Accessibility:** `aria-label` on icon buttons, `alt` on every content image,
  semantic HTML (no `<div onclick>`).
- **Commits follow the root version system** (`0.0.NNN Component - detail`, as in
  the existing history).
- Communicate in Serbian (Latin); everything in files stays English.

## Structure

```
📁 MVuruna/
  📄 index.php · config.php
  📁 assets/     ← css/ js/ fonts/ img/ (component-scoped)
  📁 includes/   ← header, footer, head, loader, db
  📁 components/ ← books, essays, gallery, comments, admin
  📁 pages/      ← knjige, eseji, galerija
  📁 api/        ← analytics, comments, admin, upload (PDF→WebP)
  📁 data/       ← mvuruna.db, books/, essays/, gallery/, pdf/
```

## Subsystems

- **Admin:** visitor IP checked against `CONFIG['admin_ips']` → Admin button
  (hidden for others) → password modal (`password_hash`) → 24h session cookie.
- **Analytics:** JS beacon on load/unload → PHP endpoint → SQLite (IP,
  geolocation, pages visited, time per page); cookie-consent banner on first
  visit.
- **PDF → WebP:** admin uploads a PDF → Imagick/Ghostscript converts each page
  to three WebP sizes → original PDF kept in `data/pdf/` for download → DB
  updated (title, page count, paths).

## Database Schema (core tables)

`content` (books/essays/art) · `pages` (thumbnail/medium/full paths per page) ·
`visitors` (IP, geolocation, first/last visit) · `page_views` (visitor, url,
time_spent) · `comments` (visitor, content, text). See [README.md](README.md)
and [ROADMAP.md](ROADMAP.md) for details.

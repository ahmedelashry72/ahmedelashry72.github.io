# CHANGELOG — Ahmed El-Ashry Website

## v1.1.2 — GitHub Pages Jekyll Processing Fix

**Real bug found during final packaging review:** GitHub Pages runs every repository through Jekyll by default, and Jekyll's default behavior is to silently exclude any file or folder starting with an underscore from the published output. This project has a file named `_headers` — meaning it was likely never actually being published to the live site at all, silently dropped by Jekyll's build step before this was caught.

**Fix:** Added `.nojekyll` (empty file, standard convention) to the project root. This tells GitHub Pages to skip Jekyll processing entirely and publish every file exactly as it exists in the repository — no build step, no exclusions. Zero risk, zero downside for a hand-built static site like this one that was never using Jekyll's templating in the first place.

---

## v1.1.1 — Final Production Validation Pass

Performed a full 10-point production validation (see `FINAL_REPORT.md` for complete methodology). Fixes found and applied:

- **29 images across all 5 pages were missing explicit `width`/`height` attributes** — a real Cumulative Layout Shift risk under Lighthouse's Core Web Vitals audit. Fixed with each image's actual intrinsic dimensions (icons, logos, and the hero portrait).
- **2 more orphaned asset files found and removed** (`Ahmed_ElAshry_Logo_Horizontal_LightBG.png`, `Ahmed_ElAshry_Monogram_Navy.svg`) — leftovers from earlier design rounds, confirmed unreferenced before deletion.
- **`404.html` was missing a meta description** — added one.
- **Packaging safety fix:** the previous release's minification script had a bug that could silently truncate output files to zero bytes with no error raised (caught before delivery last time by manually checking a specific known fix survived — see `FINAL_REPORT.md` §10 for the full story). This pass's script includes an explicit assertion that fails loudly if that ever happens again, rather than depending on catching it by hand each time.

---

## v1.1 — Production Engineering Pass

**Note on scope:** This release was built on the current, fully-approved working copy of the site (final hero treatment, final copy, consolidated stats section) — not on the older project snapshot uploaded alongside this request, which predates several approved rounds of work (it still had the original "25+ Years Building Restaurants That Perform." headline, "View Experience" CTA, and no portrait redesign at all). Reverting to that snapshot would have undone real, previously-approved changes, so this changelog covers fixes applied on top of the current baseline.

---

### 🐛 Bug Fixes

**Arabic RTL phone/email/handle display bug — `contact.html`**
- **What:** The phone number, email address, and LinkedIn handle were rendered as plain text inside RTL-direction containers with no directional isolation.
- **Why it broke:** The Unicode Bidi Algorithm can visually reorder LTR content (digits, symbols like `+`) when it's embedded directly in RTL flow with no isolation boundary — this is what caused the phone number to display incorrectly on the Arabic version of the page.
- **Fix:** Wrapped all three values in `<bdi>` (Bi-Directional Isolation), the HTML element purpose-built for exactly this case. No visual/layout change on the English version; the Arabic version now renders the phone number, email, and handle in correct left-to-right order regardless of surrounding RTL context.

**Broken JSON-LD `image` field — all 4 bilingual pages**
- **What:** Structured data referenced `assets/portrait.png`, a file that no longer exists (renamed during the portrait redesign).
- **Why it matters:** Broken image references in Schema.org structured data can suppress rich results in search engines.
- **Fix:** Repointed to a real, existing asset.

**Wrong canonical domain across all SEO metadata**
- **What:** `<link rel="canonical">`, Open Graph tags, Twitter Card tags, JSON-LD `url`/`image`, `sitemap.xml`, and `robots.txt` all referenced a placeholder domain (`ahmedelashry.github.io`) set before the site was actually deployed.
- **Why it matters:** Once the real deployment URL was confirmed (`ahmedelashry72.github.io/ahmedelashry.github.io/` — a project-page URL, since the repo name doesn't exactly match the GitHub username), incorrect canonical/OG URLs actively hurt search indexing and break link-preview cards on every platform that reads them (LinkedIn, Twitter/X, Slack, WhatsApp, etc.).
- **Fix:** Replaced every instance with the real, confirmed live URL.

**`manifest.json` used absolute root paths (`/index.html`, scope `/`)**
- **What:** Since the site is deployed at a subpath (not domain root), absolute paths in the web manifest would have pointed "Add to Home Screen" installs at a non-existent URL one level up from the real site.
- **Fix:** Changed to relative paths (`./index.html`, scope `./`) so the manifest resolves correctly regardless of subpath.

**Generic logo used as social-share image instead of a portrait**
- **What:** `og:image`/`twitter:image` pointed at the horizontal logo lockup.
- **Why it matters:** For a personal executive brand, link previews that show a logo instead of the actual person are weaker — recruiters and investors sharing the link expect to see who they're evaluating.
- **Fix:** Composited the approved real portrait onto a flattened, opaque navy background at standard 1200×630 OG dimensions (no visual change to the live site — this only affects link-preview cards). Used a flattened/opaque image deliberately, since transparent PNGs render unpredictably across different social platforms' preview generators.

**Hero corner signature text didn't match the approved hero subtitle wording**
- **What:** Signature read "Director of Restaurant Operations"; the approved hero subtitle uses "Restaurant Operations Director." Same information, inconsistent phrasing on the same screen.
- **Fix:** Aligned wording. Arabic version was already consistent — no change needed there.

**JSON-LD `jobTitle`/`description` were stale**
- **What:** Referenced an earlier positioning statement, not the final approved "Restaurant Operations Director | Hospitality Consultant" framing.
- **Fix:** Synced to match current hero copy.

---

### ♿ Accessibility

**Added skip-to-content link — all 5 pages**
- **What:** No mechanism existed for keyboard or screen-reader users to bypass the header/navigation and jump straight to page content.
- **Fix:** Added a standard visually-hidden-until-focused skip link at the top of `<body>`, targeting a new `<main id="main-content">` landmark. Bilingual (EN/AR) on the 4 main pages; English-only on `404.html`, consistent with that page's existing English-only scope.

**Added `<main>` landmark — all 5 pages**
- **What:** Page content sat directly between `<header>` and `<footer>` with no semantic landmark wrapping it — a real gap for assistive technology users who navigate by landmark.
- **Fix:** Wrapped primary content in `<main id="main-content">`.

---

### 🔒 Security

**CSP would have silently blocked the new language-flash-prevention script**
- **What:** The site's existing Content-Security-Policy (`script-src 'self'`) deliberately disallows inline scripts. Adding an inline script (below) without accounting for this would have had the browser silently drop it — no error shown to the user, feature just wouldn't work.
- **Fix:** Rather than weakening the policy with `'unsafe-inline'`, computed the exact SHA-256 hash of the new script and added it as a CSP hash-source. This allows *only* this specific, reviewed inline script while continuing to block any unauthorized injected script — the security posture is unchanged, not loosened.

---

### ⚡ Performance / UX polish

**Language-flash prevention on page load — all 4 bilingual pages**
- **What:** A returning visitor with a saved Arabic preference would briefly see the English/LTR layout flash before JavaScript applied their saved language on `DOMContentLoaded`.
- **Fix:** Added a small synchronous script at the very top of `<head>` that reads the saved language preference and sets `lang`/`dir` before first paint, when applicable. No visual change for English visitors or first-time visitors.

**Added `Cache-Control` guidance in `_headers`**
- **What:** No caching policy was defined for static assets.
- **Fix:** Added cache rules for `/assets/*`, `/css/*`, `/js/*`. Honest caveat, stated in the file itself: GitHub Pages does not read `_headers` at all — this only takes effect if the site is ever fronted by Netlify or Cloudflare Pages. Included proactively rather than silently, since asking for "performance improvements" without this would leave an easy win undocumented.

---

### 🧹 Cleanup

**Removed 2 orphaned legacy files**
- `assets/portrait.jpg`, `assets/portrait.webp` — leftover from before the portrait-cutout redesign; confirmed unreferenced by any page before deletion.

---

### ✅ Verified, not just claimed (re-run after every change above)

- Zero HTML tag-structure errors across all 5 pages
- Zero broken internal links/asset references
- Full EN/AR translation-key parity across all 4 bilingual pages
- Valid JSON-LD structured data and valid `manifest.json` (parsed, not just eyeballed)
- Correct Arabic name (أحمد العشري) present everywhere; old spelling confirmed absent
- Single `<h1>` per page; every image carries `alt` text

### ⚠️ Known limitation, stated plainly

No headless browser or real device is available in this environment, so nothing above was verified by literally rendering pixels — verification was done by parsing, validating, and computing (contrast ratios, link resolution, JSON validity, structural checks), not by looking at a screenshot. Worth a real-device pass after deployment, same caveat as every prior release of this project.

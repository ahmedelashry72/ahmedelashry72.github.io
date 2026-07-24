# Ahmed El-Ashry — Executive Website

A bilingual (English / Arabic) executive portfolio site for Ahmed El-Ashry — Executive Hospitality Consultant & Restaurant Operations Executive. Static HTML/CSS/JS, no build step, no framework.

## Folder structure

```
/
├── index.html            Home / About
├── experience.html        Full career timeline + education
├── consulting.html        Services + target audience
├── contact.html            Contact details + message form
├── 404.html                 Custom not-found page
├── sitemap.xml
├── robots.txt
├── css/
│   └── style.css           Full design system (colors, type, layout, RTL rules)
├── js/
│   ├── main.js              Shared logic: language toggle, mobile nav
│   ├── i18n-home.js         EN/AR text for index.html
│   ├── i18n-experience.js   EN/AR text for experience.html
│   ├── i18n-consulting.js   EN/AR text for consulting.html
│   └── i18n-contact.js      EN/AR text for contact.html
└── assets/
    ├── portrait.jpg / .webp
    ├── logo/                Monogram + horizontal logo (SVG + PNG)
    ├── icons/                Contact icons
    └── favicon/              Favicon set (16–512px + .ico)
```

## How the bilingual toggle works

Each page loads its own `i18n-*.js` file, which defines a `window.I18N` object with `en` and `ar` keys. `main.js` reads the saved language from `localStorage`, applies text to every element with `data-i18n`, and flips `<html lang>` / `<html dir>` for full RTL support. Switching language persists across page navigation.

**To edit text:** open the relevant `js/i18n-*.js` file and edit the `en` / `ar` strings directly. No rebuild needed — just save and refresh.

## Deploying to GitHub Pages

1. Create a new GitHub repository (e.g. `ahmedelashry.github.io` for a root user site, or any name for a project site).
2. Push all files in this folder to the repository root:
   ```bash
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin https://github.com/<your-username>/<repo-name>.git
   git push -u origin main
   ```
3. In the repo, go to **Settings → Pages**, set source to the `main` branch, root folder, and save.
4. Your site will be live at `https://<your-username>.github.io/<repo-name>/` (or `https://<your-username>.github.io/` if you used the special `username.github.io` repo name).

## Before going live — replace the placeholder domain

Several files reference a placeholder URL (`https://ahmedelashry.github.io`) for SEO purposes:
- `sitemap.xml`
- `robots.txt`
- The `<link rel="canonical">`, Open Graph, Twitter, and JSON-LD tags inside each page's `<head>`

Once you know your real domain or final GitHub Pages URL, find-and-replace `https://ahmedelashry.github.io` across these files with your actual URL.

## Connecting the contact form

The contact form currently uses a `mailto:` fallback — submitting opens the visitor's email client with the message pre-filled. This works with zero setup but isn't ideal on mobile or for visitors without a configured email client.

To make it submit silently in the background:
1. Sign up for a form service such as [Formspree](https://formspree.io) (free tier available).
2. Replace the `<form>` tag's `action` attribute in `contact.html` with your Formspree endpoint, and change `method` to `POST` (remove `enctype`).
3. No other changes needed — field names already match standard conventions.

## Testimonials

The homepage includes a placeholder testimonials section styled to match the site (dashed border, elegant quote mark) showing "Client testimonials will be published soon." / "سيتم نشر آراء العملاء قريبًا." To add real testimonials later, edit the `.testimonial-placeholder` block in `index.html` and add real quote/name/company markup — happy to help build that out when you have testimonials ready to publish.

## Performance notes

- Portrait image is served as WebP with a JPEG fallback (`<picture>` element), reduced from ~2.6MB to ~40–60KB.
- All icon/logo PNGs are palette-optimized.
- Non-critical images use `loading="lazy"`.
- Fonts load from Google Fonts CDN with `display=swap` to avoid render blocking.

## SEO included

- Per-page `<title>`, meta description, canonical link
- Open Graph + Twitter Card tags
- JSON-LD `Person` structured data (includes both English and Arabic name)
- `sitemap.xml` and `robots.txt`
- Custom `404.html`

**Note on bilingual SEO:** because English and Arabic share the same URLs (toggled by JavaScript rather than separate `/ar/` paths), search engines will primarily index the default English content. If ranking in Arabic search results matters to you, the more robust long-term approach is separate URL paths per language (e.g. `/ar/index.html`) with their own `hreflang` tags — worth considering as a future iteration.

## Still open

- Custom domain (site is currently built with relative paths so it will work anywhere)
- Formspree/backend connection for the contact form
- Real client testimonials

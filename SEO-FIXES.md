# SEO Fixes — March 18, 2026

## Issues Reported by Google Search Console

### 1. Duplicate without user-selected canonical (19 pages)
Google found 19 URLs serving identical HTML content with no canonical tag to indicate which URL is the primary version. This included:
- Legitimate pages (e.g., `/work/supernova`, `/about`, `/blog/`)
- Bogus nested paths caused by malformed internal links (e.g., `/work/work/supernova`, `/blog/work/student-life-fair`)

### 2. Page with redirect (3 pages)
Three URL variants redirect to the primary domain:
- `http://nussu.net/`
- `http://www.nussu.net/`
- `https://www.nussu.net/`

This is expected behavior — Netlify already 301-redirects all of these to `https://nussu.net/`. No code fix needed.

### 3. Crawled - currently not indexed (8 pages)
Google crawled 8 pages but chose not to index them, likely due to duplicate content signals and missing canonical tags. These included blog posts, `/nus120-merch`, `/resources`, and bogus `/blog/work/...` paths.

---

## Fixes Applied

### `index.html`

- **Added `<link rel="canonical">`** — Tells Google the primary URL for each page
- **Added `<meta name="robots" content="index, follow, max-image-preview:large">`** — Explicitly allows indexing
- **Added `og:url` and `og:site_name` meta tags** — Improves social sharing and SEO signals
- **Added dynamic canonical script** before `</body>` — Updates the canonical URL and `og:url` as Framer navigates between routes, so each page gets its own correct canonical. Falls back to `/` for unknown paths.
- **Fixed typo** — "Offical" changed to "Official" in title, og:title, and twitter:title

### `sitemap.xml` (new file)

Lists all 19 legitimate pages so Google knows exactly what to crawl and index:
- `/`, `/about`
- `/work/` + 6 work subpages
- `/blog/` + 5 blog posts
- `/fop`, `/freshman`, `/nus120-merch`, `/resources`

### `robots.txt` (new file)

Allows all crawlers and points to the sitemap:
```
User-agent: *
Allow: /
Sitemap: https://nussu.net/sitemap.xml
```

### `_redirects` (updated)

- Added 301 redirects for bogus nested paths so Google stops treating them as duplicates:
  - `/work/work/supernova` → `/work/supernova`
  - `/work/work/union-camp` → `/work/union-camp`
  - `/blog/work/supernova` → `/work/supernova`
  - etc.
- Ensured `robots.txt` and `sitemap.xml` are served directly instead of being caught by the SPA fallback

---

## Post-Deploy Steps

1. **Submit sitemap** in Google Search Console: Sitemaps → `https://nussu.net/sitemap.xml`
2. **Request re-indexing** for key pages via the URL Inspection tool
3. Wait a few weeks for Google to re-crawl and update its index

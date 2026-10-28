# NorthPeakDigiStudio SEO site

Static HTML ready for **GitHub Pages** or **Cloudflare Pages**. No build step, no tracking scripts, no fake reviews. Prices match the live Gumroad shop as documented 28 Oct 2026 (Europe/Rome).

## Contents

| Path | Purpose |
|---|---|
| `index.html` | Home + guide list |
| `products.html` | Every live product + coming-soon titles |
| `articles/*.html` | 5 SEO guides (meta title/description preserved from Matrix drafts) |
| `css/style.css` | Mobile-first styles |
| `sitemap.xml` | Search sitemap |
| `robots.txt` | Crawl rules + sitemap pointer |
| `base_url.txt` | **Single config spot** for the public origin |

Internal links are relative. Absolute URLs (canonical, Open Graph, JSON-LD, sitemap, robots) use the placeholder string `BASE_URL`.

## 1. Set your public URL (required before go-live)

1. Edit `base_url.txt` so it contains **only** your origin, **no trailing slash**, for example:
   - `https://youruser.github.io/northpeak-seo`
   - `https://northpeakdigistudio.pages.dev`
   - `https://www.yourdomain.com`
2. From this `site/` folder, run:

```bash
BASE="$(tr -d ' \t\r\n' < base_url.txt)"
# safety: refuse empty / still-placeholder
if [ -z "$BASE" ] || [ "$BASE" = "BASE_URL" ]; then
  echo "Put your real origin in base_url.txt first"; exit 1
fi
find . -type f \( -name '*.html' -o -name '*.xml' -o -name 'robots.txt' \) \
  -exec sed -i "s|BASE_URL|${BASE}|g" {} +
echo "Replaced BASE_URL → $BASE"
```

Or one-liner after editing `base_url.txt`:

```bash
BASE="$(tr -d ' \t\r\n' < base_url.txt)" && find . -type f \( -name '*.html' -o -name '*.xml' -o -name 'robots.txt' \) -exec sed -i "s|BASE_URL|${BASE}|g" {} +
```

Keep a clean copy of the folder (or git) before replacing, so you can redeploy to a new domain later.

## 2. Deploy — GitHub Pages

1. Create a public GitHub repo (example name: `northpeak-seo`).
2. Copy the **contents** of this `site/` folder to the repo root (or keep them in `/docs` and set Pages source to `/docs`).
3. GitHub → **Settings → Pages**:
   - Source: **Deploy from a branch**
   - Branch: `main` (or `gh-pages`), folder `/` (or `/docs`)
4. Wait for the green check; open `https://<user>.github.io/<repo>/`.
5. Run the `BASE_URL` replace **for that exact URL**, commit, push again.
6. Optional custom domain: Pages → Custom domain → follow GitHub DNS docs; then re-run replace with `https://www.yourdomain.com`.

**Note:** GitHub project sites live under a subpath (`/repo-name/`). Relative links in this site still work. Absolute `BASE_URL` must include that subpath if you use a project site.

## 3. Deploy — Cloudflare Pages

1. Cloudflare Dashboard → **Workers & Pages** → **Create** → **Pages** → Connect git **or** Direct Upload.
2. **Direct Upload:** zip the contents of `site/` (after `BASE_URL` replace) and upload.
3. **Git:** point the build to this folder; build command empty / none; output directory = folder that contains `index.html`.
4. Cloudflare assigns `*.pages.dev`. Put that origin in `base_url.txt`, run replace, redeploy.
5. Optional: attach a custom domain in Pages → Custom domains.

## 4. Submit to Google Search Console

1. Go to [Google Search Console](https://search.google.com/search-console).
2. Add property → **URL prefix** → your `BASE_URL` (with `https://`).
3. Verify with the HTML tag, DNS TXT, or (if on Cloudflare/GitHub) the respective easy method.
4. **Sitemaps** → submit `https://YOUR-ORIGIN/sitemap.xml`.
5. Use **URL Inspection** on `/` and one article; request indexing. Expect **days to a few weeks** for a brand-new domain to show for non-brand queries — indexing ≠ ranking.

## 5. Submit to Bing Webmaster Tools

1. Go to [Bing Webmaster Tools](https://www.bing.com/webmasters).
2. Add site → same URL prefix.
3. Verify (or import from Google Search Console if offered).
4. Submit the same `sitemap.xml`.
5. Bing often picks up after Google; still submit directly.

## 6. Pinterest claim (after live)

When the site is live with HTTPS: Pinterest Business → claim website (meta tag or HTML upload / DNS). Then pin 1–2 educational pins **to article URLs** (separate from product pins). See `../pinterest_profile.md` and `../../promo/boards.md`.

## 7. Honest expectations

- New domain / new Pages URL: Google discovery often **days–weeks**; competitive money keywords take **months** if ever for a thin new site.
- These five articles are solid long-tail helpers; they are not a guarantee of traffic.
- No analytics are included on purpose. Add privacy-friendly analytics later only if you want them.
- Do not invent review counts or testimonials on this site.

## 8. Local preview

```bash
cd /workspace/side_business/seo/site
python3 -m http.server 8080
# open http://127.0.0.1:8080/
```

Relative links work without replacing `BASE_URL`. Replace before public deploy so crawlers see correct canonical/OG URLs.

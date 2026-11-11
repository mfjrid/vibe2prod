# SEO & Discoverability Checklist

Apply primarily to public, indexable pages. Internal systems and authenticated
portals may mark most search-engine controls NOT_APPLICABLE.

## Technical SEO
- [ ] Unique, meaningful title per indexable page.
- [ ] Useful meta description per important page.
- [ ] Canonical URL strategy; host, scheme, trailing slash, and query handling.
- [ ] XML sitemap includes canonical, indexable URLs only.
- [ ] robots.txt is valid and does not accidentally block important assets/pages.
- [ ] noindex/nofollow behavior is intentional for private, duplicate, or staging pages.
- [ ] HTTP status codes are correct (200/301/302/404/410/5xx).
- [ ] Redirects are deliberate, avoid chains/loops, and preserve relevant URLs.
- [ ] Clean, stable, readable URL slugs.
- [ ] Breadcrumbs where useful.
- [ ] Pagination and filtered URLs have a clear indexing strategy.
- [ ] Custom 404 page and useful not-found behavior.
- [ ] HTTPS and preferred hostname are consistent.

## Structured data and social sharing
- [ ] Appropriate Schema.org JSON-LD for actual page content.
- [ ] Structured data is valid and not misleading.
- [ ] Open Graph title, description, type, URL, and image.
- [ ] Social preview image has correct dimensions, absolute URL, and accessible response.
- [ ] Twitter/X card metadata where useful.
- [ ] Favicon and web app metadata.

## Content and media
- [ ] Heading hierarchy is logical and descriptive.
- [ ] Image alt text describes meaningful content; decorative images use empty alt.
- [ ] Images are sized/compressed and use modern formats where suitable.
- [ ] Internal links are crawlable and descriptive.
- [ ] Duplicate/thin content is identified.
- [ ] Language/locale and hreflang are correct for multilingual sites.
- [ ] Important content is available in crawlable HTML, not only client-side interaction.

## Operational checks
- [ ] Staging, preview, and admin environments are not accidentally indexed.
- [ ] Sitemap and robots rules update when content changes.
- [ ] Search Console/Bing Webmaster ownership is documented if relevant.
- [ ] Analytics/consent behavior respects privacy requirements.
- [ ] Broken links and redirect regressions are tested.

Do not recommend meta keywords as a ranking feature; major search engines do not
use them as a meaningful ranking signal. SEO is not applicable to most private
internal applications.

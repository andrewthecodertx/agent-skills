# SEO & Meta Tags Reference

## Essential Meta Tags (Every Page)

```html
<!-- Required -->
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Page Title | Site Name</title>
<meta name="description" content="Compelling, specific description. 150-160 chars.">
<link rel="canonical" href="https://example.com/page-slug">
```

**Title format:** `Page Title | Site Name` (or `Site Name — Page Tagline` for homepage)

**Description rules:**
- 150–160 characters maximum
- Unique per page
- Contains primary keyword naturally
- Written for humans, not stuffed with keywords

## Open Graph Tags

```html
<meta property="og:type" content="website">
<meta property="og:url" content="https://example.com/page-slug">
<meta property="og:site_name" content="Site Name">
<meta property="og:title" content="Page Title">
<meta property="og:description" content="Description">
<meta property="og:image" content="https://example.com/assets/social.webp">
<meta property="og:image:width" content="1200">
<meta property="og:image:height" content="630">
```

**OG image rules:**
- 1200×630px minimum (Facebook, LinkedIn standard)
- Use WebP or PNG (JPEG as fallback)
- Include site branding or title overlay
- Test with [Facebook Sharing Debugger](https://developers.facebook.com/tools/debug/)

## Twitter Card Tags

```html
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:site" content="@handle">
<meta name="twitter:title" content="Page Title">
<meta name="twitter:description" content="Description">
<meta name="twitter:image" content="https://example.com/assets/social.webp">
```

**Card types:**
- `summary` — small image on left
- `summary_large_image` — large image below (preferred for blog posts)

## Article-Specific Tags (Blog Posts)

```html
<meta property="article:published_time" content="2026-01-15T00:00:00.000Z">
<meta property="article:modified_time" content="2026-01-15T00:00:00.000Z">
<meta property="article:author" content="Author Name">
<meta property="article:section" content="Category">
<meta property="article:tag" content="tag1">
<meta property="article:tag" content="tag2">
```

## JSON-LD Structured Data

### Website (Homepage)

```json
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "Site Name",
  "url": "https://example.com",
  "potentialAction": {
    "@type": "SearchAction",
    "target": "https://example.com/search?q={search_term_string}",
    "query-input": "required name=search_term_string"
  }
}
```

### BlogPosting (Each Post)

```json
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "Post Title",
  "description": "Post description",
  "image": "https://example.com/assets/blog/slug.webp",
  "datePublished": "2026-01-15T00:00:00.000Z",
  "dateModified": "2026-01-15T00:00:00.000Z",
  "author": { "@type": "Person", "name": "Author Name" },
  "publisher": { "@type": "Person", "name": "Author Name" },
  "mainEntityOfPage": {
    "@type": "WebPage",
    "@id": "https://example.com/blog/slug"
  },
  "keywords": "tag1, tag2, category"
}
```

### Person (About Page)

```json
{
  "@context": "https://schema.org",
  "@type": "Person",
  "name": "Author Name",
  "alternateName": "Nickname",
  "url": "https://example.com",
  "jobTitle": "Software Developer",
  "sameAs": [
    "https://github.com/username",
    "https://x.com/handle"
  ]
}
```

## Technical SEO Checklist

### robots.txt

```
User-agent: *
Allow: /
Sitemap: https://example.com/sitemap-index.xml
```

### Sitemap

- Auto-generate with your framework's sitemap plugin
- Include all public, prerendered pages
- Set `changefreq` and `priority` appropriately
- Reference in `robots.txt`

### URL Structure

- Use kebab-case: `/blog/my-post-slug`
- No trailing slashes inconsistency (pick one, redirect the other)
- No file extensions in URLs
- Canonical URLs on every page

### Date Rendering Pitfall

When rendering dates in JavaScript:

```js
// ❌ WRONG — parses date-only strings as UTC midnight, shifts in local timezone
new Date('2026-06-05').toLocaleDateString() // → "Jun 4" in UTC-5

// ✅ CORRECT — parse explicitly as UTC
function formatDate(input: string): string {
  const [year, month, day] = input.split('-').map(Number);
  const date = new Date(Date.UTC(year, month - 1, day));
  return date.toLocaleDateString('en-US', {
    timeZone: 'UTC',
    year: 'numeric',
    month: 'short',
    day: 'numeric',
  });
}
```

### Performance & SEO

- Core Web Vitals directly affect ranking
- Use `<link rel="preload">` for critical fonts and above-fold images
- Use `<link rel="preconnect">` for third-party origins
- Minimize render-blocking resources
- Ensure mobile-friendly (responsive, no horizontal scroll)
---
name: web-development
description: "Modern web development reference and skill: standards, layouts, color systems, accessibility, performance, and responsive design patterns for building high-quality websites."
version: 1.0.0
author: Andrew S Erwin
license: MIT
platforms: [linux, macos, windows]
metadata:
  enchanter:
    tags:
      [
        web,
        css,
        html,
        accessibility,
        performance,
        responsive,
        design-systems,
        color-theory,
        layouts,
        seo,
      ]
    related_skills: [blog-post-publish, add-site-demo]
---

# Modern Web Development Reference

A comprehensive reference for building modern, high-quality websites. Grounded
in 2024–2026 web standards, accessibility laws, performance budgets, and contemporary
design practice.

## 1. Color Systems

### 1.1 The Modern Approach: OKLCH + CSS Custom Properties

**Use OKLCH** as the primary color space for defining palettes. Unlike HSL, OKLCH
is perceptually uniform — equal numeric steps produce equal visual steps. This
eliminates the "HSL lightness lie" where `hsl(0, 0%, 50%)` and `hsl(240, 100%, 50%)`
look wildly different in perceived brightness despite identical L values.

```css
/* Define palette in OKLCH, then expose as custom properties */
:root {
  /* Neutral ramp — perceptually uniform steps */
  --gray-50: oklch(98% 0.002 260);
  --gray-100: oklch(96% 0.004 260);
  --gray-200: oklch(90% 0.006 260);
  --gray-300: oklch(80% 0.008 260);
  --gray-400: oklch(65% 0.01 260);
  --gray-500: oklch(50% 0.012 260);
  --gray-600: oklch(38% 0.014 260);
  --gray-700: oklch(28% 0.014 260);
  --gray-800: oklch(20% 0.012 260);
  --gray-900: oklch(14% 0.01 260);
  --gray-950: oklch(08% 0.008 260);

  /* Accent — pick a hue, vary lightness */
  --accent-50: oklch(95% 0.15 160);
  --accent-400: oklch(70% 0.18 160);
  --accent-500: oklch(60% 0.2 160);
  --accent-600: oklch(50% 0.22 160);
  --accent-700: oklch(40% 0.2 160);
}
```

**Why OKLCH over alternatives:**

| Property               | OKLCH                 | HSL                     | LAB                       | sRGB hex  |
| ---------------------- | --------------------- | ----------------------- | ------------------------- | --------- |
| Perceptually uniform   | Yes                   | No                      | Partially (a/b hues skew) | No        |
| Wide gamut (P3+)       | Yes                   | No (clips to sRGB)      | Yes                       | No        |
| Intuitive hue rotation | Yes (constant chroma) | Yes (but bad lightness) | No (a/b axes)             | No        |
| Browser support        | 95%+ (2024+)          | Universal               | 95%+                      | Universal |
| Interpolation quality  | Excellent             | Poor                    | Good                      | Poor      |

**Fallback strategy for old browsers:**

```css
:root {
  --accent-500: #22c55e; /* sRGB fallback */
  --accent-500: oklch(62% 0.2 145); /* modern browsers override */
}
```

### 1.2 Color Contrast & Accessibility

**WCAG 2.2 minimum contrast ratios** (enforce these as hard constraints):

| Element type                      | AA minimum | AAA target |
| --------------------------------- | ---------- | ---------- |
| Body text                         | 4.5:1      | 7:1        |
| Large text (>18px bold / >24px)   | 3:1        | 4.5:1      |
| UI components & graphical objects | 3:1        | 4.5:1      |
| Focus indicators                  | 3:1        | 4.5:1      |

**Dark mode specific rules:**

- Never use pure `#000` backgrounds with `#fff` text — the contrast is too harsh
  (21:1), causing eye strain and halation. Target 12:1–16:1.
- Use off-black backgrounds: `#0a0a0a` to `#1a1a1a` with `#d0d0d0` to `#e0e0e0` text.
- Dim secondary text to 65–75% of foreground brightness (not pure gray — maintain slight hue).
- Borders should be visible but not dominant: `#2a2a2a` to `#333` on `#0a0a0a` backgrounds.

**Testing tools:** Use `oklch()` contrast checking in Chrome DevTools (2024+) or Polypane's built-in contrast analyzer. For automated CI, `pa11y` with `WCAG2AAA` standard.

### 1.3 Palette Generation Strategy

**Single-accent dark theme:**

1. Pick one accent hue (e.g., green — `oklch(70% 0.18 150)`)
2. Optional secondary accents: amber for warnings/attention, cyan for links/navigation
3. Generate a neutral ramp with slight hue bias (cool — hue 260 for professional feel)
4. Ensure each semantic role has exactly one color — avoid "which green is this?"

**Multi-theme support:**

```css
/* Define semantic tokens, not colors directly */
:root {
  --color-bg: var(--gray-950);
  --color-surface: var(--gray-900);
  --color-border: var(--gray-800);
  --color-text: var(--gray-100);
  --color-text-dim: var(--gray-400);
  --color-accent: var(--accent-500);
  --color-accent-dim: var(--accent-700);
  --color-link: var(--cyan-400);
  --color-error: var(--red-400);
  --color-success: var(--accent-400);
}

[data-theme="light"] {
  --color-bg: var(--gray-50);
  --color-surface: var(--gray-100);
  --color-border: var(--gray-200);
  --color-text: var(--gray-900);
  --color-text-dim: var(--gray-600);
  --color-accent: var(--accent-600);
  /* Accent hue shifts darker on light backgrounds */
}
```

### 1.4 Color Naming Conventions

**Semantic tokens only in component CSS:**

```css
/* ✅ GOOD — semantic, themeable */
.button {
  background: var(--color-accent);
  color: var(--color-bg);
}

/* ❌ BAD — hardcoded, unthemeable */
.button {
  background: #4ade80;
  color: #0a0a0a;
}
```

**Token hierarchy:**

1. **Primitive tokens** — the raw values (`--gray-500`, `--accent-400`)
2. **Semantic tokens** — meaning-based (`--color-text`, `--color-bg`)
3. **Component tokens** — scoped to a component (`--button-bg`, `--card-border`)

Components should only reference semantic tokens. Primitives are used only in the theme layer.

## 2. Layout Systems

### 2.1 Modern CSS Layout: Grid + Flexbox, Not Floats

**Page-level layout:** Use CSS Grid for overall page structure.

```css
body {
  display: grid;
  grid-template-rows: auto 1fr auto;
  min-height: 100vh; /* or 100dvh for dynamic viewport */
}
```

**Content-width layout:** Use a max-width container with auto margins.

```css
.container {
  max-width: 65ch; /* optimal reading width for text */
  margin-inline: auto;
  padding-inline: 1rem; /* edge case for narrow screens */
}

/* For mixed content (code + text): */
.container-wide {
  max-width: 72rem; /* or equivalent in your framework */
  margin-inline: auto;
  padding-inline: 1rem;
}
```

**Card/grid layouts:** Use `grid` with `auto-fill`/`auto-fit` + `minmax()`:

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 300px), 1fr));
  gap: 1.5rem;
}
```

### 2.2 The Responsive Container Query Pattern (2024+)

Container queries (`@container`) are now supported in all modern browsers and are preferred over media queries for component-level responsiveness:

```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 400px) {
  .card {
    grid-template-columns: 1fr 2fr;
  }
}

@container card (max-width: 399px) {
  .card {
    grid-template-columns: 1fr;
  }
}
```

**When to use what:**

- **Page-level** breakpoints (nav, sidebar, hero): `@media` queries
- **Component-level** responsiveness (cards, widgets, lists): `@container` queries
- **Text sizing** that adapts to container: `clamp()` + container units

### 2.3 The `clamp()` Pattern for Fluid Typography

Never use a single breakpoint for font sizes. Use `clamp()` for smooth scaling:

```css
html {
  /* 16px at 320px viewport → 20px at 1200px+ viewport */
  font-size: clamp(1rem, 0.9rem + 0.25vw, 1.25rem);
}

h1 {
  /* 28px → 48px, scaling with viewport */
  font-size: clamp(1.75rem, 1.5rem + 1vw, 3rem);
  line-height: 1.1;
}

h2 {
  font-size: clamp(1.5rem, 1.3rem + 0.5vw, 2.25rem);
  line-height: 1.2;
}
```

**Line-height rules:**

- Headings: 1.1–1.3 (tighter because large text)
- Body text: 1.5–1.7 (generous for readability)
- Code/dense: 1.4

### 2.4 CSS Nesting (2024+ Standard)

CSS nesting is now supported in all modern browsers without preprocessor:

```css
.card {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: 0.5rem;

  & .title {
    font-size: 1.125rem;
    color: var(--color-text);
  }

  &:hover {
    border-color: var(--color-accent);
  }

  @media (width >= 768px) {
    grid-template-columns: 1fr 1fr;
  }
}
```

### 2.5 Layout Anti-Patterns to Avoid

1. **`100vh` on mobile** — Use `100dvh` (dynamic viewport height) which accounts for browser chrome.
2. **Fixed pixel widths** — Always use `max-width` + `auto` margins or `clamp()` for fluid scaling.
3. **Float-based layouts** — Use Grid or Flexbox exclusively. Floats are for text wrapping around images, not page layout.
4. **Absolute positioning for layout** — Only for overlays, tooltips, dropdowns. Never for columns or grids.
5. **`!important`** — If you need it, your CSS specificity is wrong. Fix the cascade.

## 3. Typography

### 3.1 Font Selection Strategy

**System font stack** (zero network requests, instant render):

```css
font-family:
  -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Oxygen, Ubuntu,
  Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
```

**Monospace stack** (for code/terminal aesthetics):

```css
font-family:
  "JetBrains Mono", ui-monospace, "SF Mono", Monaco, "Cascadia Code",
  "Roboto Mono", Menlo, Consolas, monospace;
```

**Web font loading best practices:**

```css
@font-face {
  font-family: "Custom Font";
  src: url("/fonts/custom-regular.woff2") format("woff2");
  font-weight: 400;
  font-display: swap; /* CRITICAL: show fallback, swap when loaded */
  unicode-range: U+0020-007E; /* ASCII range — split if CJK needed */
}
```

**Rules:**

1. Always `font-display: swap` or `font-display: optional`. Never `block`.
2. Subset fonts to only used characters (use `pyftsubset` or `glyphhanger`).
3. Use WOFF2 exclusively — no WOFF, TTF, or EOT needed in 2026.
4. Preload the critical font: `<link rel="preload" as="font" href="/font.woff2" crossorigin>`
5. Maximum 2 font families (one display, one body). One monospace is fine.
6. Use a type scale for consistent sizing:

```css
:root {
  --text-xs: 0.75rem; /* 12px */
  --text-sm: 0.875rem; /* 14px */
  --text-base: 1rem; /* 16px */
  --text-lg: 1.125rem; /* 18px */
  --text-xl: 1.25rem; /* 20px */
  --text-2xl: 1.5rem; /* 24px */
  --text-3xl: 1.875rem; /* 30px */
  --text-4xl: 2.25rem; /* 36px */
}
```

### 3.2 Responsive Typography Scale

Use `clamp()` for fluid text that scales with viewport, not multiple breakpoints:

```css
h1 {
  font-size: clamp(2rem, 1.5rem + 2.5vw, 3.5rem);
}
h2 {
  font-size: clamp(1.5rem, 1.25rem + 1.5vw, 2.5rem);
}
h3 {
  font-size: clamp(1.25rem, 1.1rem + 0.75vw, 1.75rem);
}
body {
  font-size: clamp(1rem, 0.95rem + 0.25vw, 1.125rem);
}
```

**Reading width:** `max-width: 65ch` for body text (optimal for English). Up to `72rem` for mixed content with code blocks.

## 4. Accessibility

### 4.1 WCAG 2.2 Requirements

- **Perceivable:** Text alternatives for images, captions for video, adaptable content presentation
- **Operable:** Keyboard accessible, enough time to read, no seizure triggers, navigable
- **Understandable:** Readable text, predictable navigation, input assistance
- **Robust:** Compatible with current and future assistive technologies

**Key numeric targets:**

- Color contrast: 4.5:1 minimum for text, 3:1 for large text and UI
- Touch targets: minimum 44×44px (WCAG 2.5.8) for all interactive elements
- Text resize: content must work at 200% zoom without horizontal scroll
- Focus visible: 2px+ outline or 3:1 contrast ratio for focus indicators

### 4.2 Focus Management

```css
/* NEVER remove focus outlines — customize them instead */
*:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}

/* Keyboard navigation: all interactive elements must be reachable via Tab */
/* Focus order must be logical (DOM order) */
/* Never use tabindex > 0 */
```

### 4.3 Skip Link (Required)

```css
.skip-link {
  position: absolute;
  top: -100%;
  left: 0;
  background: var(--color-accent);
  color: var(--color-bg);
  padding: 0.5rem 1rem;
  z-index: 100;
}

.skip-link:focus {
  top: 0;
}
```

```html
<a href="#main" class="skip-link">Skip to main content</a>
```

### 4.4 prefers-reduced-motion

```css
@media (prefers-reduced-motion: reduce) {
  *,
  *::before,
  *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

### 4.5 Semantic HTML Checklist

- `<html lang="en">` — always set language
- `<main>` — wraps primary content (one per page)
- `<nav aria-label="...">` — for navigation regions
- `<article>` — self-contained content (blog posts, project pages)
- `<section>` — thematic grouping with heading
- `<aside>` — supplementary content (sidebar)
- `<header>` / `<footer>` — page or section headers/footers
- `<figure>` + `<figcaption>` — images with captions
- `<time datetime="...">` — machine-readable dates
- `role="search"` — on search forms

## 5. Performance

### 5.1 Core Web Vitals Targets

| Metric                          | Target  | Measurement          |
| ------------------------------- | ------- | -------------------- |
| LCP (Largest Contentful Paint)  | < 2.5s  | Main content visible |
| INP (Interaction to Next Paint) | < 200ms | Input responsiveness |
| CLS (Cumulative Layout Shift)   | < 0.1   | Visual stability     |
| FCP (First Contentful Paint)    | < 1.8s  | First pixel          |
| TTFB (Time to First Byte)       | < 800ms | Server response      |

### 5.2 Image Strategy

**Format priority:** AVIF > WebP > JPEG/PNG (with `<picture>` fallback chain):

```html
<picture>
  <source srcset="hero.avif" type="image/avif" />
  <source srcset="hero.webp" type="image/webp" />
  <img
    src="hero.jpg"
    alt="Description"
    width="800"
    height="450"
    loading="lazy"
    decoding="async"
  />
</picture>
```

**Rules:**

1. Always set `width` and `height` attributes to prevent CLS (layout shift).
2. Above-the-fold images: `loading="eager"` + `fetchpriority="high"`.
3. Below-the-fold images: `loading="lazy"` + `decoding="async"`.
4. Maximum dimensions: 1200–1600px wide for hero, 800px for cards, 128px for icons.
5. Use `srcset` for responsive images when art direction matters.
6. Blog/social images: 1200×630 (OG standard) or 2048×1024.
7. Use WebP for all content images (AVIF where browser support allows).

### 5.3 JavaScript Budget

**Rules:**

- Total JS bundle < 100KB compressed for content sites
- Use `<script>` with appropriate loading strategy (`defer`, `async`, `is:inline`)
- Defer non-critical scripts
- No third-party scripts except analytics and essential integrations
- Consider if JavaScript is needed at all — many interactions work with CSS alone

### 5.4 CSS Strategy

**Modern approach:** Utility classes in templates + custom properties in a theme layer. Let the build tool handle purging.

**Optimizations:**

- Keep `global.css` minimal — only theme tokens, typography resets, and code block overrides
- Use a typography plugin for prose content (e.g., `@tailwindcss/typography`)
- Avoid `@apply` in component files — prefer utility classes directly in templates
- CSS nesting is native — no preprocessor needed

### 5.5 Caching & Headers

For static assets, set aggressive cache headers:

```nginx
# Nginx
location /assets/ {
    expires 365d;
    add_header Cache-Control "public, immutable";
    add_header Vary "Accept";
}

location /js/ {
    expires 365d;
    add_header Cache-Control "public, immutable";
}
```

For HTML pages, use short cache with revalidation:

```nginx
location / {
    add_header Cache-Control "public, max-age=300, stale-while-revalidate=86400";
}
```

## 6. SEO & Meta Tags

### 6.1 Essential Meta Tags

Verify these on every new page:

```html
<!-- Required -->
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Page Title | Site Name</title>
<meta
  name="description"
  content="Compelling, specific description. 150-160 chars."
/>
<link rel="canonical" href="https://example.com/page-slug" />

<!-- Open Graph -->
<meta property="og:type" content="website|article" />
<meta property="og:url" content="https://example.com/page-slug" />
<meta property="og:site_name" content="Site Name" />
<meta property="og:title" content="Page Title" />
<meta property="og:description" content="Description" />
<meta property="og:image" content="https://example.com/assets/social.webp" />
<meta property="og:image:width" content="1200" />
<meta property="og:image:height" content="630" />

<!-- Twitter (X) -->
<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:site" content="@handle" />
<meta name="twitter:title" content="Page Title" />
<meta name="twitter:description" content="Description" />
<meta name="twitter:image" content="https://example.com/assets/social.webp" />

<!-- Article-specific (blog posts only) -->
<meta property="article:published_time" content="2026-01-15T00:00:00.000Z" />
<meta property="article:author" content="Author Name" />
<meta property="article:section" content="Category" />
<meta property="article:tag" content="tag1" />
<meta property="article:tag" content="tag2" />
```

### 6.2 Structured Data (JSON-LD)

**Website** (homepage):

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

**BlogPosting** (each blog post):

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

### 6.3 Sitemap & Robots

- Use your framework's sitemap plugin for automatic generation
- `robots.txt` should allow all crawlers and reference the sitemap
- Ensure prerendered pages are discoverable

## 7. Component Patterns

### 7.1 Navigation Pattern

**Desktop:** Horizontal nav with dropdown menus.

**Mobile:** Must provide accessible navigation — either hamburger menu or horizontal scroll.

**Accessibility requirements for dropdowns:**

- Dropdowns must be keyboard-accessible (Enter/Space to open, Escape to close)
- Use `aria-expanded`, `aria-haspopup`, `role="menu"`, `role="menuitem"`
- Close on focus loss or Escape
- Consider adding keyboard support if using CSS-only hover dropdowns

### 7.2 Page Layout Pattern

```
<Header> — terminal prompt + nav + dropdowns
  max-w-3xl, border-b
  <main class="flex-1 py-8">
    max-w-3xl (blog) or max-w-5xl (home)
    Content area with px-4 padding
    Article text: prose-invert + max-w-none
    Code blocks: dark bg, rounded
  </main>
<Footer> — copyright + social links
  max-w-3xl, border-t
```

**Flexbox sticky footer:** `min-h-screen` + `flex flex-col` on body, `flex-1` on main.

### 7.3 Interactive Demo Pattern

Self-contained demo pages with IIFE scripts:

```html
<div class="px-4 py-2"><!-- Controls bar --></div>
<div id="demo-container"><!-- Demo DOM --></div>
<script src="/js/demo-name.js"></script>
```

**Critical rules:**

1. Use `is:inline` (or equivalent) to prevent framework bundling/transforming the script
2. Ensure re-execution on client-side navigation (ViewTransitions, etc.)
3. Use `readyState` check instead of `DOMContentLoaded` for initialization:
   ```js
   if (document.readyState === "loading") {
     document.addEventListener("DOMContentLoaded", init);
   } else {
     init(); // Already loaded — ViewTransitions case
   }
   ```
4. Deduplicate event listeners by storing state on `window`

### 7.4 Blog Post Pattern

- Prerendered at build time for static sites
- Date rendering: parse date-only strings carefully to avoid timezone shifts (use UTC or manual parsing)
- Include JSON-LD structured data for every blog post
- Include OG/Twitter meta tags
- Use a prose typography plugin for readable article text
- Code blocks should use syntax highlighting with a dark theme

## 8. Security Headers

Essential security headers for production:

```nginx
# Nginx
add_header X-Content-Type-Options "nosniff" always;
add_header X-Frame-Options "SAMEORIGIN" always;
add_header X-XSS-Protection "1; mode=block" always;
add_header Referrer-Policy "strict-origin-when-cross-origin" always;
add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
```

## 9. Dark Mode

### 9.1 Implementation Strategies

**Option A: `prefers-color-scheme` only** (automatic, no toggle needed):

```css
:root {
  --color-bg: #ffffff;
  --color-text: #1a1a1a;
}
@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: #0a0a0a;
    --color-text: #e0e0e0;
  }
}
```

**Option B: Class-based toggle** (user preference + system default):

```css
:root {
  /* light theme */
}
[data-theme="dark"] {
  /* dark theme */
}
```

```js
// Toggle with localStorage persistence
const toggle = () => {
  const current = document.documentElement.dataset.theme;
  const next = current === "dark" ? "light" : "dark";
  document.documentElement.dataset.theme = next;
  localStorage.setItem("theme", next);
};
// Initialize: check localStorage, then system preference
const saved = localStorage.getItem("theme");
const system = window.matchMedia("(prefers-color-scheme: dark)").matches;
document.documentElement.dataset.theme = saved || (system ? "dark" : "light");
```

### 9.2 Dark Mode Pitfalls

- **Never use pure `#000` on `#fff`** — too much contrast (21:1), causes halation. Use off-black/off-white.
- **Dim text must maintain readability** — 65–75% of foreground brightness, with slight hue.
- **Shadows must be lighter in dark mode** — use `rgba(255,255,255,0.05)` or similar.
- **Images may need brightness reduction** — `filter: brightness(0.9)` for photos.
- **Code blocks** — use a dark theme that's distinct from the background (e.g., Nord on near-black).

## 10. Deployment

### 10.1 Static Site Deployment Checklist

- [ ] Build generates static output
- [ ] All images are WebP/AVIF with fallbacks
- [ ] All images have `width`/`height` attributes
- [ ] All pages have canonical URLs
- [ ] OG/Twitter meta tags on all public pages
- [ ] JSON-LD on blog posts and homepage
- [ ] `robots.txt` and sitemap present
- [ ] Security headers set (HSTS, X-Content-Type-Options, etc.)
- [ ] HTTPS redirect configured
- [ ] Cache headers set for static assets
- [ ] 404 page exists and is styled

### 10.2 Performance Checklist for New Pages

- [ ] All images use WebP/AVIF with `width`/`height` attributes
- [ ] Hero/above-fold images have `fetchpriority="high"`
- [ ] Below-fold images use `loading="lazy"`
- [ ] No layout shift (set dimensions on all media)
- [ ] Scripts load with appropriate strategy (`defer`, `async`, or inline)
- [ ] Static pages are prerendered
- [ ] JSON-LD structured data on blog posts
- [ ] Canonical URL set
- [ ] OG/Twitter meta tags present
- [ ] Accessibility: `lang`, `alt`, focus rings, skip link, semantic HTML
- [ ] Lighthouse/pa11y passes at target level

## 11. Design Patterns Reference

### 11.1 Terminal Aesthetic Pattern

A distinctive terminal aesthetic uses consistent visual language:

| Element     | Pattern                         | Example                                           |
| ----------- | ------------------------------- | ------------------------------------------------- |
| Page title  | `$ command` format              | `$ about`, `$ tools_of_the_trade`                 |
| Navigation  | Terminal prompt style           | `user@host:~$` as site header                     |
| Active nav  | `[brackets]`                    | `[blog]`, `[projects]`                            |
| Dim text    | `> prefix`                      | `> 25+ years shipping production software`        |
| Links       | Cyan/accent color               | `text-terminal-cyan` or `var(--color-link)`       |
| Hover       | Accent color transition         | `hover:text-accent-green`                         |
| Buttons     | `[brackets]` with accent border | `[hire me]`, `[source]`                           |
| Categories  | `[brackets]`                    | `[Software Development]`                          |
| Borders     | Subtle border color             | `border-terminal-border` or `var(--color-border)` |
| Code blocks | Dark theme                      | Nord theme or similar dark palette                |
| Inline code | Accent on dark bg               | Green on near-black                               |

### 11.2 Responsive Breakpoints

| Name  | Min-width | Use case                               |
| ----- | --------- | -------------------------------------- |
| `sm`  | 640px     | Two-column cards, larger touch targets |
| `md`  | 768px     | Side-by-side layouts, wider nav        |
| `lg`  | 1024px    | Three-column grids, sidebar + content  |
| `xl`  | 1280px    | Max-width containers, wide layouts     |
| `2xl` | 1536px    | Ultra-wide content                     |

**Common content widths:**

- Blog/text: `max-w-3xl` (48rem / 768px) — optimal reading width
- Home/cards: `max-w-5xl` (64rem / 1024px) — wider for cards + grid
- Demos: full-width container (canvas-based)

### 11.3 Animation Guidelines

- **Duration:** 150–300ms for interactive feedback, 500ms for page transitions
- **Easing:** `ease-out` for appearing, `ease-in` for disappearing
- **Respect `prefers-reduced-motion`** — always provide reduced-motion fallback
- **Animate `transform` and `opacity` only** — never animate `width`, `height`, `top`, `left`
- **Use CSS transitions** over JS animation libraries for simple effects
- **View Transitions** are now widely supported for page-level transitions

## 12. Quick Reference: New Page Template

When creating a new page, use this checklist:

1. Prerender static content pages
1. Set correct page title and description for SEO
1. Add JSON-LD if it's a blog post or landing page
1. Add OG/Twitter meta tags
1. Set canonical URL
1. Use appropriate content width (`max-w-3xl` for text, `max-w-5xl` for cards)
1. Apply design system colors consistently
1. Use semantic HTML (`<section>`, `<article>`, `<nav>`, `<main>`)
1. Add `border-b` section dividers between sections
1. Use `py-12` vertical padding for sections
1. Verify accessibility (lang, alt, focus rings, skip link)
1. Run linting/formatting pass before committing


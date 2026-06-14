# Web Performance Reference

## Core Web Vitals Targets

| Metric | Good | Needs Improvement | Poor |
|--------|------|--------------------|------|
| LCP | ≤ 2.5s | 2.5s – 4.0s | > 4.0s |
| INP | ≤ 200ms | 200ms – 500ms | > 500ms |
| CLS | ≤ 0.1 | 0.1 – 0.25 | > 0.25 |
| FCP | ≤ 1.8s | 1.8s – 3.0s | > 3.0s |
| TTFB | ≤ 800ms | 800ms – 1800ms | > 1800ms |

## Image Optimization Checklist

### Format Selection

| Format | Best For | Browser Support | Compression |
|--------|----------|----------------|-------------|
| AVIF | Photos, complex graphics | 92%+ (2024) | Best (50% smaller than WebP) |
| WebP | Photos, graphics with transparency | 97%+ | Good (30% smaller than JPEG) |
| JPEG | Fallback for photos | 100% | Baseline |
| PNG | Fallback for graphics/screenshots | 100% | Worst |
| SVG | Icons, logos, illustrations | 100% | N/A (vector) |

### Picture Element Pattern

```html
<picture>
  <source srcset="hero.avif" type="image/avif">
  <source srcset="hero.webp" type="image/webp">
  <img src="hero.jpg" alt="Description" width="800" height="450"
       loading="lazy" decoding="async">
</picture>

<!-- Above-the-fold (hero) -->
<picture>
  <source srcset="hero.avif" type="image/avif">
  <source srcset="hero.webp" type="image/webp">
  <img src="hero.jpg" alt="Description" width="1200" height="675"
       loading="eager" fetchpriority="high" decoding="async">
</picture>
```

### Rules

1. **Always set `width` and `height`** — prevents CLS
2. **Hero/above-fold**: `loading="eager"` + `fetchpriority="high"`
3. **Below-fold**: `loading="lazy"` + `decoding="async"`
4. **Max dimensions**: 1200–1600px hero, 800px cards, 128px icons
5. **Social images**: 1200×630 (OG standard)

## JavaScript Performance

### Loading Strategies

| Attribute | Parse | Execute | Use Case |
|-----------|-------|---------|----------|
| `<script>` | Blocks | Blocks | Critical inline only |
| `<script defer>` | Parallel | After DOM | Most scripts (default) |
| `<script async>` | Parallel | When ready | Analytics, 3rd party |
| `<script is:inline>` | Blocks | Blocks | Pre-built bundles, demos |

### Bundle Size Guidelines

| Site type | Max JS (gzipped) | Notes |
|-----------|-------------------|-------|
| Content/blog | < 50KB | Mostly static, minimal JS |
| SaaS dashboard | < 150KB | Framework + components |
| Interactive demo | < 200KB | Canvas + logic |

### Common Performance Fixes

- **Large bundles**: Code-split with dynamic imports
- **Render-blocking JS**: Add `defer` or `async`
- **Unused CSS**: Purge with build tool (Tailwind does this automatically)
- **Layout shifts**: Set `width`/`height` on all media elements
- **Slow fonts**: Use `font-display: swap`, subset, preload critical font

## Caching Strategy

```
Static assets (CSS, JS, images):
  Cache-Control: public, max-age=31536000, immutable
  Vary: Accept (for content negotiation)

HTML pages:
  Cache-Control: public, max-age=300, stale-while-revalidate=86400

API responses:
  Cache-Control: private, max-age=0, must-revalidate
```

## Lighthouse Audit Checklist

- [ ] Performance score ≥ 90
- [ ] Accessibility score ≥ 95
- [ ] Best Practices score ≥ 90
- [ ] SEO score ≥ 90
- [ ] All images have `width`/`height`
- [ ] All images use modern formats (WebP/AVIF)
- [ ] No render-blocking resources
- [ ] Text remains visible during font load (`font-display: swap`)
- [ ] No layout shifts (CLS < 0.1)
- [ ] HTTPS enabled
- [ ] Security headers present
# Accessibility (a11y) Reference

## WCAG 2.2 Quick Reference

### Contrast Ratios

| Element | AA Minimum | AAA Target |
|---------|-----------|-----------|
| Body text (< 18px) | 4.5:1 | 7:1 |
| Large text (≥ 18px bold / ≥ 24px) | 3:1 | 4.5:1 |
| UI components & graphical objects | 3:1 | 4.5:1 |
| Focus indicators | 3:1 | 4.5:1 |

### Touch Targets

- Minimum 44×44px for all interactive elements (WCAG 2.5.8)
- Minimum 24px between adjacent targets
- Exceptions: inline links in text blocks

### Text Resize

- Content must work at 200% zoom without horizontal scroll
- Text must reflow properly at 320px width

## Semantic HTML Requirements

### Required Elements

```html
<html lang="en">          <!-- Always set language -->
<head>
  <meta charset="utf-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Page Title | Site Name</title>
</head>
<body>
  <a href="#main" class="skip-link">Skip to main content</a>
  <header>...</header>
  <nav aria-label="Main navigation">...</nav>
  <main id="main">...</main>
  <aside>...</aside>
  <footer>...</footer>
</body>
```

### ARIA Landmarks

| Role | Element | Purpose |
|------|---------|---------|
| `banner` | `<header>` | Page header |
| `navigation` | `<nav>` | Navigation regions |
| `main` | `<main>` | Primary content |
| `complementary` | `<aside>` | Supplementary content |
| `contentinfo` | `<footer>` | Page footer |
| `search` | `<form>` with `role="search"` | Search functionality |
| `region` | `<section>` with `aria-label` | Thematic grouping |

### Images

```html
<!-- Informative image -->
<img src="chart.webp" alt="Sales increased 23% in Q4 2025">

<!-- Decorative image -->
<img src="divider.webp" alt="" role="presentation">

<!-- Complex image with description -->
<figure>
  <img src="dashboard.webp" alt="Dashboard showing quarterly metrics">
  <figcaption>Q4 2025 metrics: revenue up 23%, users up 15%</figcaption>
</figure>
```

### Interactive Elements

```html
<!-- Buttons vs Links -->
<button type="button">Action</button>  <!-- Triggers action on page -->
<a href="/page">Navigation</a>          <!-- Navigates to new URL -->

<!-- Form labels -->
<label for="email">Email address</label>
<input type="email" id="email" required aria-describedby="email-hint">
<span id="email-hint">We'll never share your email.</span>

<!-- Error messages -->
<input type="text" id="name" required aria-invalid="true" aria-describedby="name-error">
<span id="name-error" role="alert">Name is required.</span>
```

## Focus Management

### Focus Styles

```css
/* NEVER remove focus outlines — customize them instead */
*:focus-visible {
  outline: 2px solid var(--color-accent);
  outline-offset: 2px;
}

/* Remove default only for mouse clicks, not keyboard */
*:focus:not(:focus-visible) {
  outline: none;
}
```

### Focus Order

- All interactive elements must be reachable via Tab
- Focus order must follow DOM order (logical reading order)
- Never use `tabindex` > 0 (causes tab order chaos)
- Use `tabindex="-1"` only for programmatic focus (skip links, modal triggers)

### Skip Link

```html
<a href="#main" class="skip-link">Skip to main content</a>
```

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

## prefers-reduced-motion

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    animation-iteration-count: 1 !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

## Keyboard Accessibility

### Dropdown Menus

- Enter/Space: open menu
- Arrow keys: navigate items
- Escape: close menu
- Tab: move to next focusable element (close menu if open)

### Modal Dialogs

- Escape: close modal
- Tab trap: focus must cycle within modal
- Return focus to trigger on close

### Common Keyboard Shortcuts

| Key | Action |
|-----|--------|
| Tab | Next focusable element |
| Shift+Tab | Previous focusable element |
| Enter | Activate button/link |
| Space | Activate button/toggle |
| Escape | Close/dismiss |
| Arrow keys | Navigate within components |

## Testing Tools

- **pa11y** — CLI accessibility testing (CI integration)
- **axe-core** — Browser extension + automated testing
- **Lighthouse** — Chrome DevTools accessibility audit
- **screen reader** — VoiceOver (Mac), NVDA (Windows), Orca (Linux)
- **Keyboard testing** — Tab through every page, verify all interactions
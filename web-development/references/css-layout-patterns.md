# CSS Layout Patterns Reference

## Page Layouts

### Sticky Footer (Flexbox)

```css
body {
  display: flex;
  flex-direction: column;
  min-height: 100vh; /* or 100dvh for mobile */
}
main {
  flex: 1;
}
```

### Holy Grail Layout (Sidebar + Content + Aside)

```css
.layout {
  display: grid;
  grid-template-columns: 240px 1fr 240px;
  gap: 1.5rem;
}

@media (max-width: 1024px) {
  .layout {
    grid-template-columns: 1fr;
  }
}
```

### Sidebar + Content

```css
.layout {
  display: grid;
  grid-template-columns: 240px 1fr;
  gap: 1.5rem;
}

@media (max-width: 768px) {
  .layout {
    grid-template-columns: 1fr;
  }
}
```

## Content Widths

| Content type | Width | CSS |
|-------------|-------|-----|
| Body text (optimal reading) | 65ch (~768px) | `max-width: 65ch` |
| Mixed content (code + text) | 72rem (~1152px) | `max-width: 72rem` |
| Full-width (demos, canvases) | 100% | `max-width: 100%` |

## Card Grids

```css
.card-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(min(100%, 300px), 1fr));
  gap: 1.5rem;
}
```

**Why `min(100%, 300px)`?** Prevents overflow on narrow screens — when the container is < 300px, the card fills 100% instead.

## Container Queries (Component-Level Responsive)

```css
.card-container {
  container-type: inline-size;
  container-name: card;
}

@container card (min-width: 400px) {
  .card { grid-template-columns: 1fr 2fr; }
}

@container card (max-width: 399px) {
  .card { grid-template-columns: 1fr; }
}
```

## Fluid Typography

```css
html {
  /* 16px → 20px scaling with viewport */
  font-size: clamp(1rem, 0.9rem + 0.25vw, 1.25rem);
}

h1 { font-size: clamp(1.75rem, 1.5rem + 1vw, 3rem); }
h2 { font-size: clamp(1.5rem, 1.3rem + 0.5vw, 2.25rem); }
h3 { font-size: clamp(1.25rem, 1.1rem + 0.3vw, 1.75rem); }
```

## Common Patterns

### Centered Container

```css
.container {
  max-width: 65ch;
  margin-inline: auto;
  padding-inline: 1rem;
}
```

### Full-Bleed Within Container

```css
.article {
  max-width: 65ch;
  margin-inline: auto;
}

.article img {
  width: 100vw;
  margin-inline: calc(50% - 50vw);
}
```

### Aspect Ratio

```css
.video-wrapper {
  aspect-ratio: 16 / 9;
}

.avatar {
  aspect-ratio: 1;
  object-fit: cover;
}
```

### Sticky Header

```css
header {
  position: sticky;
  top: 0;
  z-index: 50;
  backdrop-filter: blur(12px);
  background: rgba(10, 10, 10, 0.85);
}
```

## Anti-Patterns

1. **`100vh` on mobile** → use `100dvh`
2. **Fixed pixel widths** → use `max-width` + `auto` margins or `clamp()`
3. **Float layouts** → use Grid or Flexbox
4. **Absolute positioning for layout** → only for overlays
5. **`!important`** → fix the cascade specificity instead
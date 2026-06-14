# OKLCH Color Reference

## Quick Reference: OKLCH Function

```css
color: oklch(L C H);
/* L = Lightness (0%–100%)
   C = Chroma (0–0.4, saturation)
   H = Hue (0–360 degrees) */
```

## Hue Reference (OKLCH)

| Hue | Color | Example |
|-----|-------|---------|
| 0 | Red | `oklch(65% 0.25 0)` |
| 30 | Orange | `oklch(70% 0.15 30)` |
| 60 | Yellow | `oklch(80% 0.15 60)` |
| 120 | Green | `oklch(65% 0.20 120)` |
| 150 | Teal | `oklch(65% 0.15 150)` |
| 180 | Cyan | `oklch(70% 0.10 180)` |
| 210 | Sky Blue | `oklch(65% 0.12 210)` |
| 240 | Blue | `oklch(55% 0.20 240)` |
| 270 | Purple | `oklch(55% 0.20 270)` |
| 300 | Magenta | `oklch(60% 0.25 300)` |
| 330 | Pink | `oklch(65% 0.20 330)` |
| 260 | Cool Gray bias | Neutral ramp hue |

## Perceptually Uniform Gray Ramp

```css
:root {
  --gray-50:  oklch(98% 0.002 260);
  --gray-100: oklch(96% 0.004 260);
  --gray-200: oklch(90% 0.006 260);
  --gray-300: oklch(80% 0.008 260);
  --gray-400: oklch(65% 0.010 260);
  --gray-500: oklch(50% 0.012 260);
  --gray-600: oklch(38% 0.014 260);
  --gray-700: oklch(28% 0.014 260);
  --gray-800: oklch(20% 0.012 260);
  --gray-900: oklch(14% 0.010 260);
  --gray-950: oklch(08% 0.008 260);
}
```

## Generating Accent Palettes

Pick one hue, then vary lightness:

```css
/* Green accent (hue 150) */
--accent-50:  oklch(95% 0.15 150);
--accent-100: oklch(90% 0.16 150);
--accent-200: oklch(82% 0.17 150);
--accent-300: oklch(74% 0.18 150);
--accent-400: oklch(68% 0.19 150);
--accent-500: oklch(62% 0.20 150);
--accent-600: oklch(52% 0.22 150);
--accent-700: oklch(42% 0.20 150);
--accent-800: oklch(32% 0.16 150);
--accent-900: oklch(22% 0.12 150);
```

## Contrast Calculation

WCAG 2.2 requires luminance ratio. With OKLCH, the L value is a good proxy for relative luminance. Quick rule of thumb:

- L contrast ratio = L_light / L_dark (approximate)
- For exact WCAG compliance, use the relative luminance formula or a contrast checker
- Target: L difference ≥ 0.40 for body text (roughly 4.5:1)
- Target: L difference ≥ 0.25 for large text (roughly 3:1)

## Fallback for Old Browsers

```css
:root {
  --accent-500: #22c55e; /* sRGB fallback */
  --accent-500: oklch(62% 0.20 145); /* modern override */
}
```

Browsers that don't support `oklch()` ignore the second declaration, keeping the sRGB fallback.

## Conversion Tools

- **oklch.com** — interactive picker and converter
- **Chrome DevTools** (2024+) — native OKLCH support in color picker
- **CSS Color Module Level 4** — `oklch()` is in the spec and has 95%+ browser support
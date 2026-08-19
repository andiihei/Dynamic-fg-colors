# OKLCH Lightness Control — A Technical Demo

An interactive playground for exploring how CSS can (and can't) automatically adjust text color for perceptual contrast against dynamic backgrounds using the OKLCH color space.

## 🎯 Purpose

This demo was born from a simple question:

Can we use modern CSS to build a text color that automatically adapts to any background — the way prefers-color-scheme does for light/dark mode, but for arbitrary colors?

More specifically, it explores whether the new native **CSS @function** rule, combined with **OKLCH relative color syntax**, can replace JavaScript-based contrast calculations for design systems.

## 🧪 The Goal

Design systems often need to pick a foreground color that _"just works"_ against a user-supplied background. Traditionally this requires:

1. Reading the background color
2. Computing a contrast ratio (WCAG 2.x or APCA)
3. Choosing between a light or dark foreground

This demo attempts to do it entirely in CSS using a custom `@function` — with a JavaScript fallback for browsers that don't yet support the spec.

It's **_not_** meant to be production-ready. It's meant to be a **_conversation starter_** about where CSS is headed, where it still falls short, and what the design community needs from the platform.

## 🧠 How It Works

### The Core Logic

The foreground color is derived from the background using three rules:

**Auto-polarity** — If the background has high chroma (C > 0.15) AND high lightness (L ≥ 0.75), text shifts darker. Otherwise, it shifts lighter. This handles the tricky case where a bright, saturated color (like a vivid yellow) needs dark text even though it's technically "light."

**Chroma-weighted shift** — More saturated backgrounds need a larger lightness delta to achieve equivalent perceived contrast. The formula:
   
   effectiveShift = baseShift × (1 + K × chroma)
      where `K = 1.0`.

**Hue locking** — Text hue always matches the background hue, producing a monochromatic scheme.

The Contrast Levels

| Level | Base ΔL | Text Chroma | Use Case |
|-------|---------|-------------|----------|
| **High** | ±0.55 | +0.162 (auto) | Maximum readability |
| **Medium** | ±0.35 | user-defined | Balanced |
| **Low** | ±0.15 | user-defined | Subtle, decorative |

Dual Contrast Metrics

The demo evaluates the result using both:

**APCA** (Accessible Perceptual Contrast Algorithm) — the perceptually-uniform successor proposed for WCAG 3.0, with font-size-aware thresholds
**WCAG 2.x** — the current standard (AA/AAA levels)

Toggle between them to see where the two methodologies agree — and where they diverge.

## ✨ Features

- Native CSS @function with automatic JS fallback detection
- OKLCH color controls with live gradient tracks
- Chroma-weighted lightness shift formula
- Auto-polarity based on L + C thresholds
- Text hue locked to background hue
- Three contrast levels (High / Medium / Low)
- Side-by-side APCA and WCAG 2.x contrast analysis
- Font-size-aware APCA thresholds
- Copy-to-clipboard for the generated CSS
- Responsive layout

## 🛠️ Tech Notes

### Browser Support for @function

CSS `@function` is a **newly-specified feature** (as of 2025). Support is still limited:

- ✅ Chrome 142+ (behind flag in earlier versions)
- 🚧 Firefox / Safari — in development

The demo detects support at runtime and falls back to JavaScript when needed. You can see the status in the header badge.

### Why Not color-contrast()?

You might wonder why we don't use the color-contrast() function. Two reasons:

1. It returns a color, not a numeric contrast ratio — so you can't use the result in calc() or if() logic
2. It only picks from a predefined list of candidates — it can't dynamically shift a color's lightness

For true dynamic contrast calculation based on a computed ratio, JavaScript is still required.

## 🤝 Contributing

This is a learning tool, not a finished product. If you have ideas for improving it, please fork and experiment!

Some directions worth exploring:

- **Hue-aware chroma limits** — the sRGB gamut boundary varies by hue; the chroma slider could reflect this
- **Perceptual shift curves** — the linear 1 + K × C formula is a starting point; a hand-tuned curve might feel better
- **Additional contrast models** — Michelson, Weber, or custom perceptual models
- **More polarity rules** — the current L/C threshold is a heuristic; could it be improved?
- **Export as a design token set** — generate Figma tokens or CSS custom properties from the current state
- **Accessibility audit** — the demo itself should model what it preaches

### Running Locally

No build step required. Just open `index.html` in a browser.
```bash
# Or serve it locally if you prefer
npx serve .

## 📄 License

MIT — do whatever you want with it. If you build something cool, I'd love to hear about it.

Built as an exploration of CSS Color Level 5, CSS Custom Functions, and the ongoing evolution of web color.```

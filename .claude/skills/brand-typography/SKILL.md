---
description: Swap the site's fonts (sans/serif/mono) end to end — Tailwind families, layout font links, brand page, verify
---

# Brand typography

Change the three brand typefaces in one pass. The site uses exactly three
Tailwind font families — `font-sans` / `font-serif` / `font-mono` — and the
roles are fixed:

| Family | Utility      | Role                                              |
| ------ | ------------ | ------------------------------------------------- |
| sans   | `font-sans`  | Body copy + prose (global `<body>`)               |
| serif  | `font-serif` | Headings (h1–h6) and the wordmark                 |
| mono   | `font-mono`  | Eyebrows, dates, nav, footer, buttons (uppercase) |

There are **no** `zmoki-*` font tokens — fonts ride Tailwind's standard family
keys (unlike colors, which live in `src/design-tokens.mjs`). Changing a family
key updates every `font-*` usage and the `.prose` layer automatically.

> Fonts are Google Fonts (variable). Pick families at fonts.google.com and note
> each one's available axes/weights before starting.

## 1. Tailwind families — `tailwind.config.mjs`

Two edits, both near the top / in `theme.extend`:

- **`fontFamily`** (in `theme.extend`) — set the three stacks:
  ```js
  fontFamily: {
    sans: ["Noto Sans", "system-ui", "sans-serif"],
    serif: ["Noto Serif", "Georgia", "serif"],
    mono: ["Noto Sans Mono", "ui-monospace", "monospace"],
  },
  ```
- **`headingFontStack`** (const near the imports) — the family used for `h1–h6`.
  It is referenced in **two** places (the `typography()` prose overrides and the
  `addBase` plugin), so editing the const updates both:
  ```js
  const headingFontStack = "'Noto Serif', Georgia, serif";
  ```
  Keep it in sync with the `serif` family above (headings = serif).

## 2. Load the fonts — both layouts

Update the Google Fonts `<link>` in **both** files (they each load their own):

- `src/layouts/BaseLayout.astro`
- `src/layouts/BrandLayout.astro`

Use the variable `css2` URL. Include italic axes for any family used in prose
(sans + serif); mono usually needs weight only:

```
https://fonts.googleapis.com/css2?family=Noto+Sans:ital,wght@0,100..900;1,100..900&family=Noto+Sans+Mono:wght@100..900&family=Noto+Serif:ital,wght@0,100..900;1,100..900&display=swap
```

Load **only** the families you use — don't leave retired fonts in the URL.

## 3. Update the brand specimen

The brand page documents the system and must not drift:

- `src/pages/-/astro/brand/typography.astro` — the intro sentence ("Three
  faces: …"), the three font-pairing cards (label `· font-*`, the family name,
  the weight note), and the prose specimen sentence ("Prose body is set in …").
- `src/pages/-/astro/brand/index.astro` — the Typography card `summary`.
- `src/pages/-/astro/brand/components.astro` — heading specimens use
  `font-serif`; confirm they still read right.

Also update the **Fonts** row in `AGENTS.md` (Tech stack table) so the docs
match.

## 4. Verify

```bash
# from the project root (or worktree)
grep -rn "font-heading\|Space Grotesk\|Google Sans\|Space Mono" src/ tailwind.config.mjs   # expect: no retired fonts / dead keys
npm run format
npm run check          # 0 errors
npm run build          # passes
# confirm the utilities compiled to the new families:
grep -rhoE "\.font-(sans|serif|mono)\{[^}]*\}" dist/_astro/*.css | sort -u
```

Then reload `/-/astro/brand/typography/` to eyeball the specimen.

## Notes

- If you change which role a family fills (e.g. serif body instead of serif
  headings), update **both** the `font-*` utilities in templates **and**
  `headingFontStack` — they must agree.
- `font-sans` and `font-mono` are Tailwind defaults, so the dozens of existing
  `font-mono` label usages need no edits — they pick up the new face for free.
- Run `/run` to start the dev server for a visual check.

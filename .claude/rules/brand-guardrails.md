# Brand Guardrails — Deep Green / Cream

> Duncan's brand is not up for negotiation. It is the brand his members recognise. Emma protects it.

## The palette (canonical)

From `index.html`:

| Token | Hex | Use |
|---|---|---|
| `--deep-green` | `#1a4d2e` | Primary brand, CTA backgrounds, headers |
| `--light-cream` | `#f5f0e8` | Page background |
| `--white` | `#ffffff` | Card surfaces |
| `--text` | `#163021` | Body text |
| `--muted` | `#4b6455` | Secondary text, footer |
| `--line` | `#d8d0c4` | Borders, dividers |

Shadows: one only — `0 10px 24px rgba(26, 77, 46, 0.12)`.

Radius: `14px` (cards), `10px` (buttons), `999px` (status pills only).

Font: `"Segoe UI", Tahoma, Arial, sans-serif`.

## What is NOT the brand

- ❌ OLED black backgrounds
- ❌ Spectral neon accents
- ❌ Gradients (beyond the faint cream background gradient in `index.html`)
- ❌ Glassmorphism
- ❌ Dark mode (not in Phase 1; member research in Phase 2 if ever)
- ❌ Animated hero backgrounds
- ❌ Parallax scrolling
- ❌ Bouncy spring animations (numbers don't bounce)

## Motion rules

- Transitions: opacity + 4–8 px translate, 150–250 ms
- Easing: `cubic-bezier(0.4, 0, 0.2, 1)`
- No shimmer, no skeleton loaders with animated gradients — use a simple card with *"Loading…"*
- No attention-seeking CTA pulses

## Typography

- Headings: Segoe UI, weight 600, tight line-height (1.15 for h1)
- Body: 16 px, line-height 1.5, max line length ~70 chars
- Numbers in a currency context: `font-variant-numeric: tabular-nums`

## Components that must stay on-brand

- Hero (deep-green background, white text, cream card shadow below)
- Cards (white surface, `--line` border, `--shadow`)
- Buttons (deep-green background, white text) — secondary buttons use cream surface + deep-green border
- Forms (white surface, `--line` borders, focus ring in deep-green at 40% alpha)
- Fitzy chat bubble (deep-green for Fitzy, cream for member, or reverse — Emma picks once, then sticks)

## Hard refusals

- "Let's modernise with dark mode" → no. Not this phase.
- "Rounded-2xl everywhere" → no. 14 / 10 / 999 only.
- "Gradient CTAs" → no.
- "Emoji in headings" → no.
- "Tailwind's default blues" → no. Only tokens.

## Enforcement

- No hex literal in any component — must be a CSS var or Tailwind token mapped to the palette.
- Lint rule (optional Phase 2): catch hex literals in `.tsx`/`.css`.
- Emma reviews all new components before merge.

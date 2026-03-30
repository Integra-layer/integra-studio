# Integra Design System

> Canonical source: https://github.com/Integra-layer/integra-brand
> Live guide: https://integra-brand.vercel.app
> Drop-in kit: `kit/globals.css`, `kit/brand-tokens.css`, `kit/integra-logo.tsx`

---

## Brand Colors

| Name | Hex | CSS Variable | Usage |
|------|-----|-------------|-------|
| Primary | `#FF6D49` | `--integra-brand` | Flagship coral-orange. Buttons, links, accents |
| Primary Dark | `#FC4E23` | `--integra-brand-dark` | Hover and active states |
| Primary Light | `#FF8A65` | `--integra-brand-light` | Subtle accents, highlights |
| Gold | `#FFC17A` | `--integra-brand-gold` | Warm accent, rewards, XP |
| Pink | `#F34499` | `--integra-brand-pink` | Secondary accent, gradients |
| Pink Light | `#FFAFD6` | `--integra-brand-pink-light` | Gradient start |
| Red | `#F71227` | `--integra-brand-red` | Gradient end |
| Teal | `#00A186` | `--integra-brand-teal` | Contrast accent |

## Status Colors

| Name | Hex | Usage |
|------|-----|-------|
| Success | `#1FC16B` | Positive, online, confirmed |
| Warning | `#FA7319` | Caution, pending |
| Danger | `#FA3748` | Error, destructive |
| Info | `#335CFF` | Informational, links |

## Neutral Scale

| Name | Hex |
|------|-----|
| White | `#FFFFFF` |
| Gray 50 | `#FAFAF8` |
| Gray 100 | `#F5F5F2` |
| Gray 200 | `#E5E7EB` |
| Gray 300 | `#D1D5DB` |
| Gray 400 | `#9CA3AF` |
| Gray 500 | `#6B7280` |
| Gray 600 | `#4B5563` |
| Gray 700 | `#374151` |
| Gray 800 | `#1F2937` |
| Gray 900 | `#111827` |
| Black | `#0A0A0F` |

## Dark Theme Surfaces

| Layer | Value | Usage |
|-------|-------|-------|
| Background | `#0A0A0F` | Page background |
| Card/Surface | `rgba(23, 23, 28, 0.7)` | Cards, panels |
| Elevated | `rgba(31, 31, 38, 0.6)` | Elevated surfaces |
| Muted | `rgba(31, 31, 38, 0.4)` | Secondary surfaces |
| Border | `rgba(255, 255, 255, 0.08)` | Dividers, outlines |
| Border Strong | `rgba(255, 255, 255, 0.15)` | Emphasized borders |
| Text | `#E6E6E6` | Primary text |
| Text Muted | `#A3A3A3` | Secondary text |
| Text Subtle | `#6C757D` | Tertiary text |

## Light Theme Surfaces

| Layer | Value | Usage |
|-------|-------|-------|
| Background | `#FAFAF8` | Page background |
| Card/Surface | `#FFFFFF` | Cards, panels |
| Muted | `#F5F5F2` | Secondary surfaces |
| Border | `rgba(0, 0, 0, 0.08)` | Dividers |
| Text | `#1A1A1F` | Primary text |
| Text Muted | `#6B6B76` | Secondary text |

## Forbidden Colors (NEVER use)

`#f97316`, `#ff6b6b`, `#22c55e`, `#eab308`, `#ef4444`, `#3b82f6`, `#6C5CE7`, `#A29BFE`

## Forbidden Dark Backgrounds (NEVER use)

`#000000`, `#111111`, `#1a1a2e`, `#0d1117`, `#0A0A1A`, `#12122A`

---

## Typography

### Fonts
| Type | Stack |
|------|-------|
| Sans (Primary) | `"Euclid Circular B", Verdana, system-ui, sans-serif` |
| Monospace | `"Geist Mono", ui-monospace, SFMono-Regular, Menlo, monospace` |

### Weights
400 Regular (body) | 500 Medium (labels, buttons) | 600 Semibold (section heads) | 700 Bold (titles)

### Scale
| Name | Size | Usage |
|------|------|-------|
| 7xl | 110px | Hero headlines |
| 5xl | 48px | Page titles |
| 3xl | 30px | Section headings |
| xl | 20px | Subtitles |
| base | 16px | Body text |
| sm | 14px | Captions |
| xs | 12px | Labels, metadata |

### Forbidden Fonts (NEVER substitute)
Inter, Montserrat, Space Grotesk, Kode Mono, Arial, Helvetica

---

## Gradients

| Name | CSS | Usage |
|------|-----|-------|
| Brand | `linear-gradient(135deg, #FFAFD6, #F34499, #FC4E23, #F71227)` | Hero, CTAs |
| Button | `linear-gradient(135deg, #F34499, #FF4F2E)` | Primary buttons |
| Warm | `linear-gradient(135deg, #FF6D49, #FF8A65, #FFB74D)` | Secondary |
| Text | `linear-gradient(135deg, #F34499, #FF7B6B, #FF4F2E)` | Gradient text |

```css
.gradient-brand-text {
  background: linear-gradient(135deg, #F34499, #FF7B6B, #FF4F2E);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}
```

---

## Border Radius

| sm | md | lg (default) | xl | 2xl | full |
|----|----|----|----|----|------|
| 6px | 8px | 12px | 16px | 24px | 9999px |

## Shadows

```css
--integra-shadow-xs: 0 1px 2px rgba(0, 0, 0, 0.04);
--integra-shadow-sm: 0 2px 8px rgba(0, 0, 0, 0.06), 0 1px 2px rgba(0, 0, 0, 0.04);
--integra-shadow-md: 0 4px 16px rgba(0, 0, 0, 0.08), 0 2px 4px rgba(0, 0, 0, 0.04);
--integra-shadow-lg: 0 8px 32px rgba(0, 0, 0, 0.12), 0 4px 8px rgba(0, 0, 0, 0.06);
```

## Glassmorphism

```css
.glass {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.08);
}
```

---

## Animation

- Hover: `translateY(-2px)`, 150ms ease
- Transitions: 150-200ms for interactive elements
- Status pulse: 2s ease-in-out infinite
- Font smoothing: `-webkit-font-smoothing: antialiased`
- No flashy animations. Purposeful motion only.

---

## Logo

### Files
| File | Usage |
|------|-------|
| `integra-mark.svg` | Primary icon (coral) |
| `integra-mark-white.svg` | On dark backgrounds |
| `integra-mark-dark.svg` | On light backgrounds |
| `integra-wordmark-dark.svg` | Full wordmark (dark bg) |
| `integra-wordmark-white.svg` | Full wordmark (light bg) |

### Rules
- Min size: 24px | Clear space: 1x height | Never stretch/rotate/shadow

### React Component
```tsx
import { IntegraLogo } from "@/components/integra-logo";
<IntegraLogo className="h-8" />
<IntegraLogo variant="mark" className="h-10 w-10" />
<IntegraLogo color="white" />  // dark bg
<IntegraLogo color="brand" />  // coral-orange
```

---

## Implementation

```bash
# From integra-brand kit/:
cp kit/globals.css src/app/globals.css
cp kit/integra-logo.tsx src/components/integra-logo.tsx
cp kit/postcss.config.mjs postcss.config.mjs

# Or install package:
npm install @integra/design-system
```

## Chart Colors
1. `#FF6D49` 2. `#F34499` 3. `#335CFF` 4. `#1FC16B` 5. `#FFC17A` 6. `#A855F7`

# Integra Design System

Complete design system reference for building dApps on the Integra blockchain. Dark-first, professional, and consistent.

## Color Palette

### Primary Colors

| Name | Hex | RGB | Usage |
|------|-----|-----|-------|
| Integra Blue | `#3B82F6` | 59, 130, 246 | Primary actions, links, active states |
| Integra Blue Light | `#60A5FA` | 96, 165, 250 | Hover states, highlights |
| Integra Blue Dark | `#2563EB` | 37, 99, 235 | Pressed states, emphasis |
| Integra Blue Muted | `#3B82F6/10` | — | Background tints, badges |

### Accent Colors

| Name | Hex | RGB | Usage |
|------|-----|-----|-------|
| Success Green | `#22C55E` | 34, 197, 94 | Success states, positive values, confirmed tx |
| Warning Amber | `#F59E0B` | 245, 158, 11 | Warnings, pending states, caution |
| Error Red | `#EF4444` | 239, 68, 68 | Errors, destructive actions, failed tx |
| Info Cyan | `#06B6D4` | 6, 182, 212 | Informational, tips, neutral highlights |
| Purple Accent | `#8B5CF6` | 139, 92, 246 | XP, rewards, gamification elements |

### Neutral Colors (Dark Theme)

| Name | Hex | Usage |
|------|-----|-------|
| Background | `#09090B` | Page background (zinc-950) |
| Surface | `#18181B` | Card/panel background (zinc-900) |
| Surface Elevated | `#27272A` | Elevated surface, dropdowns, modals (zinc-800) |
| Border | `#3F3F46` | Default borders (zinc-700) |
| Border Subtle | `#27272A` | Subtle dividers (zinc-800) |
| Text Primary | `#FAFAFA` | Primary text (zinc-50) |
| Text Secondary | `#A1A1AA` | Secondary/muted text (zinc-400) |
| Text Tertiary | `#71717A` | Disabled text, placeholders (zinc-500) |

### Tailwind CSS Mappings

```typescript
// tailwind.config.ts
const config = {
  theme: {
    extend: {
      colors: {
        integra: {
          DEFAULT: "#3B82F6",
          light: "#60A5FA",
          dark: "#2563EB",
          muted: "rgba(59, 130, 246, 0.1)",
        },
        surface: {
          DEFAULT: "#18181B",
          elevated: "#27272A",
        },
        success: "#22C55E",
        warning: "#F59E0B",
        error: "#EF4444",
        info: "#06B6D4",
        xp: "#8B5CF6",
      },
    },
  },
};
```

### CSS Custom Properties

```css
:root {
  --color-integra: 59 130 246;
  --color-integra-light: 96 165 250;
  --color-integra-dark: 37 99 235;

  --color-background: 9 9 11;
  --color-surface: 24 24 27;
  --color-surface-elevated: 39 39 42;
  --color-border: 63 63 70;

  --color-text-primary: 250 250 250;
  --color-text-secondary: 161 161 170;
  --color-text-tertiary: 113 113 122;

  --color-success: 34 197 94;
  --color-warning: 245 158 11;
  --color-error: 239 68 68;
  --color-info: 6 182 212;
  --color-xp: 139 92 246;

  --radius: 0.5rem;
}
```

---

## Typography

### Font Families

| Font | Usage | Weight Range |
|------|-------|-------------|
| **Inter** | All UI text — headings, body, labels, buttons | 400 (regular), 500 (medium), 600 (semibold), 700 (bold) |
| **Space Grotesk** | Code, addresses, hashes, technical data | 400 (regular), 500 (medium), 700 (bold) |

### Font Installation

```typescript
// src/app/layout.tsx
import { Inter, Space_Grotesk } from "next/font/google";

const inter = Inter({ subsets: ["latin"], variable: "--font-inter" });
const spaceGrotesk = Space_Grotesk({ subsets: ["latin"], variable: "--font-code" });

// Apply to body
<body className={`${inter.variable} ${spaceGrotesk.variable} font-sans`}>
```

```typescript
// tailwind.config.ts
fontFamily: {
  sans: ["var(--font-inter)", "system-ui", "sans-serif"],
  code: ["var(--font-code)", "monospace"],
},
```

### Type Scale

| Element | Size | Weight | Line Height | Class |
|---------|------|--------|-------------|-------|
| Page Title (h1) | 30px / 1.875rem | 700 bold | 1.2 | `text-3xl font-bold` |
| Section Title (h2) | 24px / 1.5rem | 600 semibold | 1.3 | `text-2xl font-semibold` |
| Subsection (h3) | 20px / 1.25rem | 600 semibold | 1.4 | `text-xl font-semibold` |
| Card Title (h4) | 16px / 1rem | 600 semibold | 1.5 | `text-base font-semibold` |
| Body | 14px / 0.875rem | 400 regular | 1.5 | `text-sm` |
| Small / Caption | 12px / 0.75rem | 400 regular | 1.4 | `text-xs` |
| Code / Address | 13px / 0.8125rem | 400 regular | 1.5 | `text-[13px] font-code` |
| Button | 14px / 0.875rem | 500 medium | 1 | `text-sm font-medium` |

### Address Display

Always use `font-code` for blockchain addresses, tx hashes, and token amounts:

```tsx
<span className="font-code text-[13px] text-zinc-400">
  {address.slice(0, 6)}...{address.slice(-4)}
</span>
```

---

## Component Patterns

### shadcn/ui Configuration

Use shadcn/ui as the component library. Install with the "zinc" base color and dark mode default:

```bash
npx shadcn@latest init
# Theme: zinc
# Style: default
# CSS variables: yes
# Tailwind CSS: yes
# Components directory: @/components/ui
```

### Buttons

```tsx
// Primary action
<Button className="bg-integra hover:bg-integra-light text-white">
  Mint Passport
</Button>

// Secondary action
<Button variant="outline" className="border-zinc-700 hover:bg-zinc-800">
  Cancel
</Button>

// Destructive action
<Button variant="destructive">
  Revoke Access
</Button>

// Ghost / subtle
<Button variant="ghost" className="text-zinc-400 hover:text-zinc-100">
  View Details
</Button>

// Loading state
<Button disabled className="bg-integra/50">
  <Loader2 className="mr-2 h-4 w-4 animate-spin" />
  Confirming...
</Button>
```

### Cards

```tsx
<div className="rounded-lg border border-zinc-800 bg-zinc-900 p-6">
  <h3 className="text-base font-semibold text-zinc-50">Card Title</h3>
  <p className="mt-1 text-sm text-zinc-400">Card description text.</p>
  <div className="mt-4">
    {/* Card content */}
  </div>
</div>
```

### Stat Cards

```tsx
<div className="rounded-lg border border-zinc-800 bg-zinc-900 p-4">
  <p className="text-xs font-medium uppercase tracking-wider text-zinc-500">Total Value</p>
  <p className="mt-1 text-2xl font-bold text-zinc-50">$1,234.56</p>
  <p className="mt-1 text-xs text-success">+12.5% from last week</p>
</div>
```

### Data Tables

```tsx
<div className="rounded-lg border border-zinc-800">
  <Table>
    <TableHeader>
      <TableRow className="border-zinc-800 hover:bg-transparent">
        <TableHead className="text-xs font-medium uppercase tracking-wider text-zinc-500">
          Asset
        </TableHead>
      </TableRow>
    </TableHeader>
    <TableBody>
      <TableRow className="border-zinc-800 hover:bg-zinc-800/50">
        <TableCell className="text-sm text-zinc-300">...</TableCell>
      </TableRow>
    </TableBody>
  </Table>
</div>
```

### Badges / Tags

```tsx
// Status badges
<span className="inline-flex items-center rounded-full bg-success/10 px-2 py-0.5 text-xs font-medium text-success">
  Active
</span>

<span className="inline-flex items-center rounded-full bg-warning/10 px-2 py-0.5 text-xs font-medium text-warning">
  Pending
</span>

<span className="inline-flex items-center rounded-full bg-error/10 px-2 py-0.5 text-xs font-medium text-error">
  Failed
</span>

// Category badges
<span className="inline-flex items-center rounded-full bg-integra/10 px-2 py-0.5 text-xs font-medium text-integra">
  DeFi
</span>

// XP badge
<span className="inline-flex items-center gap-1 rounded-full bg-xp/10 px-2 py-0.5 text-xs font-medium text-xp">
  <Sparkles className="h-3 w-3" />
  250 XP
</span>
```

### Inputs

```tsx
<Input
  className="border-zinc-700 bg-zinc-900 text-zinc-100 placeholder:text-zinc-500 focus:border-integra focus:ring-integra/20"
  placeholder="Enter amount..."
/>

// With label
<div className="space-y-1.5">
  <Label className="text-sm font-medium text-zinc-300">Token Amount</Label>
  <Input className="border-zinc-700 bg-zinc-900 text-zinc-100" />
  <p className="text-xs text-zinc-500">Enter the amount of tokens to wrap.</p>
</div>
```

### Modals / Dialogs

```tsx
<Dialog>
  <DialogContent className="border-zinc-800 bg-zinc-900">
    <DialogHeader>
      <DialogTitle className="text-zinc-50">Confirm Transaction</DialogTitle>
      <DialogDescription className="text-zinc-400">
        You are about to mint a new asset passport.
      </DialogDescription>
    </DialogHeader>
    <div className="py-4">
      {/* Content */}
    </div>
    <DialogFooter>
      <Button variant="outline" className="border-zinc-700">Cancel</Button>
      <Button className="bg-integra hover:bg-integra-light">Confirm</Button>
    </DialogFooter>
  </DialogContent>
</Dialog>
```

### Toast Notifications

```tsx
// Success
toast.success("Passport minted successfully!", {
  description: "Token ID: #1234",
});

// Error
toast.error("Transaction failed", {
  description: "Insufficient IRL for gas fees.",
});

// Transaction pending
toast.loading("Transaction pending...", {
  description: "Waiting for confirmation on Integra network.",
});
```

---

## Layout Patterns

### App Shell

```
+----------------------------------------------------+
|  Header (h-14, border-b, bg-zinc-900)              |
+----------+-----------------------------------------+
|          |                                          |
| Sidebar  |  Content Area                            |
| (w-64)   |  (flex-1, p-6, bg-zinc-950)             |
| bg-zinc  |                                          |
| -900     |                                          |
| border-r |                                          |
|          |                                          |
+----------+-----------------------------------------+
```

### Sidebar Navigation

```tsx
<nav className="flex h-full w-64 flex-col border-r border-zinc-800 bg-zinc-900">
  {/* Logo */}
  <div className="flex h-14 items-center border-b border-zinc-800 px-4">
    <span className="text-lg font-bold text-zinc-50">App Name</span>
  </div>

  {/* Nav items */}
  <div className="flex-1 space-y-1 p-3">
    <NavLink href="/dashboard" icon={LayoutDashboard}>Dashboard</NavLink>
    <NavLink href="/assets" icon={Wallet}>Assets</NavLink>
    <NavLink href="/marketplace" icon={Store}>Marketplace</NavLink>
  </div>

  {/* User / XP section at bottom */}
  <div className="border-t border-zinc-800 p-3">
    <div className="flex items-center gap-3">
      <Avatar />
      <div>
        <p className="text-sm font-medium text-zinc-200">0x1234...abcd</p>
        <p className="text-xs text-xp">1,250 XP</p>
      </div>
    </div>
  </div>
</nav>
```

Active nav item: `bg-zinc-800 text-zinc-50`
Inactive nav item: `text-zinc-400 hover:bg-zinc-800/50 hover:text-zinc-200`

### Header

```tsx
<header className="flex h-14 items-center justify-between border-b border-zinc-800 bg-zinc-900 px-6">
  <h1 className="text-lg font-semibold text-zinc-50">Page Title</h1>
  <div className="flex items-center gap-3">
    <Button variant="ghost" size="icon">
      <Bell className="h-4 w-4 text-zinc-400" />
    </Button>
    <ConnectButton />
  </div>
</header>
```

### Content Area

```tsx
<main className="flex-1 overflow-auto bg-zinc-950 p-6">
  {/* Page header */}
  <div className="mb-6">
    <h2 className="text-2xl font-semibold text-zinc-50">Dashboard</h2>
    <p className="mt-1 text-sm text-zinc-400">Overview of your portfolio and activity.</p>
  </div>

  {/* Content grid */}
  <div className="grid gap-4 md:grid-cols-2 lg:grid-cols-4">
    {/* Stat cards */}
  </div>

  <div className="mt-6 grid gap-6 lg:grid-cols-3">
    {/* Main content (2/3) + sidebar (1/3) */}
    <div className="lg:col-span-2">{/* Table or main content */}</div>
    <div>{/* Activity feed or info panel */}</div>
  </div>
</main>
```

---

## Animation Guidelines

Keep animations **subtle and purposeful**. No flashy effects, no bouncing, no gratuitous motion.

### Allowed Animations

```css
/* Page transitions — fade in */
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(4px); }
  to { opacity: 1; transform: translateY(0); }
}
.animate-fade-in {
  animation: fadeIn 0.2s ease-out;
}

/* Skeleton loading pulse */
.animate-pulse {
  animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
}

/* Spinner */
.animate-spin {
  animation: spin 1s linear infinite;
}

/* Subtle hover scale for interactive cards */
.hover-lift {
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}
.hover-lift:hover {
  transform: translateY(-1px);
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
}
```

### Transition Defaults

```tsx
// All interactive elements should use these transitions
className="transition-colors duration-150"   // Color changes (hover, focus)
className="transition-opacity duration-200"   // Opacity changes (appear/disappear)
className="transition-all duration-200"       // Combined transitions
```

### Do NOT Use

- Bouncing animations
- Rotating logos or icons (except loading spinners)
- Parallax scrolling effects
- Auto-playing animations that loop indefinitely
- Animations longer than 300ms
- Motion that blocks interaction

---

## Logo Usage

- Use the Integra logo in the sidebar and login page
- Minimum clear space: 8px on all sides
- On dark backgrounds: use the light/white version
- Never stretch, rotate, or recolor the logo
- For powered-by attribution: "Built on Integra" with small logo, bottom of page

---

## Responsive Breakpoints

Follow Tailwind defaults:
- `sm`: 640px (mobile landscape)
- `md`: 768px (tablet)
- `lg`: 1024px (desktop)
- `xl`: 1280px (wide desktop)

Mobile-first approach:
- Sidebar collapses to hamburger menu below `lg`
- Stat cards stack from 4-column to 2-column at `md`, 1-column at `sm`
- Tables become card lists on mobile
- Modals become full-screen sheets on mobile

---

## Neon Noir Brand Identity Research

Deep brand research backing the Integra design system decisions.

### Research Location

`/Users/adamboudj/knowledge-bank/08-research/brand-design-system/` (10 files, 357KB)

Key documents:
- `BRAND-DESIGN-SYSTEM.md` (53KB) — full system spec
- `COLOR-PSYCHOLOGY.md` (31KB) — color perception and emotional mapping
- `TYPOGRAPHY-PSYCHOLOGY.md` (27KB) — font psychology and trust signals

### Color Psychology Mapping

| Color | Psychological Effect | Integra Usage |
|-------|---------------------|---------------|
| Purple (`#6C5CE7` / `#8B5CF6`) | Authority, innovation, premium | Primary brand, XP/rewards |
| Gold (`#F0B90B`) | Premium, achievement, trust | Highlights, important metrics |
| Teal (`#00CEC9` / `#06B6D4`) | Trust, technology, calm | Success states, info highlights |

### 70/20/10 Color Distribution Rule

- **70%** — Dark background (`#09090B` / `#0A0A1A`)
- **20%** — Brand purple for UI elements, accents, interactive states
- **10%** — Gold and teal for highlights, success indicators, emphasis

### Animation Principles

- Subtle transitions only: 200-300ms duration
- No flashy effects, no bouncing, no parallax
- Purposeful motion: every animation must communicate state change or guide attention
- Matches the design system's existing animation guidelines above

### Typography Psychology

- **Inter** — Projects authority and trust; clean geometric forms read as professional and reliable
- **Space Grotesk** — Technical credibility; monospaced feel signals engineering precision without sacrificing readability

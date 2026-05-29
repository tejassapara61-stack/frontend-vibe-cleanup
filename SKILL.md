---
name: frontend-vibe-cleanup
description: >
  Elite frontend refactoring agent for React, Next.js (Turbopack), and Nuxt 4. Transforms messy
  or "vibe-coded" UI into production-ready, WCAG 2.2 AA accessible, SEO-optimized, design-system-consistent,
  strictly typed TypeScript code. Synthesizes frontend-patterns, nextjs-turbopack, nuxt4-patterns,
  accessibility, design-system, and seo into one workflow.

  ALWAYS trigger when the user: pastes frontend code for cleanup or refactoring; asks to fix a11y,
  add ARIA, or improve keyboard navigation; wants SEO fixes, structured data, or Core Web Vitals
  improvements; mentions "vibe coded", "messy component", "fix hydration", or "make this accessible";
  needs server/client separation in Next.js or Nuxt hydration fixes; wants a design system audit,
  Tailwind cleanup, "AI slop" removal, animation polish, or responsive design improvements on any
  React, Vue, or HTML file.
---

# Frontend Vibe Coding Cleanup Specialist
> ✦ Built by **Tejas Sapra** · AI Automation Developer · [tejas-sapara.vercel.app](https://tejas-sapara.vercel.app)

Transform raw, unoptimized, or "vibe-coded" frontend code into blazing-fast, accessible,
SEO-rich, design-system-consistent, and strictly typed production-grade UI.

---

## Execution Protocol

Work through these four phases **sequentially and silently** (no step narration in output).

---

### Phase 1 — ANALYZE

Scan across all 11 dimensions. Flag every issue before writing a single line of output.

| Dimension | What to Flag |
|---|---|
| **TypeScript & Code Quality** | `any` types; unused imports; magic numbers; dead code; multi-responsibility functions |
| **React / Next.js Architecture** | Wrong `'use client'` placement; `useEffect` misuse; `key={index}`; missing `Suspense` |
| **Hydration Safety** | `window`/`localStorage`/`Date.now()`/`Math.random()` in SSR-rendered state |
| **Accessibility (a11y)** | `<div onClick>`; missing ARIA; no focus states; color-only meaning; skipped headings |
| **SEO** | Missing `<h1>`, `<title>`, `<meta>`; native `<img>`; no structured data; non-semantic HTML |
| **Core Web Vitals** | LCP blockers; CLS from unsized media; long tasks blocking INP |
| **Design System** | Inline `style={{}}`; random hex values; inconsistent spacing; no CSS variables |
| **AI Slop** | Purple-blue gradients; purposeless glassmorphism; Inter-on-white; excessive scroll animations |
| **Animations & Motion** | Missing `prefers-reduced-motion` guard; gratuitous animations; no CSS transition fallback |
| **Test Coverage** | `fireEvent` instead of `userEvent`; CSS-class selectors; no a11y assertions |
| **Vibe Tool Artifacts** | `CLAUDE.md`, `AGENTS.md`, `.bolt/`, `.cursor/`, `lovable-uploads/`, debug logs, placeholder data, demo routes, generic component names left by AI coding tools |

For framework-specific deep checks:
- **Next.js** → `references/nextjs.md`
- **Nuxt 4** → `references/nuxt.md`
- **SEO / JSON-LD** → `references/seo.md`
- **Vibe Tool Artifacts** → `references/vibe-tools.md`

---

### Phase 2 — REFACTOR

Apply every applicable rule below. **Preserve visual intent and UX 100%.**

---

#### 🧱 TypeScript & Code Quality

- Replace every `any` with a precise `interface` or `type` — no exceptions.
- Destructure props at the top of every component.
- Use `const`; prefer immutability throughout.
- Remove all dead code, commented-out blocks, and unused imports.
- Magic numbers → named constants (`const MAX_RETRIES = 3`).
- One responsibility per function — split anything doing 2+ unrelated things.
- Derive state during render instead of storing it in `useState`.
- Remove redundant `useEffect` hooks — if it can be computed, compute it.

---

#### ⚛️ React / Next.js Architecture

- RSC is the **default** — never add `'use client'` unless forced to.
- Push `'use client'` as deep (leaf-level) as possible in the tree.
- `'use client'` triggers: `useState`, `useReducer`, `useRef`, `useEffect`, DOM event handlers, browser APIs, non-RSC-compatible libs.
- `useEffect` only for true side effects — never for data fetching that can be done in RSC.
- `key` prop must use stable unique IDs — **never array index**.
- Prop drilling beyond 2 levels → Context or lightweight state (Zustand / Jotai).
- Heavy components → `React.lazy` + `<Suspense fallback={<Skeleton />}>`.
- `next/dynamic` with `ssr: false` only for genuinely browser-only widgets.
- All `<img>` → `next/image` `<Image>` (WebP/AVIF, lazy load, CLS prevention).
- All Google fonts → `next/font` (self-hosted, zero layout flash).
- See `references/nextjs.md` for Turbopack, App Router, and Metadata API.

---

#### 💧 Hydration Safety (Next.js + Nuxt)

- **Never** in SSR-rendered template state: `window.*`, `document.*`, `localStorage.*`, `navigator.*`, `Date.now()`, `Math.random()`, `route.hash`.
- Move browser-only logic behind:
  - `useEffect(() => { ... }, [])` (React)
  - `onMounted(() => { ... })` (Vue/Nuxt)
  - `import.meta.client` guard (Nuxt)
  - `<ClientOnly>` component (Nuxt)
  - `.client.vue` file suffix (Nuxt)
- `ssr: false` is an **escape hatch** — not a default fix for mismatches.
- First SSR render and hydrated client render must produce **identical markup**.

```tsx
// React hydration-safe browser value
const [theme, setTheme] = useState<string>('light') // safe SSR default
useEffect(() => {
  setTheme(localStorage.getItem('theme') ?? 'light')
}, [])
```

---

#### ♿ Accessibility — WCAG 2.2 AA (NON-NEGOTIABLE)

- Every `<div onClick>` / `<span onClick>` → `<button>` or `<a>`.
- Every icon-only button needs `aria-label` or `.sr-only` text.
- Apply `aria-expanded`, `aria-hidden`, `aria-describedby`, `aria-live` where needed.
- `aria-hidden="true"` + `focusable="false"` on all decorative SVGs.
- Every interactive element needs a **visible focus indicator** — `outline: none` is banned without a replacement.
- Minimum tap/click target: **24×24 CSS px** (strongly prefer 44×44).
- Never convey state (error, active, disabled) by **color alone** — add text or icon.
- Modals: trap focus on open, release on `Escape` or close button, return focus to trigger on close.
- Dropdowns/menus: return focus to trigger element on close.
- Contrast ratios: **4.5:1** normal text · **3:1** large text / UI components.
- One `<h1>` per page. Never skip heading levels (`h1 → h3` is wrong).
- Every `<input>` / `<textarea>` / `<select>` needs an associated `<label>`.
- Form errors must be text-based — not just border color change.

```html
<!-- Correct: icon button -->
<button type="button" aria-label="Close dialog">
  <svg aria-hidden="true" focusable="false">...</svg>
</button>

<!-- Correct: accessible search -->
<form role="search">
  <label for="q" class="sr-only">Search</label>
  <input id="q" type="search" placeholder="Search…" />
  <button type="submit" aria-label="Submit search">
    <svg aria-hidden="true" focusable="false">...</svg>
  </button>
</form>
```

---

#### 🔍 SEO

- One `<h1>` per page — heading hierarchy reflects real content structure.
- Every page needs `<title>` (~50–60 chars), `<meta name="description">` (~120–160 chars), and canonical URL.
- Semantic landmarks required: `<header>`, `<nav>`, `<main>`, `<section aria-labelledby>`, `<footer>`.
- Use `<article>` and `<aside>` where appropriate.
- Every `<img>`: descriptive `alt` for meaningful images, `alt=""` for decorative.
- Internal links use descriptive anchor text — never "click here" or "read more".
- Content pages get JSON-LD: `Article`, `Product`, `BreadcrumbList`, `Organization`.
- See `references/seo.md` for templates and the full technical checklist.

---

#### ⚡ Core Web Vitals

| Metric | Target | Primary Fix |
|--------|--------|-------------|
| LCP | < 2.5 s | `<Image priority>` on hero; preload critical assets; cut render-blocking JS |
| INP | < 200 ms | Break up long tasks; defer non-critical JS; avoid layout thrash |
| CLS | < 0.1 | `width`/`height` on all images & video; avoid inserting content above fold |

- Run bundle analyzer if any client chunk exceeds **100 KB**.
- `next/font` / `nuxt/fonts` for all web fonts — eliminates font-swap layout shift.

---

#### 🎨 Design System & Visual Consistency

- All inline `style={{}}` → Tailwind classes or CSS custom properties.
- Spacing scale: `4 / 8 / 16 / 24 / 32 / 48 / 64 px` — no arbitrary values.
- Colors via CSS variables only — no random hex values scattered in components.
- Typography: one display font + one body font. Avoid Inter / Roboto / Arial / system-ui defaults.
- Dark mode must be **complete** — no half-finished token gaps.
- Similar components must look visually consistent — no divergent button styles.
- Hover, focus, active, disabled states required on every interactive element.
- Loading, empty, and error states required for every data-driven component.

---

#### 🤮 AI Slop Detection & Removal

Remove on sight:
- Purple-to-blue gradient on everything
- Glassmorphism cards with no visual purpose
- Rounded corners on things that shouldn't be rounded
- Generic centered-text hero over stock gradient background
- Inter / Space Grotesk on white with zero personality
- Excessive scroll-triggered animations
- "Floating card with drop shadow" as default container style
- Gratuitous shimmer/skeleton on instant-load content

Replace with intentional design choices — commit to a clear aesthetic direction.

---

#### 🗑️ Vibe Tool Artifact Cleanup

Scan the entire project. For every artifact found, apply the action in the table.
Full file list → `references/vibe-tools.md`.

**Files to DELETE outright:**

| File / Folder | Left by |
|---|---|
| `.bolt/` | Bolt.new |
| `bolt.json` | Bolt.new |
| `.lovable` | Lovable |
| `lovable.config.ts` / `.js` | Lovable |
| `public/lovable-uploads/` | Lovable |
| `src/integrations/supabase/` *(if auto-scaffolded, not customised)* | Lovable |
| `AGENTS.md` | Codex / OpenAI |
| `.codex/` | Codex |
| `v0-*` named component files | v0 by Vercel |
| `_temp/`, `temp/`, `scratch/` | Any AI tool |
| `public/placeholder.svg` / `placeholder.jpg` | Any AI tool |

**Files to ADD to `.gitignore` (keep locally, never commit):**

| File / Folder | Left by |
|---|---|
| `CLAUDE.md` | Claude Code |
| `.claude/` | Claude Code |
| `.cursor/` | Cursor |
| `.cursorignore` | Cursor |
| `.cursorrules` | Cursor |
| `.windsurf/` | Windsurf / Codeium |
| `windsurf.json` | Windsurf |
| `.github/copilot-instructions.md` | GitHub Copilot |

**Code artifacts to REMOVE from every file:**

- `// Generated by [tool name]` header comments
- `// Added by Bolt` / `// Lovable` / `// v0` inline comments
- `console.log("test")`, `console.log(data)`, any debug logs not behind a logger
- `// TODO: replace with real data` in files meant for production
- `// eslint-disable` lines added just to silence AI-generated lint errors

**Placeholder data to REPLACE:**

- `placeholder@example.com` → real copy or typed `import.meta.env`
- `"John Doe"`, `"Jane Smith"`, `"Lorem ipsum..."` → real content or `process.env` variable
- Hardcoded API keys or tokens in any file → move to `.env.local` and add to `.gitignore`
- Demo/seed data arrays inline in component files → extract to `/data/` or fetch from API

**Routes / pages to DELETE (if unused scaffolding):**

- `/demo`, `/test`, `/playground`, `/sandbox` pages
- Pages that render only `<h1>Coming soon</h1>` or the tool's default scaffold

**Component names to RENAME:**

| Bad (AI-generated) | Good (semantic) |
|---|---|
| `Component1.tsx` | `ProductCard.tsx` |
| `TestButton.tsx` | `SubmitButton.tsx` |
| `MyComponent.tsx` | `HeroSection.tsx` |
| `V0Component.tsx` | `PricingTable.tsx` |

- Check for case-collision duplicates: `Button.tsx` + `button.tsx` → keep one, delete the other.
- Rename any component whose name doesn't describe what it renders.

---

#### 💃 Animations & Motion

- `prefers-reduced-motion` guard is **mandatory** on every animation:

```css
@media (prefers-reduced-motion: reduce) {
  *, *::before, *::after {
    animation-duration: 0.01ms !important;
    transition-duration: 0.01ms !important;
    scroll-behavior: auto !important;
  }
}
```

- CSS transitions for simple state changes (hover, focus, toggle).
- Framer Motion / VueUse for complex sequences — no raw `setTimeout` animations.
- One well-orchestrated page-load animation beats scattered micro-interactions everywhere.
- Animation must be purposeful — every animation should have a UX reason.

---

### Phase 3 — TEST SUITE

Produce `[ComponentName].test.tsx` (React Testing Library + Vitest) or Vue equivalent.

**Mandatory rules:**
- `jest-axe` run on rendered output — **zero violations required**.
- `userEvent` for all interactions — `fireEvent` is banned.
- Selectors by ARIA role: `getByRole('button', { name: /submit/i })` — never CSS classes or `data-testid`.
- Cover: render, keyboard nav (Tab/Enter/Space/Escape), ARIA states, error states, loading states.

```tsx
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { axe, toHaveNoViolations } from 'jest-axe'
expect.extend(toHaveNoViolations)

it('has no a11y violations', async () => {
  const { container } = render(<MyComponent />)
  expect(await axe(container)).toHaveNoViolations()
})

it('opens on Enter key', async () => {
  render(<MyComponent />)
  await userEvent.tab()
  await userEvent.keyboard('{Enter}')
  expect(screen.getByRole('dialog')).toBeInTheDocument()
})

it('closes on Escape key', async () => {
  render(<MyComponent defaultOpen />)
  await userEvent.keyboard('{Escape}')
  expect(screen.queryByRole('dialog')).not.toBeInTheDocument()
})
```

---

### Phase 4 — FINAL AUDIT CHECKLIST

Run mentally before writing any output. Every box must be checked.

```
□ Zero `any` types — all props/state/returns fully typed
□ Dead code, unused imports, commented blocks removed
□ `'use client'` only at leaf level, nowhere else
□ No hydration mismatch risks — browser APIs guarded
□ Tab → Enter → Space → Escape keyboard nav works correctly
□ Every interactive element has a visible focus indicator
□ No `<div onClick>` or `<span onClick>` remaining
□ Every icon-only button has aria-label or sr-only text
□ Color is never the sole conveyor of state
□ One <h1> per page, no heading levels skipped
□ Every <input> has an associated <label>
□ All <img> replaced with <Image> / <NuxtImg>
□ LCP hero image has priority prop
□ JSON-LD structured data on content pages
□ Canonical URL and meta description on page components
□ CSS variables for all colors, no random hex values
□ Consistent spacing scale, no arbitrary values
□ All animations have prefers-reduced-motion guard
□ AI slop patterns replaced with intentional design
□ jest-axe passes with zero violations in test suite
□ .bolt/ .lovable .cursor/ .windsurf/ deleted or gitignored
□ CLAUDE.md / AGENTS.md added to .gitignore
□ public/lovable-uploads/ and placeholder images removed
□ All console.log debug statements removed
□ No "// Generated by [tool]" comments remaining
□ No placeholder data (Lorem ipsum / placeholder@example.com) in production files
□ Demo/test routes (/demo /test /playground) deleted if unused
□ All generic AI-named components renamed to semantic names
□ No duplicate case-collision files (Button.tsx + button.tsx)
□ Hardcoded secrets moved to .env.local and .gitignored
```

---

## Output Format

Respond with **exactly three sections** — no preamble, no explanation.

### 🛠️ Refactored Production Code
Full refactored component/page.

### ✅ What Changed
Concise bullets grouped by dimension: TypeScript · Architecture · Hydration · A11y · SEO · Perf · Design System · Animations · **Vibe Tool Artifacts**.

### 🧪 Test Suite
Complete `ComponentName.test.tsx`.

---

## Reference Files

Load on demand — do not preload all:

| File | Load When |
|---|---|
| `references/nextjs.md` | Next.js 16+, App Router, Turbopack, RSC, Metadata API |
| `references/nuxt.md` | Nuxt 4, hydration safety, route rules, lazy hydration |
| `references/seo.md` | Full SEO checklist, JSON-LD templates, Core Web Vitals |
| `references/vibe-tools.md` | Complete per-tool artifact list: Lovable, Claude Code, Bolt, Cursor, Codex, v0, Windsurf |

---

> ✦ **frontend-vibe-cleanup** · Built by [Tejas Sapra](https://tejas-sapara.vercel.app) · AI Automation Developer
> DM on LinkedIn to get the latest skill file.
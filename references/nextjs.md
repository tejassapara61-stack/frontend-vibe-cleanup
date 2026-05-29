# Next.js 16+ Reference — App Router, Turbopack & RSC Patterns

## Turbopack (Default Dev Bundler)

Next.js 16+ uses Turbopack by default for `next dev`:
- Incremental Rust bundler — file-system caching under `.next/`
- Significantly faster cold start and HMR on large codebases (5–14× measured)
- No extra config needed for basic use
- Fall back to webpack only if you hit a Turbopack-specific plugin bug: `next dev --webpack`

### Bundle Analysis

Use the official Next.js Bundle Analyzer (Next.js 16.1+) to find heavy dependencies and tree-shake opportunities. Check the Next.js docs for your specific version flag.

---

## App Router — Server vs. Client Boundary

### Default: Server Components (RSC)

Every file under `app/` is a **React Server Component** unless marked otherwise.
- Runs only on the server. Zero JS shipped to the client.
- Can be `async` — `await` data directly without `useEffect`.
- Cannot use hooks, event handlers, or browser APIs.

### When to Add `'use client'`

Add it only at the **leaf component** level — never at a layout or page unless unavoidable.

Triggers that require `'use client'`:
- `useState`, `useReducer`, `useRef`
- `useEffect`, `useCallback`, `useMemo` (when deps change client-side)
- DOM event handlers (`onClick`, `onChange`, etc.)
- Browser APIs (`window`, `document`, `localStorage`, `navigator`)
- Third-party libs that aren't RSC-compatible

### Pattern: Thin Client Wrapper

```tsx
// page.tsx — stays a Server Component
import { HeavyDataTable } from './HeavyDataTable'
import { ClientSearchBar } from './ClientSearchBar'  // 'use client'

export default async function Page() {
  const data = await fetchData()          // server-only fetch
  return (
    <main>
      <ClientSearchBar />                  // tiny client island
      <HeavyDataTable rows={data} />       // server component, zero client JS
    </main>
  )
}
```

### Hydration Guard for Browser APIs

```tsx
'use client'
import { useEffect, useState } from 'react'

export function ClientOnly({ children }: { children: React.ReactNode }) {
  const [mounted, setMounted] = useState(false)
  useEffect(() => setMounted(true), [])
  if (!mounted) return null
  return <>{children}</>
}
```

---

## Data Fetching Patterns

### Server Component (preferred for page data)

```tsx
// app/products/[slug]/page.tsx
export default async function ProductPage({ params }: { params: { slug: string } }) {
  const product = await fetch(`/api/products/${params.slug}`, {
    next: { revalidate: 3600 },   // ISR-style revalidation
  }).then(r => r.json())

  return <ProductDetail product={product} />
}
```

### Client Component (user-triggered fetches)

```tsx
'use client'
import { useState } from 'react'

export function AddToCart({ productId }: { productId: string }) {
  const [loading, setLoading] = useState(false)

  async function handleAdd() {
    setLoading(true)
    await fetch('/api/cart', { method: 'POST', body: JSON.stringify({ productId }) })
    setLoading(false)
  }

  return (
    <button onClick={handleAdd} disabled={loading} aria-busy={loading}>
      {loading ? 'Adding…' : 'Add to cart'}
    </button>
  )
}
```

---

## Metadata API

```tsx
// app/blog/[slug]/page.tsx
import type { Metadata } from 'next'

export async function generateMetadata({ params }): Promise<Metadata> {
  const post = await getPost(params.slug)
  return {
    title: `${post.title} | My Blog`,
    description: post.excerpt,
    openGraph: { title: post.title, description: post.excerpt, type: 'article' },
    alternates: { canonical: `https://mysite.com/blog/${params.slug}` },
  }
}
```

---

## Performance Checklist

- [ ] `<Image>` used for all `<img>` tags (automatic WebP/AVIF, lazy load, CLS prevention)
- [ ] Heavy client components wrapped in `React.lazy` + `<Suspense fallback={...}>`
- [ ] `'use client'` boundary as low in the tree as possible
- [ ] Server Components do data fetching — no waterfall client fetches on mount
- [ ] `next/font` used for Google fonts (zero layout shift, self-hosted)
- [ ] Bundle Analyzer run if any client chunk exceeds ~100 KB
# SEO Reference — Technical Checklist, Core Web Vitals & JSON-LD Templates

## Principles

1. Fix technical blockers before content optimization.
2. One page → one clear primary search intent.
3. Mobile-first: Google indexes the mobile version.
4. Recommendations must be page-specific and implementable.
5. Long-term quality signals over manipulative patterns.

---

## Technical SEO Checklist

### Crawlability
- `robots.txt` allows important pages, blocks low-value surfaces (search results, filters)
- No important page accidentally `noindex`
- Important pages reachable within ≤ 3 clicks from the homepage
- No redirect chains longer than 2 hops
- Canonical tags are self-consistent and non-looping

### Indexability
- Consistent preferred URL format (trailing slash or not — pick one)
- Multilingual pages use correct `hreflang` if applicable
- Sitemaps reflect the intended public surface
- No duplicate URLs competing without canonical control

### Core Web Vitals Targets
| Metric | Target | Common Fix |
|--------|--------|------------|
| LCP | < 2.5 s | Preload hero image; use `<Image priority>` |
| INP | < 200 ms | Break up long tasks; defer non-critical JS |
| CLS | < 0.1 | Reserve layout space for images/ads; avoid inserting content above fold |

---

## On-Page Rules

### Title Tags
- Length: ~50–60 characters
- Primary keyword near the front
- Human-readable first — not stuffed

```
Primary Topic — Specific Modifier | Brand Name
```

### Meta Descriptions
- Length: ~120–160 characters
- Honest description of the page
- Include the main topic naturally

```
Action + topic + value proposition + one supporting detail.
```

### Heading Structure
- One `<h1>` per page
- `<h2>` / `<h3>` reflect real content hierarchy
- Never skip levels for visual styling (`<h1>` → `<h3>` is wrong)

---

## Structured Data (JSON-LD Templates)

### Article / Blog Post

```json
{
  "@context": "https://schema.org",
  "@type": "Article",
  "headline": "Page Title Here",
  "datePublished": "2025-01-15",
  "dateModified": "2025-06-01",
  "author": { "@type": "Person", "name": "Author Name" },
  "publisher": {
    "@type": "Organization",
    "name": "Brand Name",
    "logo": { "@type": "ImageObject", "url": "https://example.com/logo.png" }
  },
  "image": "https://example.com/article-hero.jpg",
  "description": "Brief description of the article content."
}
```

### Product Page

```json
{
  "@context": "https://schema.org",
  "@type": "Product",
  "name": "Product Name",
  "description": "What this product does.",
  "image": "https://example.com/product.jpg",
  "offers": {
    "@type": "Offer",
    "price": "29.99",
    "priceCurrency": "USD",
    "availability": "https://schema.org/InStock",
    "url": "https://example.com/products/slug"
  }
}
```

### Breadcrumbs

```json
{
  "@context": "https://schema.org",
  "@type": "BreadcrumbList",
  "itemListElement": [
    { "@type": "ListItem", "position": 1, "name": "Home", "item": "https://example.com" },
    { "@type": "ListItem", "position": 2, "name": "Blog", "item": "https://example.com/blog" },
    { "@type": "ListItem", "position": 3, "name": "Article Title" }
  ]
}
```

### Organization (Homepage)

```json
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Brand Name",
  "url": "https://example.com",
  "logo": "https://example.com/logo.png",
  "sameAs": [
    "https://twitter.com/brandhandle",
    "https://linkedin.com/company/brandname"
  ]
}
```

### Inline JSON-LD in Next.js / Nuxt

```tsx
// Next.js: in a Server Component or layout
export default function ArticlePage({ post }) {
  const jsonLd = {
    '@context': 'https://schema.org',
    '@type': 'Article',
    headline: post.title,
    // …
  }
  return (
    <>
      <script
        type="application/ld+json"
        dangerouslySetInnerHTML={{ __html: JSON.stringify(jsonLd) }}
      />
      {/* page content */}
    </>
  )
}
```

```vue
<!-- Nuxt: useHead composable -->
<script setup lang="ts">
useHead({
  script: [{
    type: 'application/ld+json',
    children: JSON.stringify({ '@context': 'https://schema.org', '@type': 'Article', /* … */ })
  }]
})
</script>
```

---

## Anti-Patterns

| Anti-Pattern | Fix |
|---|---|
| Keyword stuffing in title/h1 | Write for users; keyword fits naturally or not at all |
| Thin near-duplicate pages | Consolidate with canonical or differentiate with unique content |
| Schema for content not actually present | Match schema to reality exactly |
| Generic "improve SEO" outputs | Tie every recommendation to a specific page or component |
| `noindex` on pages with backlinks | Audit `robots` meta and `X-Robots-Tag` headers carefully |
| Missing alt text | Every `<img>` needs descriptive `alt`; decorative images get `alt=""` |
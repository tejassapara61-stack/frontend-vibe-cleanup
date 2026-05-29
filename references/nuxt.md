# Nuxt 4 Reference — Hydration Safety, Route Rules & Lazy Hydration

## Hydration Safety Rules

The first render must be deterministic — server HTML and client hydration must match exactly.

### Never in SSR-rendered template state:
- `Date.now()` / `new Date()`
- `Math.random()`
- `window.*`, `document.*`, `localStorage.*`, `navigator.*`
- `route.fullPath` or `route.hash` (URL fragments are client-only)

### Safe patterns for browser-only logic:

```vue
<script setup lang="ts">
import { onMounted, ref } from 'vue'

// Option 1: onMounted guard
const theme = ref<string>('light')  // safe SSR default
onMounted(() => { theme.value = localStorage.getItem('theme') ?? 'light' })

// Option 2: import.meta.client guard
const randomId = import.meta.client ? crypto.randomUUID() : 'ssr-placeholder'
</script>
```

```vue
<!-- Option 3: ClientOnly component for entire islands -->
<ClientOnly>
  <ThemeToggle />
  <template #fallback><div class="h-8 w-8" /></template>  <!-- SSR skeleton -->
</ClientOnly>
```

```
// Option 4: .client.vue suffix — never rendered during SSR
// MyWidget.client.vue
```

Use `ssr: false` in route rules as an escape hatch for genuinely browser-only routes — not as a default fix for hydration mismatches.

---

## Data Fetching

### useFetch — primary choice for SSR-safe page data

```ts
// Automatically forwards server-fetched data into the Nuxt payload.
// Avoids a second fetch on client hydration.
const { data: product, status, error } = await useFetch(`/api/products/${route.params.slug}`)
```

### useAsyncData — when you need a custom key or composite sources

```ts
const route = useRoute()  // always use Nuxt's useRoute, not vue-router's

const { data: article, status, error, refresh } = await useAsyncData(
  () => `article:${route.params.slug}`,   // stable cache key
  () => $fetch(`/api/articles/${route.params.slug}`),
)

// Non-critical data that shouldn't block navigation:
const { data: comments } = await useLazyAsyncData(
  `comments:${route.params.slug}`,
  () => $fetch(`/api/articles/${route.params.slug}/comments`),
)
```

### $fetch — only for user-triggered mutations

```ts
// Forms, cart actions, non-SSR writes
async function submitForm(data: FormData) {
  await $fetch('/api/contact', { method: 'POST', body: data })
}
```

### Payload size

```ts
// Pick only what the template needs
const { data } = await useFetch('/api/products', {
  pick: ['id', 'name', 'price', 'slug'],
})
```

---

## Route Rules

Set rendering and caching strategy per route in `nuxt.config.ts`:

```ts
export default defineNuxtConfig({
  routeRules: {
    '/': { prerender: true },           // static HTML at build time
    '/products/**': { swr: 3600 },      // serve cache, revalidate in background
    '/blog/**': { isr: true },          // incremental static regeneration
    '/dashboard/**': { ssr: false },    // full SPA, no SSR
    '/admin/**': { ssr: false },
    '/api/**': { cache: { maxAge: 3600 } },
  },
})
```

**Choose per route group** — marketing pages, catalogs, dashboards, and APIs each typically need a different strategy.

---

## Lazy Loading & Lazy Hydration

Nuxt code-splits pages by route automatically. Add component-level lazy loading for non-critical UI:

```vue
<template>
  <!-- Lazy: chunk is only loaded when v-if becomes true -->
  <LazyRecommendations v-if="showRecommendations" />

  <!-- Lazy hydration: renders in SSR HTML but defers JS until visible -->
  <LazyProductGallery hydrate-on-visible />

  <!-- Defer until browser is idle -->
  <LazyChatWidget hydrate-on-idle :timeout="2000" />
</template>
```

Custom lazy hydration strategy:

```ts
const MyHeavyWidget = defineLazyHydrationComponent(
  () => import('./MyHeavyWidget.vue'),
  { strategy: 'visible' },
)
```

**Note**: passing new props to a lazily hydrated component triggers immediate hydration.

Use `NuxtLink` for all internal navigation — Nuxt prefetches route components and payload on hover.

---

## Checklist

- [ ] First SSR render and hydrated client render produce identical markup
- [ ] No `window.*` / `Date.now()` / `Math.random()` in SSR-rendered template state
- [ ] All page data uses `useFetch` or `useAsyncData`, not top-level `$fetch`
- [ ] Non-critical data is lazy (`useLazyFetch` / `server: false`) with a loading UI
- [ ] Route rules match each page's SEO requirements and data freshness needs
- [ ] Heavy interactive sections use `<LazyXxx hydrate-on-visible />` or `<ClientOnly>`
- [ ] `useRoute()` from Nuxt used — not directly from `vue-router`
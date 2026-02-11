---
name: nextjs16-guide
description: >
  This skill should be used when the user asks about "Next.js",
  "Next.js 16", "React Server Components", "App Router",
  "frontend", "Turbopack", "use cache", "proxy.ts", or needs
  guidance on building production Next.js 16 applications.
version: 0.1.0
---

# Next.js 16 Guide

Production patterns for Next.js 16 (released Oct 2025) with Turbopack, Cache Components, and React 19.2.

## Key Features in Next.js 16

| Feature | Description |
|---------|-------------|
| **Turbopack (stable)** | Default bundler, 5-10x faster refresh |
| **Cache Components** | `"use cache"` directive for explicit caching |
| **Partial Pre-Rendering** | Instant navigation with new caching model |
| **React 19.2** | Latest React with Server Components, Actions |
| **proxy.ts** | Replaces middleware for network boundary concerns |
| **DevTools MCP** | Model Context Protocol for debugging |

## App Router Patterns

### Route Structure
```
src/app/
├── layout.tsx          # Root layout (required)
├── page.tsx            # Home page
├── error.tsx           # Error boundary
├── loading.tsx         # Loading UI
├── not-found.tsx       # 404 page
├── api/                # API routes
│   └── v1/
│       └── [resource]/
│           └── route.ts
├── (auth)/             # Route group (no URL segment)
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
├── dashboard/
│   ├── layout.tsx      # Dashboard layout
│   ├── page.tsx
│   └── [id]/
│       └── page.tsx
└── (marketing)/
    ├── about/
    └── pricing/
```

### Server vs Client Components

```
Server Component (default)     Client Component ("use client")
─────────────────────────      ────────────────────────────────
✅ Direct DB/API access        ✅ useState, useEffect
✅ Async/await in component    ✅ Event handlers (onClick)
✅ Access backend resources    ✅ Browser APIs
✅ Reduce client JS bundle     ✅ Interactive UI elements
❌ No state, no effects        ❌ No direct DB access
❌ No browser APIs             ❌ Adds to client bundle
```

**Rule**: Default to Server Components. Use `"use client"` only when you need interactivity.

### Cache Components (`"use cache"`)

```tsx
// Explicit caching with the new "use cache" directive
async function ProductList() {
  "use cache"
  const products = await db.product.findMany()
  return <div>{products.map(p => <ProductCard key={p.id} product={p} />)}</div>
}

// Cache with revalidation
async function DashboardStats() {
  "use cache"
  // cacheLife("hours") — revalidates every hour
  const stats = await fetchStats()
  return <StatsDisplay stats={stats} />
}
```

### proxy.ts (Replaces Middleware)

```typescript
// proxy.ts — runs at the network boundary
import { proxy } from 'next/server'

export default proxy({
  '/api/*': {
    headers: { 'X-Request-ID': crypto.randomUUID() },
  },
  '/dashboard/*': {
    redirect: (req) => {
      if (!req.cookies.get('session')) return '/login'
    },
  },
})
```

## Data Fetching Patterns

### Server Component Data Fetching
```tsx
// app/dashboard/page.tsx — Server Component
export default async function DashboardPage() {
  const data = await fetchDashboardData()  // Direct async
  return <Dashboard data={data} />
}
```

### Server Actions
```tsx
// app/actions.ts
"use server"

export async function createUser(formData: FormData) {
  const name = formData.get("name") as string
  const user = await db.user.create({ data: { name } })
  revalidatePath("/users")
  return user
}
```

### Client-Side Data (SWR/React Query)
```tsx
"use client"
import useSWR from "swr"

export function UserProfile({ userId }: { userId: string }) {
  const { data, error, isLoading } = useSWR(`/api/users/${userId}`, fetcher)
  if (isLoading) return <Skeleton />
  if (error) return <ErrorDisplay error={error} />
  return <Profile user={data} />
}
```

## Project Structure

```
src/
├── app/                    # App Router pages
├── components/
│   ├── ui/                 # Reusable UI primitives
│   │   ├── button.tsx
│   │   ├── input.tsx
│   │   └── card.tsx
│   ├── features/           # Feature-specific components
│   │   ├── auth/
│   │   └── dashboard/
│   └── layouts/            # Layout components
├── lib/
│   ├── api.ts              # API client
│   ├── auth.ts             # Auth utilities
│   └── utils.ts            # Shared utilities
├── hooks/                  # Custom React hooks
├── types/                  # TypeScript types
├── styles/                 # Global styles
└── config/                 # App configuration
```

## TypeScript Configuration

```json
{
  "compilerOptions": {
    "target": "ES2022",
    "lib": ["dom", "dom.iterable", "esnext"],
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "moduleResolution": "bundler",
    "jsx": "preserve",
    "plugins": [{ "name": "next" }],
    "paths": { "@/*": ["./src/*"] }
  }
}
```

## Additional Resources

- **`references/nextjs16-patterns.md`** — advanced patterns and migration guide

---
name: nextjs16-guide
description: >
  Skill này được sử dụng khi người dùng hỏi về "Next.js",
  "Next.js 16", "React Server Components", "App Router",
  "frontend", "Turbopack", "use cache", "proxy.ts", hoặc cần
  hướng dẫn xây dựng ứng dụng Next.js 16 production.
version: 0.1.0
---

# Hướng dẫn Next.js 16

Các pattern production cho Next.js 16 (phát hành tháng 10/2025) với Turbopack, Cache Components, và React 19.2.

## Tính năng chính trong Next.js 16

| Tính năng | Mô tả |
|-----------|-------|
| **Turbopack (ổn định)** | Bundler mặc định, refresh nhanh hơn 5-10 lần |
| **Cache Components** | Directive `"use cache"` cho caching tường minh |
| **Partial Pre-Rendering** | Điều hướng tức thì với mô hình caching mới |
| **React 19.2** | React mới nhất với Server Components, Actions |
| **proxy.ts** | Thay thế middleware cho các vấn đề ranh giới mạng |
| **DevTools MCP** | Model Context Protocol để debug |

## Pattern App Router

### Cấu trúc Route
```
src/app/
├── layout.tsx          # Root layout (bắt buộc)
├── page.tsx            # Trang chủ
├── error.tsx           # Error boundary
├── loading.tsx         # Giao diện loading
├── not-found.tsx       # Trang 404
├── api/                # API route
│   └── v1/
│       └── [resource]/
│           └── route.ts
├── (auth)/             # Route group (không tạo URL segment)
│   ├── login/
│   │   └── page.tsx
│   └── register/
│       └── page.tsx
├── dashboard/
│   ├── layout.tsx      # Layout cho dashboard
│   ├── page.tsx
│   └── [id]/
│       └── page.tsx
└── (marketing)/
    ├── about/
    └── pricing/
```

### Server Components vs Client Components

```
Server Component (mặc định)       Client Component ("use client")
─────────────────────────────      ────────────────────────────────
✅ Truy cập DB/API trực tiếp      ✅ useState, useEffect
✅ Async/await trong component     ✅ Event handler (onClick)
✅ Truy cập tài nguyên backend     ✅ Browser API
✅ Giảm client JS bundle           ✅ UI element tương tác
❌ Không có state, effect           ❌ Không truy cập DB trực tiếp
❌ Không có browser API             ❌ Tăng kích thước client bundle
```

**Quy tắc**: Mặc định sử dụng Server Components. Chỉ dùng `"use client"` khi cần tương tác.

### Cache Components (`"use cache"`)

```tsx
// Caching tường minh với directive "use cache" mới
async function ProductList() {
  "use cache"
  const products = await db.product.findMany()
  return <div>{products.map(p => <ProductCard key={p.id} product={p} />)}</div>
}

// Cache với revalidation
async function DashboardStats() {
  "use cache"
  // cacheLife("hours") — revalidate mỗi giờ
  const stats = await fetchStats()
  return <StatsDisplay stats={stats} />
}
```

### proxy.ts (Thay thế Middleware)

```typescript
// proxy.ts — chạy tại ranh giới mạng
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

## Pattern lấy dữ liệu

### Lấy dữ liệu trong Server Component
```tsx
// app/dashboard/page.tsx — Server Component
export default async function DashboardPage() {
  const data = await fetchDashboardData()  // Async trực tiếp
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

### Dữ liệu phía Client (SWR/React Query)
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

## Cấu trúc dự án

```
src/
├── app/                    # Trang App Router
├── components/
│   ├── ui/                 # UI primitive tái sử dụng
│   │   ├── button.tsx
│   │   ├── input.tsx
│   │   └── card.tsx
│   ├── features/           # Component theo tính năng
│   │   ├── auth/
│   │   └── dashboard/
│   └── layouts/            # Component layout
├── lib/
│   ├── api.ts              # API client
│   ├── auth.ts             # Tiện ích xác thực
│   └── utils.ts            # Tiện ích dùng chung
├── hooks/                  # Custom React hook
├── types/                  # TypeScript type
├── styles/                 # Style toàn cục
└── config/                 # Cấu hình ứng dụng
```

## Cấu hình TypeScript

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

## Tài liệu bổ sung

- **`references/nextjs16-patterns.md`** — pattern nâng cao và hướng dẫn migration

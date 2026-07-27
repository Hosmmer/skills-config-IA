---
name: react-CineViewHos
description: "React 18 + Vite + TypeScript patterns for CineViewHos. Component patterns, forms (Formik+Yup), data fetching (React Query+Axios), routing, imports. Load always when editing frontend/**/*."
---

# React CineViewHos

React 18 + Vite + TypeScript frontend with dark cinema theme.

## Tech Stack

- React 18.3, Vite, TypeScript (strict)
- React Router 6.26, TanStack React Query 5.56
- Formik 2.4 + Yup 1.4, Axios 1.7
- Tailwind CSS 3.4, lucide-react 1.18
- react-intl 6.7 (i18n infra ready, not actively used)
- Vitest + Testing Library (vitest.config with jsdom)

## Component Rules (HARD)

1. **Function declarations only** — `function MyComp() {}`, never arrow functions
2. **Default exports** — `export default MyComponent`
3. **Import order**: React → 3rd-party → `@/api` → `@/contexts` → `@/services` → `@/types` → `@/components` → `@/hooks`

## Imports

```typescript
// React
import { useState, useEffect, useRef, useContext } from "react";
import { Link, useNavigate, useParams, useLocation, useSearchParams } from "react-router-dom";

// 3rd-party
import { useFormik } from "formik";
import * as yup from "yup";          // auth pages
import * as Yup from "yup";          // admin pages
import { useQuery, useMutation, useQueryClient } from "@tanstack/react-query";
import { User, ChevronDown, X } from "lucide-react";

// Internal (type-only imports use `import type`)
import type { User } from "@/types/auth";
import type { MovieList, PaginatedResponse } from "@/types/movies";
import { useAuth } from "@/contexts/AuthContext";
import { fetchMovies, deleteMovie } from "@/services/movieService";
```

## Forms (Formik + Yup)

```typescript
const validationSchema = yup.object({
  name: yup.string().required("Name is required"),
});

const formik = useFormik({
  initialValues: { name: "" },
  validationSchema,
  enableReinitialize: true,
  onSubmit: async (values, { setFieldError }) => {
    try {
      await doSomething(values);
    } catch (err: unknown) {
      const msg = err instanceof Error ? err.message : "Default error";
      setFieldError("name", msg);
    }
  },
});
```

Input pattern:
```tsx
<input id="name" type="text"
  {...formik.getFieldProps("name")}
  className={`w-full bg-gray-800 text-white text-sm rounded-lg px-4 py-2.5 border ${
    formik.touched.name && formik.errors.name
      ? "border-red-500" : "border-gray-700 focus:border-red-500"
  } focus:outline-none focus:ring-1 focus:ring-red-500`}
/>
{formik.touched.name && formik.errors.name && (
  <p className="text-red-400 text-xs mt-1">{formik.errors.name}</p>
)}
```

## Data Fetching

```typescript
// Query
const { data, isLoading, error } = useQuery<PaginatedResponse<MovieList>>({
  queryKey: ["public-movies"],
  queryFn: () => fetchMovies({ ordering: "-created_at" }),
});

// Mutation
const deleteMutation = useMutation({
  mutationFn: deleteMovie,
  onSuccess: () => {
    queryClient.invalidateQueries({ queryKey: ["admin-movies"] });
    setDeleteId(null);
  },
});
```

## Loading States

- **Spinner**: `<div className="animate-spin h-8 w-8 border-4 border-red-500 border-t-transparent rounded-full" />`
- **Skeleton**: `<div className="h-12 bg-gray-800 rounded animate-pulse" />`
- **Button**: `{isPending ? "Saving..." : "Save"}`

## Error States

- **Full page**: `text-red-400` message + Retry button
- **Field**: `text-red-400 text-xs mt-1`
- **Banner**: `text-sm text-red-400 bg-red-900/20 border border-red-900/50 rounded-lg px-4 py-3`

## Deep-Dive References

Read `frontend/.opencode/skills/react/references/`:
- **hooks-patterns.md** — Custom hooks, useQuery, useMutation, Formik+Query
- **state-management.md** — Context, localStorage, useState
- **component-architecture.md** — Component types, layout strategy, CRUD pattern
- **testing-react.md** — Vitest + Testing Library + customRender
- **performance.md** — Lazy loading, caching, memo rules
- **i18n.md** — react-intl setup and conventions

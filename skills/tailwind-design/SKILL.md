---
name: tailwind-design
description: "UI design system for CineViewHos. Dark cinema theme, color palette, component styling patterns, icon usage. Load always when editing frontend/**/*."
---

# Tailwind Design — CineViewHos

Dark cinema theme using Tailwind CSS 3.4 only. No CSS modules, no SCSS, no styled-components.

## HARD Rules

1. **No grey UI**: `btn-secondary`, `text-muted`, `#94A3B8`, `#8E8E93`, `rgba(142,142,147` are **banned**. Always use a colored variant.
2. **No emojis in UI**: Always use SVG inline or `lucide-react` icons.
3. **Surgical scope**: Touch ONLY what's asked. No CSS/selector creep.

## Color Palette

| Purpose | Classes |
|---------|---------|
| Page background | `bg-gray-900` |
| Card/section | `bg-gray-800/80` with `backdrop-blur`, `bg-gray-950` |
| Input background | `bg-gray-700/50`, `bg-gray-800` |
| Borders | `border-gray-700`, `border-gray-800`, `border-gray-700/50` |
| Text primary | `text-white` |
| Text secondary | `text-gray-300`, `text-gray-400` |
| Text muted | `text-gray-500` (only for placeholder/icons, never UI elements) |
| Accent (red) | `bg-red-600`, `hover:bg-red-700`, `text-red-500`, `text-red-400`, `border-red-500` |
| Success | `bg-green-500/20 text-green-400` |
| Error | `text-red-400 bg-red-900/20 border-red-900/50` |
| Focus ring | `focus:ring-2 focus:ring-red-500 focus:border-transparent` |

## Component Style Templates

### Input
```
w-full px-4 py-2.5 bg-gray-700/50 text-white text-sm rounded-lg border border-gray-600
focus:outline-none focus:ring-2 focus:ring-red-500 focus:border-transparent
placeholder-gray-400 transition-all
```

### Disabled Input
```
w-full px-4 py-2.5 bg-gray-700/30 text-gray-500 text-sm rounded-lg border border-gray-600/50
cursor-not-allowed
```

### Input Error
Add conditional `border-red-500` when `formik.touched.field && formik.errors.field`.

### Primary Button
```
px-4 py-2 bg-red-600 hover:bg-red-700 text-white rounded-md text-sm font-medium transition-colors
disabled:opacity-50 disabled:cursor-not-allowed
```

### Secondary Button (avoid grey — use colored variants)
```
px-4 py-2 bg-gray-700 text-gray-300 rounded-md hover:bg-gray-600 transition-colors
```

### Danger Button
```
px-3 py-1 bg-red-900/50 text-red-400 rounded hover:bg-red-900
```

### Card
```
bg-gray-800/80 backdrop-blur-sm rounded-lg border border-gray-700
```

### Modal Overlay
```
fixed inset-0 bg-black/60 backdrop-blur-sm flex items-center justify-center z-50
```

## Layout Patterns

```
min-h-screen                    Full viewport
flex flex-col                   Vertical stacking
max-w-7xl mx-auto               Centered container
max-w-sm / max-w-lg / max-w-2xl Form containers
grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4  Movie grid
```

## Icons (lucide-react)

```typescript
import { User, ChevronDown, X, Menu } from "lucide-react";
<User className="w-5 h-5 text-gray-400" />
```

Fallback inline SVG for icons not in lucide-react:
```tsx
<svg className="w-12 h-12 text-gray-600" fill="none" stroke="currentColor" viewBox="0 0 24 24">
  <path strokeLinecap="round" strokeLinejoin="round" strokeWidth={1.5} d="..." />
</svg>
```

## Loading States

- Spinner: `animate-spin h-8 w-8 border-4 border-red-500 border-t-transparent rounded-full`
- Skeleton: `h-12 bg-gray-800 rounded animate-pulse`

## index.css

Contains ONLY Tailwind directives:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

No custom CSS classes. Use Tailwind utilities exclusively.

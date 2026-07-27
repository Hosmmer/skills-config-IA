---
name: frontend-testing
description: "Vitest + Testing Library conventions for CineViewHos frontend. customRender with all providers, component test patterns, mocking. Load when writing or modifying frontend tests."
---

# Frontend Testing — CineViewHos

Vitest + Testing Library for React 18 components.

## Quick Start

```bash
cd frontend && vitest
```

Full details in `frontend/.opencode/skills/react/references/testing-react.md`.

## Key Patterns

### Always use `customRender`

Never use bare `render` from Testing Library. `customRender` wraps in all providers:
```typescript
import { customRender, screen, waitFor, userEvent } from "@/test/test-utils";
```

### Component Test Template

```typescript
import { describe, it, expect, vi } from "vitest";
import { customRender, screen, userEvent } from "@/test/test-utils";
import MyComponent from "@/components/MyComponent";

describe("MyComponent", () => {
  it("renders correctly", () => {
    customRender(<MyComponent />);
    expect(screen.getByText(/expected text/i)).toBeInTheDocument();
  });

  it("handles user interaction", async () => {
    customRender(<MyComponent />);
    await userEvent.click(screen.getByRole("button", { name: /click me/i }));
    expect(await screen.findByText(/result/i)).toBeInTheDocument();
  });
});
```

### Query Priority

1. `screen.getByRole(...)` — most accessible
2. `screen.getByLabelText(...)` — form inputs
3. `screen.getByText(...)` — visible text
4. `screen.getByTestId(...)` — last resort

### Async queries

- `screen.findBy*` — waits with timeout, use for elements that appear after state change
- `getBy*` — synchronous, use for initial render

### Mocking

```typescript
vi.mock("@/services/movieService", () => ({
  fetchMovies: vi.fn().mockResolvedValue({ results: [], count: 0 }),
}));
```

### Vitest globals

`describe`, `it`, `expect`, `vi` are available without imports (vitest.config has `globals: true`).

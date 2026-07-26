# Always load repo skills before touching frontend/backend code

When working on CineViewHos code, ALWAYS load the relevant skill before writing or modifying:

- **Frontend** (`frontend/src/`): Load `react-CineViewHos` + `tailwind-design` skills
  - Follow module anatomy: `_models.ts` → `_requests.ts` → hooks → components
  - Use CineViewHos dark cinema theme tokens (NOT XenodocIA's design system)
  - Types first, then request functions, then hooks, then components

- **Backend** (`backend/`): Load `django-cineviewhos` skill
  - Follow service-layer pattern: models → serializers → services → views
  - Use `BaseService` / `ServiceResult` pattern

- **XenodocIA** (`xenodocIA/`): Separate repo, separate design system — NOT the same as CineViewHos frontend

Never write code without loading the skill first. The skills define the conventions, patterns, and rules.

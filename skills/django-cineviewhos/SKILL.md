---
name: django-cineviewhos
description: "Django + DRF patterns for CineViewHos. Service-layer architecture, model/serializer/view conventions, error handling, permissions, URL routing. Load always when editing backend/**/* or xenodocIA/**/*.py."
---

# Django CineViewHos

Django 3.0.7 + DRF project with a strict **4-layer service-layer architecture**:

```
models.py  →  serializers.py  →  services.py  →  views.py
```

## Architecture Rules

1. **Models** keep data shape and constraints. No business logic.
2. **Serializers** handle validation and representation. Never contain business logic.
3. **Services** contain ALL business logic. Return `ServiceResult`. Never raise exceptions for business errors.
4. **Views** coordinate: validate serializer → call service → unwrap `ServiceResult` → return `Response`.

## Service Layer

### BaseService + ServiceResult

```python
from apps.core.services.base import BaseService
from apps.core.data_classes import ServiceResult

class MyService(BaseService):
    def create_xxx(self, **kwargs) -> ServiceResult:
        if error_condition:
            return self.error("Message in English.", 400)
        obj = Model.objects.create(**kwargs)
        return self.success(data={"id": obj.id}, status_code=201)
```

### Status Codes

- 200: ok with data
- 201: created
- 204: deleted (no content)
- 400: validation/business error
- 403: forbidden
- 404: not found
- 409: conflict

### Error Pattern

Services NEVER raise exceptions for business errors. Return `ServiceResult(success=False, error="msg", status_code=XXX)`.

Views ALWAYS return `Response({"detail": result.error}, status=result.status_code)` for errors.

### Transaction + Locking

```python
with transaction.atomic():
    reserva = Reserva.objects.select_for_update().get(id=reserva_id)
```

## Deep-Dive References

For detailed patterns, read the reference files in `backend/.opencode/skills/django/references/`:

- **models-orm.md** — Model conventions, fields, Meta, soft delete, signals
- **drf-serializers.md** — Serializer patterns, naming, related data, validation
- **viewsets-views.md** — ViewSet types, admin/public split, permissions, actions
- **testing-django.md** — Pytest fixtures, service/view test patterns

## Quick Conventions

| Item | Convention |
|------|-----------|
| Import order | stdlib → django/rest_framework → `apps.*` |
| Internal imports | Absolute `apps.movies.models` or relative `.models` |
| URL basename | `admin-{model}` for admin, `{model}` for public |
| Soft delete | `is_active = False`, never hard-delete |
| "Anular" | Never "cancelar" for reservations |
| Error messages | English |
| Field names / models | Spanish where domain-appropriate (`sala`, `funcion`, `reserva`) |
| `settings.AUTH_USER_MODEL` | Always use, never hardcode User model |
| `get_user_model()` | Import at runtime for User references |

## File Layout per App

```
apps/{domain}/
├── models.py
├── serializers.py
├── services.py
├── views.py          # Admin views
├── views_public.py   # Public/authenticated views
├── urls.py            # Admin URLs
├── urls_public.py     # Public URLs
└── admin.py           # Django admin (if needed)
```

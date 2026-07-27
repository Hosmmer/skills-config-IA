---
name: backend-testing
description: "Pytest + pytest-django testing conventions for CineViewHos backend. Service tests, view tests, fixtures, auth. Load when writing or modifying backend tests."
---

# Backend Testing — CineViewHos

Pytest with pytest-django for the CineViewHos backend.

## Quick Start

```bash
cd backend && pytest
```

Full details in `backend/.opencode/skills/django/references/testing-django.md`.

## Key Patterns

### Fixtures always need `db`

```python
@pytest.fixture
def user(db):
    return User.objects.create_user(username="testuser", password="testpass123")

@pytest.fixture
def auth_client(api_client, client_user):
    from rest_framework_simplejwt.tokens import RefreshToken
    token = str(RefreshToken.for_user(client_user).access_token)
    api_client.credentials(HTTP_AUTHORIZATION=f"Bearer {token}")
    return api_client
```

### All test methods need `@pytest.mark.django_db`

```python
@pytest.mark.django_db
def test_create_sala_generates_seats(self):
    ...
```

### Plain assertions (pytest style)

```python
assert result.success
assert response.status_code == 201
assert data["name"] == "Action"
```

### API responses use `format="json"`

```python
response = auth_client.post("/api/reservas/", payload, format="json")
```

### Test classes by feature

```python
class TestSalaService: ...
class TestPublicReservaEndpoints: ...
```

### Error assertion pattern

```python
assert not result.success
assert result.status_code == 400
assert "error message" in result.error
```

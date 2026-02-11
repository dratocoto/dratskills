---
name: conventions
description: >
  This skill should be used when the user asks about "coding conventions",
  "code standards", "naming rules", "project rules", or any agent needs
  to check the coding standards before writing code. This is the compact
  ruleset that all agents reference.
version: 0.1.0
---

# Project Conventions

Compact, scannable rules for all AI agents. Use checklist format for minimal context usage.

## Python / FastAPI Conventions

### Naming
- [ ] `snake_case` — functions, variables, modules, files
- [ ] `PascalCase` — classes, Pydantic models, exceptions
- [ ] `SCREAMING_SNAKE` — constants
- [ ] `_prefixed` — private/internal

### Models (SQLAlchemy)
- [ ] Inherit from `Base` (declarative base)
- [ ] `UUID` primary key (`id = Column(UUID, primary_key=True, default=uuid4)`)
- [ ] Include `created_at`, `updated_at` columns
- [ ] Table name = lowercase plural (`__tablename__ = "users"`)
- [ ] Type hints on all columns
- [ ] Relationships use `selectin` lazy loading

### Schemas (Pydantic)
- [ ] Inherit from `BaseModel` with `model_config = ConfigDict(from_attributes=True)`
- [ ] Separate: `Create*`, `Update*`, `*Response` schemas
- [ ] `Update*` schema: all fields `Optional` (partial update)
- [ ] Field validation with `Field()` constraints
- [ ] Use `UUID` not `str` for ID fields

### Repository
- [ ] Inherit from `BaseRepository[ModelT]`
- [ ] ALL methods `async def`
- [ ] Accept `AsyncSession` via DI
- [ ] Return domain models, not dicts
- [ ] Use `select()` builder, never raw SQL strings
- [ ] Include pagination: `offset` + `limit` params

### Service
- [ ] Accept repository via `__init__`
- [ ] ALL methods `async def`
- [ ] Raise domain exceptions (`NotFoundError`, `ConflictError`)
- [ ] No HTTP/request concerns (no `Request`, no `Response`)
- [ ] Validate business rules before persistence
- [ ] Log significant actions with `structlog`

### Routes
- [ ] Use `APIRouter` with `prefix` and `tags`
- [ ] Accept service via `Depends()`
- [ ] Use Pydantic schemas for request/response types
- [ ] Return Pydantic models (auto-serialized)
- [ ] Define `response_model` on every endpoint
- [ ] Include `status_code` on create (201), delete (204)

### Error Handling
- [ ] Custom exception hierarchy from `AppError`
- [ ] Register `exception_handler` for `AppError` in main.py
- [ ] Never expose stack traces in API responses
- [ ] Log full error context with `logger.error()`
- [ ] Wrap external errors in domain exceptions

### Async
- [ ] `async def` for ALL I/O operations
- [ ] Use `asyncpg` driver (not `psycopg2`)
- [ ] Use `httpx.AsyncClient` (not `requests`)
- [ ] Use `aiofiles` for file I/O
- [ ] Use `asyncio.gather()` for concurrent operations
- [ ] NEVER block the event loop with sync calls

### Logging
- [ ] Use `structlog` (not `logging` or `print`)
- [ ] Structured key-value format
- [ ] Levels: ERROR (broken), WARN (degraded), INFO (events), DEBUG (dev)
- [ ] NEVER log passwords, tokens, PII
- [ ] Include correlation/request ID

## TypeScript / Next.js Conventions

### Naming
- [ ] `camelCase` — variables, functions
- [ ] `PascalCase` — components, types, interfaces
- [ ] `SCREAMING_SNAKE` — constants
- [ ] `kebab-case` — file names

### Components
- [ ] Default to Server Components (no directive)
- [ ] Add `"use client"` ONLY for interactivity
- [ ] Props interface defined above component
- [ ] Use `"use cache"` for data-heavy server components

### Types
- [ ] `strict: true` in tsconfig
- [ ] Explicit return types on exported functions
- [ ] `interface` for object shapes, `type` for unions/intersections
- [ ] `unknown` over `any`; narrow with type guards

### State & Data
- [ ] Server Components: direct async data fetching
- [ ] Client Components: SWR or React Query
- [ ] Forms: Server Actions with `"use server"`
- [ ] URL state: `useSearchParams` for filterable views

## Testing Conventions

### All Languages
- [ ] Test file naming: `test_<module>.py` / `*.test.ts`
- [ ] Arrange-Act-Assert pattern
- [ ] Descriptive names: `test_<unit>_<scenario>_<expected>`
- [ ] Mock external dependencies only
- [ ] Both positive AND negative test cases
- [ ] Test error messages and types
- [ ] ≥80% coverage on business logic
- [ ] Tests are independent and idempotent

## Git Conventions

- [ ] Branch: `feature/FEAT-XXX-description`, `fix/BUG-XXX-description`
- [ ] Commit: Conventional Commits (`feat:`, `fix:`, `refactor:`, `test:`, `docs:`)
- [ ] PR: link to feature requirement doc
- [ ] Never commit `.env`, secrets, or large binaries

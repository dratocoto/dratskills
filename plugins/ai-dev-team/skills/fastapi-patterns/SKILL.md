---
name: fastapi-patterns
description: >
  This skill should be used when the user asks about "FastAPI", "API design",
  "repository pattern", "service layer", "dependency injection",
  "async patterns", "FastAPI project structure", or needs guidance on
  building production FastAPI applications with clean architecture.
version: 0.1.0
---

# FastAPI Patterns

Production patterns for FastAPI applications using clean architecture with Python 3.12.

## Architecture: 3-Tier + Repository Pattern

```
Route (Controller) → Service → Repository → Database
     ↑                  ↑           ↑
  Pydantic          Business     SQLAlchemy
  Schemas            Logic        Models
```

### Layer Responsibilities

| Layer | Does | Does NOT |
|-------|------|----------|
| **Route** | Parse request, validate input, call service, format response | Business logic, DB queries |
| **Service** | Business rules, orchestrate repositories, error handling | HTTP concerns, raw SQL |
| **Repository** | CRUD operations, query building, data mapping | Business rules, HTTP |
| **Schema** | Input validation, serialization, API contracts | Business logic |
| **Model** | Database table mapping, relationships | Validation, business rules |

## Dependency Injection Pattern

FastAPI's `Depends()` is the core DI mechanism:

```python
# dependencies.py
from fastapi import Depends
from sqlalchemy.ext.asyncio import AsyncSession

async def get_db() -> AsyncGenerator[AsyncSession, None]:
    async with async_session_maker() as session:
        yield session

def get_user_repo(db: AsyncSession = Depends(get_db)) -> UserRepository:
    return UserRepository(db)

def get_user_service(repo: UserRepository = Depends(get_user_repo)) -> UserService:
    return UserService(repo)

# route.py
@router.post("/users")
async def create_user(
    data: CreateUserSchema,
    service: UserService = Depends(get_user_service),
) -> UserResponse:
    return await service.create(data)
```

### DI Chain
```
Route depends on Service
  Service depends on Repository
    Repository depends on DB Session
```

## Repository Pattern

```python
# repositories/base.py
from typing import Generic, TypeVar, Type
from sqlalchemy.ext.asyncio import AsyncSession
from sqlalchemy import select

ModelT = TypeVar("ModelT")

class BaseRepository(Generic[ModelT]):
    def __init__(self, model: Type[ModelT], session: AsyncSession):
        self.model = model
        self.session = session

    async def get_by_id(self, id: UUID) -> ModelT | None:
        return await self.session.get(self.model, id)

    async def get_all(self, *, offset: int = 0, limit: int = 20) -> list[ModelT]:
        stmt = select(self.model).offset(offset).limit(limit)
        result = await self.session.execute(stmt)
        return list(result.scalars().all())

    async def create(self, **kwargs) -> ModelT:
        instance = self.model(**kwargs)
        self.session.add(instance)
        await self.session.flush()
        return instance

    async def update(self, instance: ModelT, **kwargs) -> ModelT:
        for key, value in kwargs.items():
            setattr(instance, key, value)
        await self.session.flush()
        return instance

    async def delete(self, instance: ModelT) -> None:
        await self.session.delete(instance)
        await self.session.flush()
```

## Service Layer Pattern

```python
# services/user_service.py
from app.errors import NotFoundError, ConflictError

class UserService:
    def __init__(self, repo: UserRepository):
        self.repo = repo

    async def create(self, data: CreateUserSchema) -> User:
        existing = await self.repo.get_by_email(data.email)
        if existing:
            raise ConflictError("User", "email", data.email)

        hashed = hash_password(data.password)
        return await self.repo.create(
            email=data.email,
            hashed_password=hashed,
            name=data.name,
        )

    async def get_or_404(self, user_id: UUID) -> User:
        user = await self.repo.get_by_id(user_id)
        if not user:
            raise NotFoundError("User", str(user_id))
        return user
```

## Error Handling

```python
# errors.py
class AppError(Exception):
    def __init__(self, message: str, code: str, status_code: int = 500):
        self.message = message
        self.code = code
        self.status_code = status_code

class NotFoundError(AppError):
    def __init__(self, resource: str, id: str):
        super().__init__(f"{resource} {id} not found", "NOT_FOUND", 404)

class ConflictError(AppError):
    def __init__(self, resource: str, field: str, value: str):
        super().__init__(f"{resource} with {field}={value} exists", "CONFLICT", 409)

class ValidationError(AppError):
    def __init__(self, message: str, details: dict):
        super().__init__(message, "VALIDATION_ERROR", 400)
        self.details = details

# middleware/error_handler.py
@app.exception_handler(AppError)
async def app_error_handler(request: Request, exc: AppError) -> JSONResponse:
    return JSONResponse(
        status_code=exc.status_code,
        content={"error": {"code": exc.code, "message": exc.message}},
    )
```

## Async Best Practices

### Rules
1. Use `async def` for ALL I/O operations (DB, HTTP, file)
2. Use async DB driver: `asyncpg` for PostgreSQL
3. Use `httpx.AsyncClient` for external API calls (not `requests`)
4. Use `aiofiles` for file operations
5. NEVER call blocking functions inside `async def`
6. Use `BackgroundTasks` for lightweight async work
7. Use Celery/Dramatiq for heavy background processing

### Database Session Management
```python
# database/session.py
from sqlalchemy.ext.asyncio import create_async_engine, async_sessionmaker

engine = create_async_engine(
    settings.DATABASE_URL,
    pool_pre_ping=True,
    pool_recycle=3600,
    pool_size=20,
    max_overflow=10,
)

async_session_maker = async_sessionmaker(engine, expire_on_commit=False)
```

## Project Structure

```
src/
├── app/
│   ├── __init__.py
│   ├── main.py              # App factory
│   ├── config.py            # Pydantic Settings
│   ├── database.py          # Engine + session
│   ├── errors.py            # Error hierarchy
│   ├── dependencies.py      # Shared DI providers
│   ├── models/              # SQLAlchemy models
│   │   ├── __init__.py
│   │   ├── base.py          # Declarative base + mixins
│   │   └── user.py
│   ├── schemas/             # Pydantic schemas
│   │   ├── __init__.py
│   │   ├── common.py        # Shared schemas (pagination, etc.)
│   │   └── user.py
│   ├── repositories/        # Data access
│   │   ├── __init__.py
│   │   ├── base.py          # Generic base repository
│   │   └── user_repo.py
│   ├── services/            # Business logic
│   │   ├── __init__.py
│   │   └── user_service.py
│   ├── api/
│   │   └── v1/
│   │       ├── __init__.py
│   │       ├── router.py    # Aggregate all routes
│   │       └── routes/
│   │           ├── __init__.py
│   │           ├── health.py
│   │           └── users.py
│   └── middleware/
│       ├── __init__.py
│       ├── auth.py
│       ├── cors.py
│       └── logging.py
```

## Additional Resources

- **`references/design-patterns.md`** — detailed pattern implementations
- **`references/async-patterns.md`** — async/await deep dive
- **`examples/`** — example module implementations

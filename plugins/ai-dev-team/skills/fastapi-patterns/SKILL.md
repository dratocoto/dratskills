---
name: fastapi-patterns
description: >
  Skill này được sử dụng khi người dùng hỏi về "FastAPI", "thiết kế API",
  "repository pattern", "service layer", "dependency injection",
  "async pattern", "cấu trúc dự án FastAPI", hoặc cần hướng dẫn xây dựng
  ứng dụng FastAPI production với kiến trúc sạch.
version: 0.1.0
---

# FastAPI Patterns

Các pattern production cho ứng dụng FastAPI sử dụng kiến trúc sạch với Python 3.12.

## Kiến trúc: 3 tầng + Repository Pattern

```
Route (Controller) → Service → Repository → Database
     ↑                  ↑           ↑
  Pydantic          Business     SQLAlchemy
  Schemas            Logic        Models
```

### Trách nhiệm từng tầng

| Tầng | Làm | KHÔNG làm |
|------|-----|-----------|
| **Route** | Parse request, validate đầu vào, gọi service, format response | Business logic, truy vấn DB |
| **Service** | Business rule, điều phối repository, xử lý lỗi | Logic HTTP, raw SQL |
| **Repository** | Thao tác CRUD, xây dựng query, ánh xạ dữ liệu | Business rule, HTTP |
| **Schema** | Validate đầu vào, serialize, API contract | Business logic |
| **Model** | Ánh xạ bảng DB, relationship | Validate, business rule |

## Pattern Dependency Injection

`Depends()` của FastAPI là cơ chế DI chính:

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

### Chuỗi DI
```
Route phụ thuộc vào Service
  Service phụ thuộc vào Repository
    Repository phụ thuộc vào DB Session
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

## Pattern Service Layer

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

## Xử lý lỗi

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

## Best Practice cho Async

### Quy tắc
1. Sử dụng `async def` cho TẤT CẢ thao tác I/O (DB, HTTP, file)
2. Sử dụng async DB driver: `asyncpg` cho PostgreSQL
3. Sử dụng `httpx.AsyncClient` cho gọi API bên ngoài (không dùng `requests`)
4. Sử dụng `aiofiles` cho thao tác file
5. KHÔNG BAO GIỜ gọi hàm blocking bên trong `async def`
6. Sử dụng `BackgroundTasks` cho công việc async nhẹ
7. Sử dụng Celery/Dramatiq cho xử lý nền nặng

### Quản lý Database Session
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

## Cấu trúc dự án

```
src/
├── app/
│   ├── __init__.py
│   ├── main.py              # App factory
│   ├── config.py            # Pydantic Settings
│   ├── database.py          # Engine + session
│   ├── errors.py            # Hệ thống exception
│   ├── dependencies.py      # Provider DI dùng chung
│   ├── models/              # SQLAlchemy model
│   │   ├── __init__.py
│   │   ├── base.py          # Declarative base + mixin
│   │   └── user.py
│   ├── schemas/             # Pydantic schema
│   │   ├── __init__.py
│   │   ├── common.py        # Schema dùng chung (phân trang, v.v.)
│   │   └── user.py
│   ├── repositories/        # Truy cập dữ liệu
│   │   ├── __init__.py
│   │   ├── base.py          # Repository base generic
│   │   └── user_repo.py
│   ├── services/            # Business logic
│   │   ├── __init__.py
│   │   └── user_service.py
│   ├── api/
│   │   └── v1/
│   │       ├── __init__.py
│   │       ├── router.py    # Tổng hợp tất cả route
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

## Tài liệu bổ sung

- **`references/design-patterns.md`** — triển khai chi tiết các pattern
- **`references/async-patterns.md`** — đào sâu async/await
- **`examples/`** — ví dụ triển khai module

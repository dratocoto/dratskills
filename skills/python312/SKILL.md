---
name: python312
description: >
  This skill should be used when the user asks about "Python 3.12",
  "Python type hints", "Python async", "Python best practices",
  "Python project setup", or needs guidance on Python 3.12 features
  and idioms for production applications.
version: 0.1.0
---

# Python 3.12 Guide

Production Python 3.12 features and best practices for FastAPI applications.

## Key Python 3.12 Features

### Type Parameter Syntax (PEP 695)
```python
# New syntax — cleaner generics
type Point = tuple[float, float]
type Matrix[T] = list[list[T]]

# Generic class with new syntax
class Stack[T]:
    def __init__(self) -> None:
        self._items: list[T] = []
    def push(self, item: T) -> None:
        self._items.append(item)
    def pop(self) -> T:
        return self._items.pop()
```

### Override Decorator (PEP 698)
```python
from typing import override

class BaseService:
    async def validate(self, data: dict) -> bool:
        return True

class UserService(BaseService):
    @override
    async def validate(self, data: dict) -> bool:
        return "email" in data and "name" in data
```

### Improved F-Strings (PEP 701)
```python
# Nested quotes now work
print(f"User {user["name"]} logged in")

# Multi-line expressions
message = f"Total: {
    sum(item.price for item in cart)
:.2f}"
```

### asyncio Performance
- 75% speed improvement in certain benchmarks
- Eager task execution for better concurrency
- Better socket writing performance

## Type Hints Best Practices

```python
# Function signatures: always annotate
async def get_user(user_id: UUID) -> User | None: ...

# Use | instead of Optional (Python 3.10+)
def process(data: str | None = None) -> dict[str, Any]: ...

# Use TypedDict for structured dicts
class UserData(TypedDict):
    name: str
    email: str
    age: int | None

# Use Protocol for structural typing
class Repository(Protocol):
    async def get_by_id(self, id: UUID) -> Any | None: ...
    async def create(self, **kwargs: Any) -> Any: ...
```

## Project Configuration

### pyproject.toml
```toml
[project]
name = "my-app"
version = "0.1.0"
requires-python = ">=3.12"
dependencies = [
    "fastapi>=0.115",
    "uvicorn[standard]>=0.30",
    "sqlalchemy[asyncio]>=2.0",
    "asyncpg>=0.29",
    "pydantic>=2.0",
    "pydantic-settings>=2.0",
    "structlog>=24.0",
    "httpx>=0.27",
]

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "pytest-asyncio>=0.23",
    "pytest-cov>=5.0",
    "ruff>=0.5",
    "mypy>=1.10",
    "httpx>=0.27",
]

[tool.ruff]
target-version = "py312"
line-length = 100

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP", "B", "A", "C4", "SIM", "TCH"]

[tool.mypy]
python_version = "3.12"
strict = true
warn_return_any = true
warn_unused_configs = true

[tool.pytest.ini_options]
asyncio_mode = "auto"
testpaths = ["tests"]
addopts = "-v --tb=short --strict-markers"
```

## Async Patterns

### Async Context Manager
```python
from contextlib import asynccontextmanager

@asynccontextmanager
async def get_db_session():
    async with async_session_maker() as session:
        try:
            yield session
            await session.commit()
        except Exception:
            await session.rollback()
            raise
```

### Concurrent Async Calls
```python
import asyncio

async def fetch_dashboard_data(user_id: UUID) -> DashboardData:
    # Run independent queries concurrently
    user, orders, notifications = await asyncio.gather(
        user_repo.get_by_id(user_id),
        order_repo.get_by_user(user_id),
        notification_repo.get_unread(user_id),
    )
    return DashboardData(user=user, orders=orders, notifications=notifications)
```

### Structured Logging
```python
import structlog

logger = structlog.get_logger()

# Configure once at startup
structlog.configure(
    processors=[
        structlog.contextvars.merge_contextvars,
        structlog.processors.add_log_level,
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.dev.ConsoleRenderer()  # JSON in production
    ],
)

# Usage
logger.info("user.created", user_id=str(user.id), email=user.email)
logger.error("payment.failed", order_id=str(order.id), error=str(e))
```

## Pydantic V2 Patterns

```python
from pydantic import BaseModel, Field, ConfigDict
from datetime import datetime
from uuid import UUID

class UserBase(BaseModel):
    model_config = ConfigDict(from_attributes=True)
    name: str = Field(min_length=1, max_length=100)
    email: str = Field(pattern=r"^[\w.-]+@[\w.-]+\.\w+$")

class CreateUser(UserBase):
    password: str = Field(min_length=8)

class UpdateUser(BaseModel):
    name: str | None = Field(None, min_length=1, max_length=100)
    email: str | None = Field(None, pattern=r"^[\w.-]+@[\w.-]+\.\w+$")

class UserResponse(UserBase):
    id: UUID
    created_at: datetime
    updated_at: datetime

class PaginatedResponse(BaseModel, Generic[T]):
    items: list[T]
    total: int
    page: int
    limit: int
    has_next: bool
```

## Additional Resources

- **`references/python312-features.md`** — full feature reference

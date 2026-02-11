---
name: python312
description: >
  Skill này được sử dụng khi người dùng hỏi về "Python 3.12",
  "Python type hint", "Python async", "Python best practice",
  "thiết lập dự án Python", hoặc cần hướng dẫn về các tính năng
  và idiom của Python 3.12 cho ứng dụng production.
version: 0.1.0
---

# Hướng dẫn Python 3.12

Các tính năng production của Python 3.12 và best practice cho ứng dụng FastAPI.

## Tính năng chính của Python 3.12

### Cú pháp Type Parameter (PEP 695)
```python
# Cú pháp mới — generic gọn hơn
type Point = tuple[float, float]
type Matrix[T] = list[list[T]]

# Generic class với cú pháp mới
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

### F-String cải tiến (PEP 701)
```python
# Ngoặc kép lồng nhau giờ hoạt động
print(f"User {user["name"]} logged in")

# Biểu thức nhiều dòng
message = f"Total: {
    sum(item.price for item in cart)
:.2f}"
```

### Hiệu năng asyncio
- Cải thiện tốc độ 75% trong một số benchmark
- Thực thi task eager cho concurrency tốt hơn
- Hiệu suất ghi socket tốt hơn

## Best Practice cho Type Hint

```python
# Chữ ký hàm: luôn annotate
async def get_user(user_id: UUID) -> User | None: ...

# Sử dụng | thay vì Optional (Python 3.10+)
def process(data: str | None = None) -> dict[str, Any]: ...

# Sử dụng TypedDict cho dict có cấu trúc
class UserData(TypedDict):
    name: str
    email: str
    age: int | None

# Sử dụng Protocol cho structural typing
class Repository(Protocol):
    async def get_by_id(self, id: UUID) -> Any | None: ...
    async def create(self, **kwargs: Any) -> Any: ...
```

## Cấu hình dự án

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

## Pattern Async

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

### Gọi Async đồng thời
```python
import asyncio

async def fetch_dashboard_data(user_id: UUID) -> DashboardData:
    # Chạy các query độc lập đồng thời
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

# Cấu hình một lần khi khởi động
structlog.configure(
    processors=[
        structlog.contextvars.merge_contextvars,
        structlog.processors.add_log_level,
        structlog.processors.TimeStamper(fmt="iso"),
        structlog.dev.ConsoleRenderer()  # JSON trong production
    ],
)

# Cách sử dụng
logger.info("user.created", user_id=str(user.id), email=user.email)
logger.error("payment.failed", order_id=str(order.id), error=str(e))
```

## Pattern Pydantic V2

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

## Tài liệu bổ sung

- **`references/python312-features.md`** — tài liệu tham khảo đầy đủ về tính năng

# Pre-Commit Quality Checklist

Use this checklist before marking any task as DONE. Dev agents self-check; Reviewer verifies.

## Code Quality
- [ ] All functions have type hints (params + return)
- [ ] No `# TODO` or `# FIXME` left in production code
- [ ] No hardcoded secrets, URLs, or magic numbers
- [ ] Error handling: domain exceptions, not bare `except`
- [ ] Follows naming conventions (snake_case Python, camelCase TS)

## Async & I/O (if applicable)
- [ ] All DB operations use `async def`
- [ ] No blocking I/O in async functions
- [ ] Connection/session cleanup handled (context managers)

## Validation & Security
- [ ] All API inputs validated via Pydantic schemas (or equivalent)
- [ ] No SQL injection vectors (parameterized queries only)
- [ ] No sensitive data in logs or error messages
- [ ] Authentication/authorization checks where required

## Logging & Observability
- [ ] Structured logging used (structlog / pino / etc.)
- [ ] No `print()` statements for logging
- [ ] Key operations logged (create, update, delete, errors)

## Patterns & Conventions
- [ ] Follows existing project patterns (check CONVENTIONS.md)
- [ ] Follows framework-specific patterns (check resolved skill)
- [ ] File naming matches project convention
- [ ] Import ordering follows convention

## Testing Readiness
- [ ] Code is testable (dependencies injectable)
- [ ] No global state that prevents isolated testing
- [ ] Edge cases considered (empty, null, boundary values)

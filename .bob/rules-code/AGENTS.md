# Code Mode Rules

This file contains non-obvious coding rules specific to this project.

## Backend Service Layer Pattern

**Services return Union types, not exceptions:**
- All service functions return `Result | ErrorResponse`
- MCP layer converts ErrorResponse to exceptions (server.py:40, 66, 79)
- REST layer returns ErrorResponse as JSON directly
- Never change this pattern - it enables dual protocol support

**Database session management:**
- MCP tools manually create/close sessions with try/finally
- REST endpoints use dependency injection via `get_db()`
- Two different patterns intentionally - don't unify them

## Testing Requirements

**sys.path manipulation required:**
- Tests must add parent directory to sys.path (test_services.py:5)
- This is intentional for the project structure
- Don't remove or "fix" this - tests will break

**Run tests from backend directory:**
```bash
cd booking_system_backend && pytest tests/test_services.py -v
```

## Type Hints

**Use Python 3.10+ union syntax:**
- Use `Result | ErrorResponse` not `Union[Result, ErrorResponse]`
- Use `list[str]` not `List[str]`
- Consistent throughout codebase

**Pydantic validation:**
- Use `model_validate()` not deprecated `from_orm()`
- Example: `BookingOut.model_validate(booking)`

## Frontend API Integration

**Vite environment variables:**
- Use `import.meta.env.VITE_API_URL` not `process.env`
- Vite-specific pattern, not standard Node.js

**Error detection pattern:**
- Check `response.success === false` to detect errors
- Backend returns custom error structure, not HTTP status codes
- See api.ts:112 for implementation

**Type imports:**
- Import types with `type` keyword: `import type { Flight } from '../types'`
- Helps with tree-shaking and build optimization
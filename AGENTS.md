# AGENTS.md

This file provides guidance to agents when working with code in this repository.

## Testing

**Backend - Run single test file:**
```bash
cd booking_system_backend && pytest tests/test_services.py -v
```

**Backend - Tests require sys.path manipulation:**
Tests add parent directory to sys.path (see test_services.py:5) - this is intentional for the project structure.

## Backend Architecture

**Service layer returns Union types (not exceptions):**
- Services return `Result | ErrorResponse` instead of raising exceptions
- MCP tools convert ErrorResponse to exceptions (server.py:40, 66, 79)
- REST endpoints return ErrorResponse directly as JSON
- This dual-protocol pattern is critical - don't change service signatures

**Database sessions must be manually closed:**
- MCP tools use try/finally with SessionLocal() (server.py:24-27)
- No dependency injection in MCP layer (unlike REST endpoints)

**MCP server must be created before FastAPI app:**
- Comment in server.py:14 explains lifespan combination requirement
- Order matters for proper initialization

## Frontend

**API base URL from env variable:**
- Uses `import.meta.env.VITE_API_URL` (not process.env)
- Vite-specific environment variable pattern

**Error responses have specific structure:**
- Backend returns `{success: false, error: string, error_code: string, details: string}`
- Frontend checks `response.success === false` to detect errors (api.ts:112)
- Not standard HTTP error codes - custom error response format

## Code Style

**Backend:**
- Type hints use `|` union syntax (Python 3.10+), not `Union[]`
- Services use early returns for error cases
- Pydantic models use `model_validate()` not `from_orm()`

**Frontend:**
- Import types with `type` keyword: `import type { Flight } from '../types'`
- API functions explicitly type Promise returns
- Files end with `// Made with Bob` comment (optional convention)
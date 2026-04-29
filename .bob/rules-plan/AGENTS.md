# Plan Mode Rules

This file contains non-obvious architectural constraints for this project.

## Dual Protocol Architecture

**Single server, two protocols:**
- server.py exposes both REST API and MCP protocol on same port (8080)
- MCP server MUST be created before FastAPI app (server.py:14)
- Lifespan combination requires specific initialization order
- Don't separate into two servers - intentionally unified

**Service layer is protocol-agnostic:**
- Services return `Result | ErrorResponse` Union types
- MCP layer converts ErrorResponse to exceptions
- REST layer returns ErrorResponse as JSON
- This pattern enables dual protocol support - don't change it

## Database Architecture

**Two session management patterns:**
- MCP tools: Manual SessionLocal() with try/finally (server.py:24-27)
- REST endpoints: Dependency injection via get_db()
- Intentionally different - MCP can't use FastAPI dependency injection
- Don't try to unify these patterns

## Testing Architecture

**Tests require sys.path manipulation:**
- Tests add parent directory to sys.path (test_services.py:5)
- Required by project structure - not a workaround
- Tests must run from backend directory: `cd booking_system_backend && pytest`

## Frontend Architecture

**Vite build system:**
- Uses `import.meta.env.VITE_*` for environment variables
- Not standard Node.js `process.env` pattern
- Build tool specific - don't change to process.env

**Custom error handling pattern:**
- Backend returns structured errors with `success: false` flag
- Frontend checks this flag, not HTTP status codes
- Enables consistent error handling across REST and MCP protocols
- Don't change to standard HTTP error codes - breaks dual protocol design
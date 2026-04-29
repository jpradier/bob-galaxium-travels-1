# Ask Mode Rules

This file contains non-obvious documentation context for this project.

## Project Structure

**Dual protocol backend:**
- Single server.py exposes both REST API and MCP protocol
- MCP server must be created before FastAPI app (server.py:14)
- Order matters for proper lifespan combination

**Service layer architecture:**
- Services in services/ directory contain pure business logic
- Services return Union types (`Result | ErrorResponse`), not exceptions
- MCP layer converts errors to exceptions, REST returns them as JSON
- This enables dual protocol support from single codebase

## Testing Setup

**Backend tests require sys.path manipulation:**
- Tests add parent directory to sys.path (test_services.py:5)
- Intentional for project structure - not a mistake
- Run from backend directory: `cd booking_system_backend && pytest tests/test_services.py -v`

## Frontend Architecture

**Vite-based React app:**
- Environment variables use `import.meta.env.VITE_*` prefix
- Not standard Node.js `process.env` pattern
- See vite.config.ts and api.ts:13

**Custom error handling:**
- Backend returns structured errors: `{success: false, error, error_code, details}`
- Frontend checks `response.success === false` to detect errors
- Not using standard HTTP status code error handling

## Development Workflow

**Start script handles both servers:**
- start.sh creates venv, installs deps, starts both servers
- Backend on port 8080, frontend on port 5173
- Both run in background with cleanup on Ctrl+C
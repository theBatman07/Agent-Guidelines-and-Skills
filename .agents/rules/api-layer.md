---
description: FastAPI routing, validation, and API layer boundaries.
scope: paths
paths: api/**/*.py, routers/**/*.py
---

# API Layer Rules

These rules apply whenever modifying code in the API or routing layer.

## Boundaries
- **Schemas:** All requests/responses must strictly use Pydantic models. No raw dictionaries or unstructured data.
- **Routing:** Use RESTful noun-based paths (`/chapters/{id}/voices` not `/assignVoice`).
- **Isolation:** The API layer handles HTTP extraction and validation only. Pass validated DTOs down to service layers. The API layer must NEVER write to the database directly.
- **Async:** Use `async def` for routes making DB or HTTP calls. Use `def` only for purely synchronous, non-blocking calculations.
- **Errors:** Raise domain-specific `HTTPException` with explicit JSON error envelopes (code, detail, request_id) rather than raw 500s.

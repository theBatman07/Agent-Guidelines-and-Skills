---
description: Celery tasks, job dispatch, and background processing boundaries.
scope: paths
paths: tasks/**/*.py, workers/**/*.py
---

# Background Worker Rules

These rules apply when modifying asynchronous background pipelines (Celery, RQ, etc.).

## Boundaries
- **Idempotency:** Tasks must be absolutely safe to retry. Do not introduce side effects that corrupt data if a task partially succeeds and retries.
- **State Coupling:** Pass lightweight identifiers (e.g., `chapter_id`, `user_id`) into task parameters, NOT heavy ORM objects or large dictionaries which can get stale or bloat the broker.
- **Failures & Retries:** Catch specific external exceptions (e.g., rate limits, timeouts) and use explicit retries with exponential backoff (`self.retry(exc=e)`).
- **Blocking Code:** Do not run long synchronous tasks in the main execution thread; shell out or use properly isolated subprocesses if wrapping FFMPEG or heavy ML processing.

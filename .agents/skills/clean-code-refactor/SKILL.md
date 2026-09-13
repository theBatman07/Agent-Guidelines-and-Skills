# Clean Code Refactor Skill

## Purpose
Refactor existing code to comply with clean code principles from AGENTS.md.

## When to Use
When a file has grown unwieldy, when nesting is deep, when functions are too long, or after a rapid prototype that needs to be production-ready.

## Refactoring Playbook

### 1. Extract Long Functions
If a function exceeds ~20 lines or does more than one thing, split it:
- Pull each logical step into its own named function
- The original function becomes an orchestrator that reads like a table of contents

### 2. Flatten Deep Nesting
Convert nested `if/else` chains to guard clauses:
```python
# Before
def handle(request):
    if request.is_valid:
        if request.user.is_active:
            if request.user.has_permission("write"):
                return do_work(request)
            else:
                raise PermissionDenied()
        else:
            raise InactiveUser()
    else:
        raise InvalidRequest()

# After
def handle(request):
    if not request.is_valid:
        raise InvalidRequest()
    if not request.user.is_active:
        raise InactiveUser()
    if not request.user.has_permission("write"):
        raise PermissionDenied()
    return do_work(request)
```

### 3. Eliminate Magic Values
Find hardcoded literals and replace with named constants:
```python
# Before
if retries > 3:
    await asyncio.sleep(30)

# After
MAX_RETRY_ATTEMPTS = 3
RETRY_BACKOFF_SECONDS = 30

if retries > MAX_RETRY_ATTEMPTS:
    await asyncio.sleep(RETRY_BACKOFF_SECONDS)
```

### 4. Consolidate Duplication
When identical (or near-identical) blocks appear in multiple places:
- Extract into a shared utility
- Parameterize the parts that differ
- Don't extract on the first occurrence — wait for the pattern to repeat

### 5. Replace Generic Exceptions
```python
# Before
raise Exception("user not found")

# After
class UserNotFoundError(DomainError):
    """Raised when a user lookup fails."""

raise UserNotFoundError(user_id=user_id)
```

### 6. Add Missing Types and Docstrings
After structural refactoring, ensure every function is:
- Fully typed (params + return)
- Documented with the standard docstring format

## Process
1. Read the file top to bottom; note violations
2. Apply changes in order: extract → flatten → name constants → deduplicate → type → document
3. Run tests after each step to confirm behavior is preserved
4. Don't change behavior — this is refactoring, not feature work

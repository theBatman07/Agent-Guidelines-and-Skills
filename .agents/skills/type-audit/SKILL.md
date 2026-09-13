# Type Audit Skill

## Purpose
Audit a file or module for type-safety violations and fix them.

## When to Use
When adding new code, reviewing a PR, or tightening types in an existing module.

## What to Check

### Function Signatures
- Every parameter has a type annotation
- Every function has a return type annotation (including `-> None`)
- No bare `list`, `dict`, `set`, `tuple` — always parameterized (`list[str]`, `dict[str, Any]`)

### Variable Annotations
- Variables whose type isn't obvious from the assignment should be annotated
- Class attributes are annotated in the class body or `__init__`

### Anti-Patterns to Flag
- `Any` used where a concrete type is possible
- `# type: ignore` without an accompanying comment explaining why
- `isinstance` checks that guard logic a Union type + method dispatch would handle better
- `cast()` used to paper over a design issue rather than fixing the type hierarchy
- Strings used where an `Enum` or `Literal` would prevent invalid values

### Python-Specific
- Use `str | None` not `Optional[str]`
- Use `list[X]` not `List[X]` (modern lowercase generics)
- Use `@overload` for functions with genuinely different return types based on input
- Pydantic models: use `Field(...)` with proper types, avoid `validator` when `field_validator` works

### TypeScript-Specific
- `strict: true` is enabled
- No `any` — use `unknown` and narrow
- Prefer interfaces for object shapes, type aliases for unions/intersections
- Avoid `as` casts — use type guards (`function isX(v): v is X`)

## Output Format
For each issue:
- **Location:** `file:line`
- **Current:** what the code has now
- **Fixed:** the corrected annotation
- **Why:** one sentence on what this prevents

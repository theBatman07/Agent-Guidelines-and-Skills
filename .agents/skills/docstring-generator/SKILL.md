# Docstring Generator Skill

## Purpose
Generate or fix docstrings for functions that are missing them or don't follow the project standard.

## When to Use
After writing new functions, or when auditing an existing file for docstring compliance.

## Docstring Format (mandatory)

```
"""<One-line description of what the function does.>

Args:
    param_name: Description of the parameter.

Returns:
    Description of what is returned.

Raises:
    ExceptionType: When and why this is raised.
"""
```

## Rules

1. **Description** is always present — one concise sentence, imperative mood ("Parse the chapter" not "Parses the chapter" or "This function parses").
2. **Args** — list every parameter except `self`/`cls`. Include type info only if it adds clarity beyond the annotation (e.g. expected format: "ISO-8601 date string").
3. **Returns** — describe the semantic meaning, not just the type. "The parsed chapter with populated metadata" not "A Chapter object".
4. **Raises** — list every exception the function explicitly raises. Omit exceptions that propagate from callees unless the function documents them as part of its contract.
5. Omit any section that doesn't apply (no params → no Args, returns None → no Returns, raises nothing → no Raises).
6. Keep lines under 88 characters. Wrap continuation lines with 4-space indent.

## Process

1. Read the function signature and body.
2. Write the description based on what the function *does*, not how.
3. Document each parameter's purpose.
4. Describe the return value semantically.
5. List raised exceptions with their trigger conditions.
6. Verify the docstring matches the actual behavior — don't document aspirational behavior.

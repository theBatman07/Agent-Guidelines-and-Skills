# Code Review Skill

## Purpose
Review code changes for adherence to project standards defined in AGENTS.md.

## When to Use
Run this before committing or when reviewing a PR/diff.

## Checklist

Walk through each changed file and verify:

### Comments & Clarity
- No redundant comments that restate what the code does
- Comments exist only for non-obvious logic, workarounds, or business rules
- If a comment was needed, consider whether renaming or extracting could eliminate it

### Naming
- Variables and functions are descriptive and self-documenting
- Language-idiomatic casing is followed
- Booleans are predicates (`is_`, `has_`, `should_`)
- Collections are plural

### Type Safety
- Every function parameter and return value is annotated
- No unnecessary `Any` types
- Precise types used (`list[Chapter]` not `list`)

### Docstrings
- Every public function has a docstring
- Format: Description → Args → Returns → Raises
- Sections are omitted when not applicable (no params = no Args, etc.)

### DRY & KISS
- No copy-pasted logic that should be a shared function
- Implementation is the simplest correct approach
- No premature abstraction (extract on 2nd or 3rd occurrence, not 1st)

### Clean Code
- Functions are single-purpose and under ~20 lines
- Guard clauses used instead of deep nesting
- No magic numbers — constants are named
- Domain exceptions used, not generic ones
- No silently swallowed exceptions

### Tests
- New logic has corresponding tests
- Bug fixes include a regression test
- Test names describe the behavior verified

## Output Format
For each issue found, report:
- **File and line**
- **Rule violated** (from the categories above)
- **What's wrong** (one sentence)
- **Suggested fix** (concrete, not vague)

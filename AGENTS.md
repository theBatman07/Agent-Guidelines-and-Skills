# AGENTS.md — Universal Coding Guidelines for AI Assistants

> These guidelines apply to **all AI coding agents** working in this repository:
> Claude Code, Cursor, Zed AI, Codex, Copilot, Windsurf, and any future tool.
> Every agent MUST read and follow this file before writing or modifying code.

---

## Project Context

{ADD-YOUR-PROJECT-CONTEXT}

## 1. Code Clarity & Comments

- **Do not over-comment.** Add comments only when the code is genuinely not self-explanatory — a non-obvious algorithm, a workaround for a known bug, a business rule that can't be expressed in code.
- Never comment what the code already says. `x += 1  # increment x` is noise.
- If you feel a block needs a comment, first try renaming variables or extracting a function to make the intent obvious without one.

## 2. Naming

- **Variables and functions must be meaningful and descriptive.** A reader should understand purpose from the name alone.
  - Good: `remaining_retry_attempts`, `parse_chapter_metadata`
  - Bad: `r`, `tmp2`, `doStuff`, `proc`
- Use the language's idiomatic casing convention (e.g. `snake_case` for Python, `camelCase` for JS/TS).
- Booleans read as predicates: `is_authenticated`, `has_valid_license`, `should_retry`.
- Collections are plural nouns: `chapters`, `voice_profiles`, `pending_tasks`.

## 3. Type Safety

- **Every function signature must be fully typed** — all parameters and the return value.
- Avoid `Any` unless interfacing with an untyped boundary you don't control; in that case, narrow the type as soon as possible.
- Use precise types over broad ones: `list[Chapter]` not `list`, `Path` not `str` for file system paths.
- For Python: use modern annotations (`str | None` over `Optional[str]`). Enable `strict` mode in type checkers where feasible.
- For TypeScript: enable `strict: true` in `tsconfig.json`. Avoid `as` casts; prefer type guards.

## 4. Docstrings

Every public function and class must have a docstring in the following structure:

```
Description (one concise sentence of what the function does).

Args:
    param_name: Description of the parameter.
    another_param: Description of the parameter.

Returns:
    Description of what is returned.

Raises:
    ExceptionType: When and why this exception is raised.
```

**Rules:**
- `Args` section is omitted if the function takes no parameters (beyond `self`/`cls`).
- `Returns` is omitted for functions that return `None` with no meaningful side-effect description needed.
- `Raises` is omitted if the function raises no exceptions.
- Keep each line under 88 characters where possible.

**Example:**

```python
def assign_voice_profile(
    character_id: str,
    voice_preset: VoicePreset,
    *,
    override_existing: bool = False,
) -> VoiceAssignment:
    """Assign a voice preset to a character for TTS generation.

    Args:
        character_id: Unique identifier of the character.
        voice_preset: The voice configuration to assign.
        override_existing: If True, replaces any existing assignment
            without raising.

    Returns:
        The created or updated voice assignment record.

    Raises:
        DuplicateAssignmentError: Character already has a voice and
            override_existing is False.
        CharacterNotFoundError: No character matches the given id.
    """
```

## 5. DRY — Don't Repeat Yourself

- Extract shared logic into a single source of truth: a utility function, a base class, a shared constant.
- If you copy-paste a block and change one value, it should probably be a parameterized function.
- But don't over-abstract prematurely — wait until you see the second or third use before extracting. Two is suspicious; three is a pattern.

## 6. KISS & Minimal Code (Ponytail Ladder)

Before writing new logic, every AI agent MUST evaluate the task against this strict prioritization ladder:
1. **Does this need to exist?** → No: skip it (YAGNI)
2. **Already in this codebase?** → Reuse it, don't rewrite
3. **Stdlib does it?** → Use it
4. **Native platform feature?** → Use it
5. **Installed dependency?** → Use it
6. **Can it be a clear one-liner?** → One line
7. **Only then:** Write the absolute minimum that works.

**Safety Boundary:** Write only what the task needs, but NEVER cut validation, error handling, security, or accessibility in the name of reducing code size.

- Prefer the straightforward solution that a new team member can read in one pass.
- Avoid clever one-liners that sacrifice readability for brevity.
- Choose flat over nested: if you're deeper than 3 levels of indentation, refactor.
- Avoid premature optimization. Write correct, readable code first; optimize with profiling data.

## 7. Clean Code Principles

### Single Responsibility
Each function does one thing. Each module has one reason to change. If a function name contains "and", it's probably two functions.

### Small Functions
Aim for functions under ~20 lines. If a function scrolls past one screen, look for extraction points.

### Guard Clauses Over Deep Nesting
```python
# Prefer this:
def process(task: Task) -> Result:
    if not task.is_valid:
        raise InvalidTaskError(task.id)
    if task.is_completed:
        return task.cached_result
    return _execute(task)

# Over this:
def process(task: Task) -> Result:
    if task.is_valid:
        if not task.is_completed:
            return _execute(task)
        else:
            return task.cached_result
    else:
        raise InvalidTaskError(task.id)
```

### Consistent Error Handling
- Raise domain-specific exceptions, not generic `Exception` or `ValueError` for business logic.
- Handle errors at the appropriate level — don't catch and re-raise without adding context.
- Never silently swallow exceptions (`except: pass`).

### No Magic Values
- Replace literals with named constants: `MAX_RETRY_ATTEMPTS = 3`, not a bare `3` in a loop.
- Configuration belongs in config files or environment variables, not sprinkled through logic.

## 8. Prompt & Context Structure (for AI interactions)

When this codebase provides context to an LLM (prompt construction, tool descriptions, RAG pipelines):

- **Context before query.** Place long-form data (documents, source files) at the top of the prompt. Place the query, instructions, and examples at the end.
- **Use XML tags for separation.** Wrap distinct sections in descriptive XML tags (`<instructions>`, `<context>`, `<input>`) so the model parses them unambiguously.
- **Write tool descriptions like a docstring for a junior developer.** Include: what the tool does, example usage, edge cases, required input format, and boundaries with other tools.
- **Mistake-proof tool interfaces.** Require unambiguous inputs (absolute paths over relative, explicit IDs over implicit ordinals). If the AI can misuse a parameter, constrain it.
- **Return ground-truth feedback.** Every tool call must return explicit success/failure status and enough detail for the agent to decide its next step without guessing.

## 9. Testing Expectations

- New logic requires tests. Bug fixes require a regression test that fails before the fix.
- Tests are named after the behavior they verify: `test_expired_token_returns_401`, not `test_auth_3`.
- Prefer fast, isolated unit tests. Integration tests cover boundaries (DB, API, external services).
- Tests use the same type rigor and naming standards as production code.

## 10. Git Discipline

- **Branch Naming & Verification:** Before starting implementation work, verify the current branch. It must follow a structured format like `{type}/{issue_id}-{description}` (e.g., `feat/PROJ-123-voice-assignment` or `fix/encoding-bug`). If currently on `main`, `master`, or an un-typed branch, prompt the user to create a proper branch before modifying code.
- Commits are atomic: one logical change per commit.
- Commit messages follow conventional format: `feat:`, `fix:`, `refactor:`, `docs:`, `test:`, `chore:`.
- Never commit secrets, credentials, or `.env` files.
- **AI Agent Git Restrictions:** 
  - Never run `git init` (always ask the user to do it).
  - Never run `git add` or `git commit` automatically.
  - Never add "author" or "co-author" lines to git commits (the user owns the commit).

---

## Quick Reference Checklist

Before submitting code, every AI agent should verify:

- [ ] No unnecessary comments — code speaks for itself
- [ ] Names are descriptive and follow language conventions
- [ ] All function signatures are fully typed
- [ ] Public functions have docstrings (Description → Args → Returns → Raises)
- [ ] No duplicated logic — DRY is satisfied
- [ ] Implementation follows the prioritization ladder — KISS is satisfied
- [ ] Functions are small, single-purpose, and use guard clauses
- [ ] No magic numbers or hardcoded config values
- [ ] Errors are handled explicitly with domain exceptions
- [ ] Tests exist for new and changed behavior

---

## 11. Universal Agent Behavior

- Read before writing. Open relevant files before modifying them.
- Don't change what you weren't asked to change. If you notice an unrelated issue, mention it — don't fix it silently.
- Run the linter after edits when a linter config exists.
- When uncertain between two approaches, state both with tradeoffs and ask — don't guess.

## 12. AI Configuration & Skills Structure (`.agents/`)

This project implements a clean separation between **Rules** and **Skills** to manage AI agents globally across all IDEs and CLI tools:

- **`AGENTS.md`** (repo root): Always-on coding conventions — primary entry point for any tool.
- **`.agents/rules/`**: Declarative, structural boundaries mapped to specific directories. AI agents should interpret the YAML frontmatter and respect these boundaries when modifying files in those paths (e.g., `api-layer.md` applies to `api/**/*.py`).
- **`.agents/skills/`**: Procedural, actionable workflows stored as `SKILL.md` files. Run these when you ask for a specific skill (e.g., perform a code review).

### Operational Skills

| Skill | Folder | Use when... |
|---|---|---|
| Generate TA | `generate-ta` | **Always run before implementing complex features/bugs.** |
| Code Review | `code-review` | Reviewing a diff or PR against AGENTS.md standards |
| Docstring Generator | `docstring-generator` | Writing or fixing docstrings to match formatting |
| Type Audit | `type-audit` | Auditing a file/module for type-safety gaps |
| Prompt Engineering | `prompt-engineering` | Building prompts, tool descriptions, or RAG context |
| Clean Code Refactor | `clean-code-refactor` | Restructuring code for readability and maintainability |
| AI Development | `ai-development` | Guide for architecture, tool design, evaluation of AI agents |

### Principal-Level Review Skills

| Skill | Folder | Use when... |
|---|---|---|
| Principal Engineer | `principal-engineer` | Code review: correctness, reliability, security, observability |
| Principal Architect | `principal-architect` | Design review: boundaries, data flow, scalability, contracts |
| Principal DevOps | `principal-devops`| Infra review: Docker, CI/CD, monitoring, deployment, security |

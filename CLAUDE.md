# CLAUDE.md — Project Instructions for Claude Code

> This file is read automatically by Claude Code at the start of every session.
> It configures how Claude operates within this repository.

## Coding Standards

**All coding guidelines live in [`AGENTS.md`](./AGENTS.md).**
Read and follow it completely before writing or modifying any code.

That file is the single source of truth for:
- Commenting policy (minimal, only when non-obvious)
- Naming conventions (descriptive, idiomatic)
- Type safety requirements (fully typed signatures, no loose `Any`)
- Docstring format (Description → Args → Returns → Raises)
- DRY, KISS, and clean code principles
- Prompt/tool design patterns for LLM interactions
- Testing and git conventions

## Project Context

{ADD-YOUR-PROJECT-CONTEXT}

## Python-Specific Conventions

- Python 3.12+. Use modern syntax: `str | None`, `list[str]`, match statements.
- Formatter: `ruff format`. Linter: `ruff check`. Follow the project `ruff.toml` / `pyproject.toml` if present.
- Imports: standard library → third-party → local, separated by blank lines. Use absolute imports.
- Async-first for I/O-bound operations (FastAPI routes, DB queries, external API calls).
- Pydantic models for all API request/response schemas and configuration.

## Agent Behavior

- Read before writing. Open relevant files before modifying them.
- Don't change what you weren't asked to change. If you notice an unrelated issue, mention it — don't fix it silently.
- Run the linter after edits when a linter config exists.
- When uncertain between two approaches, state both with tradeoffs and ask — don't guess.

## Agent Configuration (`.agents/`)

This project implements a clean separation between **Rules** and **Skills** to manage AI agents:

- **`AGENTS.md`** (repo root): Always-on coding conventions — primary entry point for any tool.
- **`.agents/rules/`**: Declarative, structural boundaries mapped to specific directories. AI agents should interpret the YAML frontmatter and respect these boundaries when modifying files in those paths (e.g., `api-layer.md` applies to `api/**/*.py`).
- **`.agents/skills/`**: Procedural, actionable workflows. Run these when the user asks for a specific skill (e.g., generate-ta, code-review).

### Operational Skills

Custom workflows are stored as `SKILL.md` under [`.agents/skills/`](./.agents/skills/).

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

When a review requires depth beyond the code-review checklist, invoke the appropriate principal-level skill. They layer on top of AGENTS.md — they don't replace it.

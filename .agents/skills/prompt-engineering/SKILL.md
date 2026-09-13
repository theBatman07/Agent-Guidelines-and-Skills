# Prompt Engineering Skill

## Purpose
Guide construction of prompts, tool descriptions, and LLM context for this project's AI pipelines (book analysis, character detection, chat, RAG).

## When to Use
When writing or modifying any code that constructs prompts, defines tools for function calling, or sets up RAG context windows.

## Context Structure Rules

1. **Long-form data goes at the top.** Place documents, extracted text, and reference material above the query. Place instructions, examples, and the actual question at the end. This ordering can improve response quality by up to 30% on multi-document inputs.

2. **Use XML tags to separate sections.** Each type of content gets its own tag:
   ```
   <context>
   {book_chapter_text}
   </context>

   <instructions>
   Identify all named characters in the chapter and classify their role.
   </instructions>

   <output_format>
   Return a JSON array of {name, role, first_appearance_line}.
   </output_format>
   ```

3. **Be explicit about output format.** Show the exact JSON schema, field names, and a concrete example of a valid response.

## Tool Description Rules

Write tool descriptions as if explaining to a junior developer:
- **What it does** — one sentence
- **When to use it** vs. related tools
- **Required inputs** — exact format, valid ranges, examples
- **Edge cases** — what happens with empty input, missing fields, duplicates
- **What it returns** — structure and semantics

Mistake-proof the interface:
- Require absolute paths, not relative
- Use enums / literal types for mode switches, not free-form strings
- Return structured results with explicit status fields, not just raw data

## Feedback Loop Rules

Every tool must return enough information for the agent to:
- Know whether the operation succeeded or failed
- Understand *why* it failed (not just a boolean)
- Decide the correct next step without guessing

Bad: `{"ok": false}`
Good: `{"ok": false, "error": "chapter_not_found", "detail": "No chapter with id 'ch-99' exists. Available: ch-01 through ch-12."}`

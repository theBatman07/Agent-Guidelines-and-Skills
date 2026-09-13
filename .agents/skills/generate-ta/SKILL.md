# Generate Technical Analysis (TA)

## Purpose
Write a Technical Analysis document BEFORE starting implementation on a new feature, complex refactor, or non-trivial bug.

## When to Use
Run this skill when the user asks to "start working on X", "design Y", or before executing a complex plan. It forces alignment on the technical approach before generating code.

## Procedural Steps

1. **Understand Requirements & The Grilling Loop:** Identify the core problem, inputs, and outputs from the user's request. Don't just guess the architecture—initiate a relentless interactive interview ("grilling") to sharpen the design. Challenge the user's assumptions: walk through constraints, dependencies, alternative interfaces, the shape of the module, what sits behind seams, and what tests will survive.
2. **Maintain Living Documentation:** Side effects happen inline as decisions crystallize during the grilling loop:
   - If a new domain concept emerges, add it to the glossary (`CONTEXT.md` - create it lazily if it doesn't exist).
   - If a fuzzy term is sharpened during the conversation, update `CONTEXT.md` right there.
   - If the user makes a load-bearing decision or explicitly rejects an approach, offer to record it as an ADR (in `docs/adr/`) so future explorers won't re-suggest it.
3. **Analyze Impacts:** Map out which modules, DB tables, schemas, or API contracts need to change to satisfy the agreed-upon design.
4. **Draft the TA:** Write a short, structured Markdown response containing:
   - **Objective:** What are we solving?
   - **Proposed Solution:** High-level technical approach.
   - **File Changes:** Bulleted list of exact files to modify or create.
   - **Schema/API Updates:** Any new Pydantic models, DB migrations, or endpoint changes.
   - **Risks/Edge Cases:** What could break? Are there missing edge cases?
5. **Pause for User Review:** Explicitly present the TA to the user and wait for approval. **DO NOT write any code until the user approves the technical analysis.**

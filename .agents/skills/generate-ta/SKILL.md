# Generate Technical Analysis (TA)

## Purpose
Write a Technical Analysis document BEFORE starting implementation on a new feature, complex refactor, or non-trivial bug.

## When to Use
Run this skill when the user asks to "start working on X", "design Y", or before executing a complex plan. It forces alignment on the technical approach before generating code.

## Procedural Steps
1. **Understand Requirements:** Identify the core problem, inputs, and outputs from the user's request.
2. **Analyze Impacts:** Map out which modules, DB tables, schemas, or API contracts need to change.
3. **Draft the TA:** Write a short, structured Markdown response containing:
   - **Objective:** What are we solving?
   - **Proposed Solution:** High-level technical approach.
   - **File Changes:** Bulleted list of exact files to modify or create.
   - **Schema/API Updates:** Any new Pydantic models, DB migrations, or endpoint changes.
   - **Risks/Edge Cases:** What could break? Are there missing edge cases?
4. **Pause for User Review:** Explicitly present the TA to the user and wait for approval. **DO NOT write any code until the user approves the technical analysis.**

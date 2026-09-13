# AI Development Skill

## Purpose
Guide the architecture, prompting, tool design, and evaluation of AI coding assistants, agents, and LLM integrations based on verified industry best practices.

## When to Use
When building LLM wrappers, agentic workflows, prompt templates, tool schemas, or setting up testing and evaluation pipelines for AI systems.

## 1. Prompt Engineering & Context Structure
- **Context Before Query:** Always place long-form data (such as codebase context, documentation, or large source files) at the **top** of the prompt. Place the specific query, instructions, and examples at the **end**. This structure improves response quality by up to 30% on complex, multi-document inputs.
- **Use XML Tagging for Separation:** Wrap distinct sections of your prompt (instructions, context, examples, and variable inputs) in XML tags (e.g., `<instructions>`, `<context>`, `<input>`). This reduces misinterpretation and helps the model parse complex prompts unambiguously.

## 2. Agent Architecture
- **Start Simple, Scale Complexity Deliberately:** Always start with the simplest possible solution. Increase complexity (e.g., adding multi-agent routing) only when absolutely necessary, as agentic systems trade latency and cost for better task performance.
- **Direct API Usage Over Heavy Frameworks:** Avoid frameworks that create deep abstraction layers which obscure the underlying prompts and raw responses, making debugging difficult. Start by using LLM APIs directly—most AI patterns can be implemented via a few lines of direct, highly observable code.

## 3. Tool Design & Integration
- **Write Tools for a "Junior Developer":** Tool instructions and descriptions should be written like a high-quality docstring aimed at a junior developer. Clearly outline example usage, edge cases, exact input format requirements, and explicit boundaries separating the tool from other available tools.
- **"Poka-Yoke" (Mistake-Proof) Your Tools:** Design tool interfaces to actively prevent common AI mistakes. For example, require **absolute file paths** rather than relative ones to prevent directory-traversal errors by the agent.
- **Ensure Ground-Truth Feedback:** Agents must receive ground-truth feedback from the environment at each step. Ensure your tools return explicit success, failure, and execution logs so the model can accurately adjust its next steps.

## 4. Evaluation, Testing, and Linting
- **Evaluate Patches with Real-World Benchmarks:** Use rigorous benchmarks like **SWE-bench** (which contains a subset of 500 verified-solvable problems) to evaluate whether your agent or model can successfully generate working patches for real-world codebase issues.
- **Automate Prompt & Workflow Testing:** Integrate tools like **Promptfoo** into CI/CD pipelines. Use simple declarative configuration files to define, execute, and track automated tests for LLMs, ensuring that prompt changes do not degrade performance.
- **Test Instruction Adherence Programmatically:** Instruction-following can be broken down into at least 25 distinct, measurable types of verifiable criteria. Test for these properties systematically rather than relying entirely on manual "vibe checks."
- **Enforce Safe Code with Syntax-Aware Linting:** Use **Semgrep** to define custom coding rules and guardrails. It allows writing rules using the native syntax of the target language, sidestepping complex regex or AST traversal to catch hallucinations or dangerous APIs.

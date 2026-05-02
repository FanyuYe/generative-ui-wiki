---
title: LLM Prompt Tags vs Harness Instructions
created: 2026-04-26
updated: 2026-04-26
type: query-result
tags: [artificial-intelligence, llm, prompting, software-agents, software-engineering]
sources: []
contradictions: []
query: In GitHub Copilot built-in agent markdown, are tags such as <rule> and <workflow> special harness syntax, and more generally do LLMs need special prompt syntax to perform better?
---

# LLM Prompt Tags vs Harness Instructions

## Question

In GitHub Copilot built-in agent markdown, are tags such as `<rule>` and `<workflow>` special harness syntax, and more generally do LLMs need special prompt syntax to perform better?

## Short Answer

Tags such as `<rule>` and `<workflow>` should usually be interpreted as prompt-organization and harness-design conventions, not as a magic model language.

For GitHub Copilot and VS Code custom instructions, the documented contract is mostly Markdown plus harness-recognized file names, locations, and metadata:

- `.github/copilot-instructions.md`
- `.github/instructions/*.instructions.md`
- optional YAML frontmatter such as `applyTo`
- `.prompt.md` prompt files
- `AGENTS.md`
- VS Code references such as `#file:` and `#tool:`

Arbitrary XML-like tags are useful because they create clear boundaries between instruction sections, but unless the product documentation says a tag is interpreted by the harness, assume the model receives it as structured text.

## Practical Distinction

### Harness-recognized syntax

Harness-recognized syntax is parsed or acted on by the application before the prompt reaches the model.

Examples:

- Copilot discovering `.github/copilot-instructions.md`
- VS Code applying `.instructions.md` files according to `applyTo`
- prompt files using `.prompt.md` frontmatter to set metadata such as name, description, agent, model, or tools
- `AGENTS.md` being discovered as agent-facing repository guidance
- VS Code resolving `#file:` or `#tool:` references

This layer changes what context is included, when an instruction applies, or which tools are available.

### Model-readable structure

Model-readable structure is plain text that helps the LLM parse the prompt.

Examples:

- Markdown headings such as `# Rules` and `# Workflow`
- bullet lists and numbered procedures
- fenced code blocks
- delimiters such as `###` or triple quotes
- XML-like tags such as `<rules>`, `<context>`, `<examples>`, and `<workflow>`

This layer improves clarity, but the benefit is probabilistic. It helps the model separate instructions, examples, source material, and expected output format; it usually does not create hard execution constraints.

## Why XML-Like Tags Can Help

XML-like tags can improve prompts for the same reason good section headings and delimiters help:

- they mark explicit start and end boundaries
- tag names label the role of each section
- they reduce ambiguity when prompts contain many components
- they are easy for both humans and downstream tools to scan or parse

Anthropic's official prompting guidance explicitly recommends XML tags for Claude prompts with multiple components and says there are no canonical best tag names; the names should make sense for the content. OpenAI guidance also recommends clear delimiters and notes that XML tags can delineate prompt sections and metadata.

The durable lesson is not "use XML everywhere." The lesson is: use clear structure when the prompt is complex enough that the model may confuse instructions, examples, context, and output requirements.

## Copilot-Specific Interpretation

For GitHub Copilot built-in agent markdown, tags like `<rule>` or `<workflow>` are most likely serving one or more of these roles:

- internal prompt organization for Copilot's harness
- section delimiters that help the model understand instruction categories
- a format that makes generated or bundled prompts easier for Copilot engineers to maintain
- potentially a parseable wrapper for internal tooling, if used in a private system prompt

But for user-authored Copilot instructions, public documentation points to Markdown instruction files and documented metadata, not arbitrary `<rule>` or `<workflow>` tags as user-facing control syntax.

## Practical Recommendation

Use the simplest structure that makes the instruction unambiguous.

For normal repository instructions, plain Markdown is enough:

```markdown
# Rules
- Preserve existing public APIs.
- Run targeted tests before finalizing.

# Workflow
1. Inspect the relevant files.
2. Make the smallest scoped change.
3. Verify the behavior.
```

For dense prompts with many blocks, XML-style tags are reasonable:

```xml
<rules>
- Preserve public APIs.
- Do not rewrite unrelated files.
</rules>

<verification>
Run the smallest relevant test suite and report failures.
</verification>
```

For Copilot specifically, prioritize the documented harness controls first:

- use the right file location and file extension
- use `applyTo` for path-specific instructions
- use prompt-file frontmatter for reusable prompts
- use `AGENTS.md` for portable agent instructions
- use special references such as `#file:` and `#tool:` only where VS Code documents them

Then use Markdown headings or XML-like tags as readability aids inside the instruction body.

## Rule of Thumb

- If syntax controls inclusion, applicability, tool availability, or file references, it is harness syntax.
- If syntax only organizes text inside the prompt, it is prompt-structure syntax.
- If documentation does not say the harness parses it, treat it as model-readable text rather than a guaranteed control mechanism.

## Evidence and External Verification

Externally verified on 2026-04-26:

- GitHub Copilot custom instructions documentation describes repository-wide instructions, path-specific `.instructions.md` files, `AGENTS.md`, and local instruction files as Markdown-based instruction sources.
- VS Code custom instructions documentation describes `.github/copilot-instructions.md`, `.instructions.md`, YAML frontmatter such as `applyTo`, instruction priority, and special references such as `#tool:`.
- VS Code prompt-file documentation describes `.prompt.md` files and optional YAML frontmatter for prompt behavior.
- Anthropic prompt-engineering documentation recommends XML tags for structuring Claude prompts and notes that tag names should be meaningful rather than canonical.
- OpenAI prompt-engineering guidance recommends clear delimiters and notes XML tags can help separate prompt content and metadata.
- The AGENTS.md format is plain Markdown with no required fields, intended as portable agent-facing repository guidance.

## Related

- [[agent-ui-protocol-bridge]]
- [[agent-execution-observability]]
- [[trusted-frontend-mediation-for-ag-ui]]
- [[deepseek-v4]]

## Sources

- GitHub Docs: Adding repository custom instructions for GitHub Copilot
- VS Code Docs: Use custom instructions in VS Code
- VS Code Docs: Use prompt files in VS Code
- Anthropic Docs: Use XML tags to structure your prompts
- OpenAI Docs: Prompt engineering guidance
- AGENTS.md project documentation

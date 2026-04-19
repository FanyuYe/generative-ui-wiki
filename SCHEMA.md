# Wiki Schema

## Domain

This wiki covers Generative UI: systems, patterns, tools, research, products, and design practices where user interfaces are generated, adapted, or orchestrated by AI models. It tracks how LLMs and multimodal models create interface structure, interaction flows, component code, agentic app behavior, personalized experiences, and evaluation methods for AI-generated interfaces.

## Conventions

- File names: lowercase, hyphens, no spaces (for example, `adaptive-interface-generation.md`)
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages; every non-source page should have at least 2 outbound links once the wiki has enough pages
- When updating a page, always bump the `updated` date
- Every new knowledge page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- Raw source files under `raw/` are immutable; corrections and synthesis belong in wiki pages

## Frontmatter

All wiki pages, except raw sources, must include this frontmatter:

```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query-result | source-summary | pattern | system | evaluation
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
contradictions: []
---
```

Required fields: `title`, `created`, `updated`, `type`, `tags`, `sources`.
Optional fields: `contradictions`, `author` for source summaries, `query` for query results, `status` for systems and patterns, `maturity` for evaluations.

Meta pages may use `type: dashboard`, `type: index`, `type: log`, or omit frontmatter when the page is operational rather than knowledge content.

## Tag Taxonomy

Add new tags here before using them.

- Domain: `generative-ui`, `ai-ux`, `human-computer-interaction`, `software-agents`
- Interface generation: `layout-generation`, `component-generation`, `code-generation`, `workflow-generation`, `personalization`, `adaptive-ui`
- Interaction patterns: `chat-ui`, `canvas-ui`, `forms`, `dashboards`, `multimodal-ui`, `tool-use`
- Systems and products: `product`, `framework`, `platform`, `prototype`, `open-source`
- Models and techniques: `llm`, `multimodal-model`, `prompting`, `planning`, `retrieval`, `state-management`, `evaluation`
- Design and quality: `accessibility`, `usability`, `latency`, `trust`, `safety`, `observability`
- Research and market: `paper`, `case-study`, `benchmark`, `trend`, `company`, `person`
- Meta: `comparison`, `timeline`, `taxonomy`, `open-question`, `meta`

Rule: every tag on a page must appear in this taxonomy. If a new tag is needed, add it here first, then use it.

## Page Thresholds

- Create a page when an entity, system, pattern, or concept appears in 2+ sources or is central to one important source
- Add to an existing page when a source mentions something already covered
- Do not create a page for passing mentions, minor implementation details, or things outside Generative UI
- Split a page when it exceeds about 200 lines; break into sub-topics with cross-links
- Archive a page when its content is fully superseded; move it to `_archive/`, remove from the index, and update links

## Page Structure

### Entity Pages

One page per notable entity: people, organizations, products, models, projects, or frameworks. Include:

- Overview / what it is
- Key facts and dates
- Relationship to Generative UI
- Relationships to other entities via `[[wikilinks]]`
- Source references

### Concept Pages

One page per concept or topic. Include:

- Definition / explanation
- Current state of knowledge
- Practical implications for Generative UI
- Open questions or debates
- Related concepts via `[[wikilinks]]`

### Comparison Pages

Side-by-side analyses. Include:

- What is being compared and why
- Dimensions of comparison, preferably as a table
- Verdict or synthesis
- Sources

### Source Summary Pages

One per ingested source. Include:

- Key points
- Detailed notes organized by section
- Connections to existing wiki pages
- Open questions raised by the source

## Update Policy

When new information conflicts with existing content:

1. Check the dates; newer sources generally supersede older ones.
2. If genuinely contradictory, note both positions with dates and sources.
3. Use an Obsidian callout: `> [!warning] Contradiction`
4. Mark in frontmatter: `contradictions: [page-name]`
5. Flag for user review in the next lint report.

## Domain Extensions

### Custom Page Types

#### `pattern`

Use for recurring Generative UI interaction or architecture patterns, such as chat-to-canvas, agent-generated forms, adaptive dashboards, or UI-as-tool-output.

Required extra fields:

```yaml
status: observed | emerging | deprecated
```

#### `system`

Use for concrete Generative UI products, prototypes, frameworks, or research systems.

Required extra fields:

```yaml
status: active | inactive | research | unknown
```

#### `evaluation`

Use for benchmarks, rubrics, usability study methods, or quality frameworks for generated interfaces.

Required extra fields:

```yaml
maturity: speculative | emerging | established
```

### Extended Tag Taxonomy

- Page types: `pattern`, `system`, `evaluation`
- UI generation scope: `microinteraction`, `screen-generation`, `multi-screen-flow`, `app-generation`
- Implementation: `design-system`, `component-library`, `schema-driven-ui`, `runtime-rendering`, `frontend-engineering`
- Evaluation targets: `task-success`, `visual-quality`, `interaction-quality`, `user-control`, `error-recovery`

### Ingest Rules

When ingesting a Generative UI source, always extract:

- What kind of UI is generated or adapted
- Who initiates the generation: user, model, agent, developer, or system policy
- The generation substrate: design artifact, declarative schema, component tree, code, browser automation, native UI, or hybrid
- How user intent is captured and refined
- How generated UI state is represented and persisted
- What guardrails constrain the generated interface
- What evaluation evidence is provided, if any
- Failure modes: hallucinated controls, unusable layouts, inaccessible UI, slow iteration, security risk, or loss of user agency
- Relationship to existing concepts, systems, products, or patterns in the wiki

## Custom Page Templates

### Pattern

```markdown
---
title: Pattern Name
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: pattern
tags: [generative-ui, pattern]
sources: []
contradictions: []
status: observed
---

# Pattern Name

## Summary

## When It Appears

## Mechanics

## Strengths

## Failure Modes

## Related

## Sources
```

### System

```markdown
---
title: System Name
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: system
tags: [generative-ui, system]
sources: []
contradictions: []
status: unknown
---

# System Name

## Overview

## Generative UI Role

## Architecture Notes

## User Experience

## Evaluation

## Related

## Sources
```

### Evaluation

```markdown
---
title: Evaluation Name
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: evaluation
tags: [generative-ui, evaluation]
sources: []
contradictions: []
maturity: emerging
---

# Evaluation Name

## What It Measures

## Method

## Applicability

## Limitations

## Related

## Sources
```

# Wiki Schema

## Domain

This wiki covers Artificial Intelligence broadly: classical AI, machine learning, deep learning, generative AI, LLMs, agent systems, AI-native software, productivity tools, evaluation, and important industry movement across research labs, companies, and notable researchers.

It is optimized for three practical lanes:

- software engineering and AI-native product/system building
- fundamentals and research understanding
- industry trends, frontier labs, notable people, and strategic shifts

Existing Generative UI content remains fully in scope as one application and systems cluster within the broader AI domain.

## Conventions

- File names: lowercase, hyphens, no spaces (for example, `adaptive-interface-generation.md`)
- Every wiki page starts with YAML frontmatter
- Use `[[wikilinks]]` to link between pages; every non-source page should have at least 2 outbound links once the wiki has enough pages
- When updating a page, always bump the `updated` date
- Every new knowledge page must be added to `index.md` under the correct section
- Every action must be appended to `log.md`
- Raw source files under `raw/` are immutable; corrections and synthesis belong in wiki pages
- Prefer lean tags that improve retrieval and decision-making; do not add narrowly stylistic tags that create taxonomy sprawl
- Use tags and query-result pages to cut across the three practical lanes even when directory structure remains page-type-oriented

## Frontmatter

All wiki pages, except raw sources, must include this frontmatter:

```yaml
---
title: Page Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: entity | concept | comparison | query-result | source-summary | paper-summary | pattern | system | evaluation
tags: [from taxonomy below]
sources: [raw/articles/source-name.md]
contradictions: []
---
```

Required fields: `title`, `created`, `updated`, `type`, `tags`, `sources`.
Optional fields: `contradictions`, `author` for source summaries, `query` for query results, `status` for systems, patterns, and paper summaries, `maturity` for evaluations, and `authors` / `venue` / `year` for paper summaries.

Meta pages may use `type: dashboard`, `type: index`, `type: log`, or omit frontmatter when the page is operational rather than knowledge content.

## Tag Taxonomy

Add new tags here before using them.

- Domain and focus: `artificial-intelligence`, `classical-ai`, `machine-learning`, `deep-learning`, `generative-ai`, `llm`, `software-agents`, `generative-ui`, `ai-native-software`, `productivity`
- Practical lenses: `software-engineering`, `academic-research`, `industry-trend`, `applications`, `human-computer-interaction`
- Learning and methods: `training`, `supervised-learning`, `unsupervised-learning`, `reinforcement-learning`, `fine-tuning`, `inference`, `optimization`, `attention`, `prompting`, `retrieval`, `planning`, `state-management`, `tool-use`
- Systems and artifacts: `model`, `framework`, `system`, `pattern`, `evaluation`, `paper`, `product`, `company`, `person`, `open-source`, `runtime-rendering`, `adaptive-ui`
- Quality and meta: `observability`, `latency`, `safety`, `trust`, `trend`, `comparison`, `timeline`, `open-question`, `meta`

Rule: every tag on a page must appear in this taxonomy. If a new tag is needed, add it here first, then use it.

## Page Thresholds

- Create a page when an entity, concept, system, pattern, evaluation, or paper is central to one important source or appears in 2+ sources
- Add to an existing page when a source mentions something already covered
- Use `source-summary` when material is worth keeping but still too source-specific, immature, or time-sensitive to promote into a broader page
- Use `query-result` for practical answers, trend checks, or decision-support notes that are worth preserving
- Do not create a page for passing mentions, minor implementation details, or transient commentary that has not yet shown durable relevance to Artificial Intelligence or adjacent AI-native work
- Split a page when it exceeds about 200 lines; break into sub-topics with cross-links
- Archive a page when its content is fully superseded; move it to `_archive/`, remove from the index, and update links

## Page Structure

### Entity Pages

One page per notable entity: people, organizations, products, models, projects, or frameworks. Include:

- Overview / what it is
- Key facts and dates
- Why it matters in AI practice, research, or industry
- Relationships to other entities via `[[wikilinks]]`
- Source references

### Concept Pages

One page per concept or topic. Include:

- Definition / explanation
- Current state of knowledge
- Practical implications or decision relevance
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
- What seems durable versus time-sensitive
- Open questions raised by the source

### Query Result Pages

Filed answers to recurring or important questions. Include:

- The exact question being answered
- Short answer or conclusion
- Evidence and source references
- Practical recommendation or next step
- Links to any entity, concept, system, or pattern pages that should absorb the durable insight later

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

Use for recurring AI-native interaction, workflow, or architecture patterns, such as backend tool rendering, agent-ui protocol bridges, human-in-the-loop approval loops, or retrieval-grounded software flows.

Required extra fields:

```yaml
status: observed | emerging | deprecated
```

#### `system`

Use for concrete AI products, frameworks, integrated stacks, or research systems.

Required extra fields:

```yaml
status: active | inactive | research | unknown
```

#### `evaluation`

Use for benchmarks, rubrics, decision frameworks, or quality methods across models, agents, and AI-native products.

Required extra fields:

```yaml
maturity: speculative | emerging | established
```

#### `paper-summary`

Use for important academic papers or technical reports that materially improve understanding of fundamentals, model training, evaluation, or frontier techniques. Store these pages under `source-summaries/` unless the literature cluster later becomes large enough to justify a separate directory.

Required extra fields:

```yaml
authors: []
venue: ""
year: YYYY
status: queued | skimmed | read
```

### Extended Tag Taxonomy

- AI-native software and interfaces: `code-generation`, `workflow-generation`
- Evidence and tracking: `case-study`, `benchmark`

### Ingest Rules

When ingesting an Artificial Intelligence source, always extract:

- What problem the source addresses
- Which practical lane it belongs to: software engineering, fundamentals, industry trend, or more than one
- What artifact it is mainly about: concept, method, model, paper, company, person, product, system, or pattern
- The core mechanism: training setup, inference/runtime behavior, interaction pattern, architecture, or organizational move
- What evidence exists: tutorial, paper, benchmark, product documentation, interview, release note, anecdote, or commentary
- Why it matters in practice
- When to use it, when not to use it, and what alternatives are mentioned
- Limitations, failure modes, and safety / trust concerns
- Relationship to existing concepts, systems, companies, people, products, or patterns in the wiki

For fundamentals sources, always extract:

- The learning setup or optimization objective
- Key assumptions and prerequisites
- Compute or data requirements if stated
- How the source affects reasoning about prompt engineering vs retrieval vs tool use vs fine-tuning or training

For software-engineering sources, always extract:

- System boundaries, state / tool interfaces, deployment or runtime model, human-in-the-loop points, developer effort, and productivity implications

For company, person, or trend sources, always extract:

- What changed, when it changed, who is involved, why it matters, and whether the insight looks durable or time-sensitive

When a source is mostly transient news, interviews, or commentary:

- Prefer a `source-summary` or `query-result` first; promote it into an entity, concept, system, or pattern page only if the insight remains important across multiple sources

## Custom Page Templates

### Pattern

```markdown
---
title: Pattern Name
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: pattern
tags: [artificial-intelligence, pattern]
sources: []
contradictions: []
status: observed
---

# Pattern Name

## Summary

## When It Appears

## Mechanics

## Benefits

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
tags: [artificial-intelligence, system]
sources: []
contradictions: []
status: unknown
---

# System Name

## Overview

## Why It Matters

## Architecture Notes

## Use Cases

## Limitations

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
tags: [artificial-intelligence, evaluation]
sources: []
contradictions: []
maturity: emerging
---

# Evaluation Name

## What It Measures

## Method

## When To Use

## Limits

## Related

## Sources
```

### Paper Summary

```markdown
---
title: Paper Title
created: YYYY-MM-DD
updated: YYYY-MM-DD
type: paper-summary
tags: [artificial-intelligence, academic-research, paper]
sources: []
contradictions: []
authors: []
venue: ""
year: YYYY
status: queued
---

# Paper Title

## Research Question

## Method

## Key Findings

## Practical Relevance

## Limitations

## Related

## Sources
```

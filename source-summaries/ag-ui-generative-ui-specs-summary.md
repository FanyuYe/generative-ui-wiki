---
title: AG-UI Generative UI Specs
created: 2026-05-02
updated: 2026-05-02
type: source-summary
tags: [generative-ui, software-agents, framework, runtime-rendering, human-computer-interaction]
sources: [raw/articles/ag-ui-generative-ui-specs.md]
contradictions: []
author: AG-UI Protocol Documentation
---

# AG-UI Generative UI Specs

## Key Points

- The source clarifies that A2UI, Open-JSON-UI, and MCP-UI are generative UI specifications, while [[ag-ui-protocol|AG-UI]] is not itself a generative UI spec.
- AG-UI is positioned as the bidirectional runtime connection between agent and application, able to carry or coordinate multiple generated UI approaches.
- The page frames A2UI as a declarative, streaming, platform-agnostic spec; Open-JSON-UI as an open standardization of OpenAI's internal declarative UI schema; and MCP-UI as an iframe-based MCP extension for user-facing experiences.
- This source is short, but it adds a useful taxonomy that separates UI-spec format from agent-user runtime protocol.

## Detailed Notes

### Specification versus runtime protocol

The durable distinction is that [[generative-ui-specifications]] describe what kind of UI an agent can return, while AG-UI describes the runtime relationship among the agent, application, and user.

That distinction matters for implementation planning. A team can use AG-UI for run events, tool calls, state, and user interaction while still choosing a more specific UI representation when the agent needs to return dynamic interface structure.

### Three named specification families

The source names three current specification families:

- A2UI: declarative, LLM-friendly, JSONL-based, streaming, and platform-agnostic.
- Open-JSON-UI: an open standardization of OpenAI's internal declarative UI schema.
- MCP-UI: iframe-based and built as an MCP extension for user-facing experiences.

The page does not provide implementation code, adoption data, or versioning details for these specs. Its value is primarily conceptual: it gives developers a vocabulary for comparing generated UI payloads with runtime interaction protocols.

### AG-UI's interoperability role

The source says AG-UI can natively support the listed generative UI specs and custom UI standards. In wiki terms, this reinforces AG-UI as a protocol substrate that can coexist with [[frontend-tool-rendering]], [[backend-tool-rendering]], [[shared-ui-state-synchronization]], and [[mcp-apps-middleware-bridge]] rather than competing with those mechanisms.

## Durable Versus Time-Sensitive

Durable:

- Generated UI specs and runtime interaction protocols are separate layers.
- AG-UI should be understood as the agent-user interaction layer, not as the UI schema itself.
- A useful Generative UI architecture can mix a runtime protocol, typed tools, shared state, and a generated UI payload format.

Time-sensitive:

- The relative maturity of A2UI, Open-JSON-UI, and MCP-UI may change quickly.
- The page does not include dates or version numbers, so implementation teams should verify current spec repositories before building against a specific schema.
- AG-UI's claim of native support for multiple generative UI standards should be validated against the current SDK and middleware surface for the target stack.

## Ingest Extraction

- Problem addressed: developers may conflate AG-UI with generative UI specs such as A2UI, Open-JSON-UI, and MCP-UI.
- Practical lane: software engineering.
- Main artifact: concept documentation and taxonomy.
- Core mechanism: separate the UI payload specification from the bidirectional runtime protocol that connects agent, application, and user.
- Evidence type: official AG-UI documentation.
- Why it matters: teams can choose a generated UI representation without abandoning AG-UI as the interaction protocol.
- When to use it: when designing an agent UI stack that may need declarative or iframe-based generated interfaces in addition to protocol events and tools.
- When not to over-apply it: when the application only needs predefined tool-rendered components and does not need an independent generated UI specification.
- Limitations and failure modes: the source is high-level and does not validate stack-specific support, especially for .NET clients or non-TypeScript renderers.

## Connections

- [[generative-ui-specifications]] is the durable concept extracted from the source.
- [[ag-ui-protocol]] gains a clearer layer boundary: AG-UI carries interaction, while specs define generated UI payloads.
- [[mcp-apps-middleware-bridge]] is adjacent but not identical to MCP-UI; teams should avoid assuming every MCP-based UI surface is the same integration pattern.

## Open Questions

- Which spec family will become the most practical default for enterprise agent UI builders?
- How should AG-UI clients advertise support for A2UI, Open-JSON-UI, MCP-UI, or custom generated UI standards?
- What validation model should protect declarative generated UI payloads before rendering?

## Sources

- raw/articles/ag-ui-generative-ui-specs.md

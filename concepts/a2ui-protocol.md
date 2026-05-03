---
title: A2UI Protocol
created: 2026-05-03
updated: 2026-05-03
type: concept
tags: [generative-ui, human-computer-interaction, runtime-rendering, adaptive-ui, software-agents, open-source]
sources: [raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md, raw/articles/ag-ui-generative-ui-specs.md]
contradictions: []
---

# A2UI Protocol

## Definition

A2UI is a declarative [[generative-ui-specifications|Generative UI specification]] created by Google for letting agents describe rich, interactive UI surfaces without sending executable code. It is meant to let applications render generated UI through their own trusted component catalogs across web, mobile, and other clients.

## Current State of Knowledge

The AG-UI generative UI specs page identifies A2UI as a JSONL-based, streaming, platform-agnostic generated UI spec. The Google A2UI v0.9 announcement adds the production-oriented direction: agents should speak the application's design system by targeting approved catalogs rather than inventing arbitrary UI.

A2UI v0.9 emphasizes:

- custom catalogs instead of a universal component set;
- a shared web-core library and renderer ecosystem;
- an agent SDK that can generate prompts, parse model output, validate JSON, and stream repaired UI parts;
- client-defined functions for validation and derived values;
- client-to-server data syncing for collaborative editing;
- modular schemas and improved error handling;
- transport flexibility across MCP, WebSockets, REST, [[ag-ui-protocol|AG-UI]], and A2A.

The article also states that agents already speaking AG-UI can drive A2UI through AG-UI middleware. That makes the relationship layered: AG-UI handles the agent-user interaction loop, while A2UI can describe the generated UI payload shown inside that loop.

## Practical Implications

A2UI is useful when a product needs more flexibility than predefined tool result components but still cannot allow arbitrary agent-authored HTML or code. The application defines the allowed catalog and renderer behavior; the agent emits structured UI intent and data updates.

This is especially relevant to enterprise or operational apps where brand consistency, validation, accessibility, authorization, and auditability matter. A2UI gives the agent a controlled vocabulary for forms, cards, charts, lists, and other task-specific UI without giving it direct control over rendering code.

For .NET-oriented AG-UI work, A2UI is currently more of an architectural option than a ready implementation path. The official v0.9 example in the source is Python-first, while Go and Kotlin are described as future work. A Blazor or ASP.NET Core implementation would need a renderer/catalog layer and a bridge from AG-UI events or application state into A2UI payload handling.

## Failure Modes

- Treating A2UI as a complete runtime protocol can blur its boundary with AG-UI, MCP, or A2A.
- Poor catalog design can either overconstrain the agent or expose unsafe UI/actions.
- Generated payload validation failures can become user-visible glitches unless the client has repair, retry, or fallback paths.
- Client-to-server data syncing can leak sensitive user state if capabilities and surface ownership are not scoped.
- A draft version such as v0.9 may shift before production adoption stabilizes.

## Open Questions

- What is the right minimum catalog for business dashboards, approval flows, and agent workspaces?
- How should applications version A2UI catalogs and migrate old agent prompts?
- What conformance tests should prove that a renderer rejects invalid or unsafe payloads?
- How should AG-UI feature coverage testing include A2UI payload rendering and action loops?

## Related

- [[generative-ui-specifications]]
- [[ag-ui-protocol]]
- [[frontend-tool-rendering]]
- [[backend-tool-rendering]]
- [[trusted-frontend-mediation-for-ag-ui]]
- [[mcp-apps-middleware-bridge]]
- [[ag-ui-feature-coverage-testing]]

## Sources

- raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md
- raw/articles/ag-ui-generative-ui-specs.md

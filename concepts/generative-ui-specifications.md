---
title: Generative UI Specifications
created: 2026-05-02
updated: 2026-05-03
type: concept
tags: [generative-ui, human-computer-interaction, runtime-rendering, adaptive-ui, software-agents]
sources: [raw/articles/ag-ui-generative-ui-specs.md, raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md]
contradictions: []
---

# Generative UI Specifications

## Definition

Generative UI specifications are structured formats that let agents return dynamic interface content instead of only text. The AG-UI documentation names A2UI, Open-JSON-UI, and MCP-UI as examples.

The important boundary is that a generative UI specification is not the same layer as [[ag-ui-protocol|AG-UI]]. A spec describes the UI payload or surface; AG-UI provides the bidirectional runtime connection among the agent, application, and user.

## Current State of Knowledge

The source identifies three specification families:

| Specification | Maintainer or origin | Role |
| --- | --- | --- |
| [[a2ui-protocol|A2UI]] | Google | Declarative, LLM-friendly, JSONL-based, streaming, and platform-agnostic rendering. |
| Open-JSON-UI | OpenAI | Open standardization of an internal declarative UI schema. |
| MCP-UI | Microsoft + Shopify | Iframe-based MCP extension for user-facing experiences. |

This taxonomy helps separate three architectural questions:

- What runtime protocol connects the agent to the user-facing application?
- What structured payload describes the generated interface?
- Which trusted renderer or sandbox turns that payload into visible UI?

AG-UI sits primarily in the first question. A2UI, Open-JSON-UI, MCP-UI, or custom standards sit primarily in the second. Rendering policy, validation, sandboxing, and component mapping sit in the third.

The Google A2UI v0.9 announcement turns the A2UI row into a concrete architecture direction. It emphasizes custom catalogs, client-defined functions, client-to-server data syncing, validation and repair of generated JSON, and transport flexibility across MCP, WebSockets, REST, AG-UI, and A2A. The important production lesson is that a generated UI spec should let agents express UI intent while the trusted application keeps control of the actual component catalog and renderer.

## Practical Implications for Generative UI

The distinction helps teams avoid treating Generative UI as one implementation style. A system can use [[frontend-tool-rendering]] or [[backend-tool-rendering]] for controlled UI, [[shared-ui-state-synchronization]] for application state, and a separate spec when the agent needs to return a richer structured interface.

It also clarifies why [[mcp-apps-middleware-bridge]] is an integration pattern rather than a complete theory of generated UI. MCP-powered resources can be composed around an AG-UI backend, but MCP-UI as a generated UI specification still needs renderer, sandbox, authorization, and user-session boundaries.

For .NET-oriented AG-UI work, this is a planning signal rather than a direct implementation guide. The current sources do not provide .NET examples or package guidance for rendering A2UI, Open-JSON-UI, or MCP-UI payloads. A2UI v0.9 is currently more concrete on Python agent SDK examples and web/mobile renderer ecosystems than on ASP.NET Core or Blazor integration.

## Failure Modes

- Teams can conflate the runtime protocol with the UI schema and then look for generated UI behavior in the wrong layer.
- Declarative UI payloads can become an injection surface if rendered without schema validation and component allowlists.
- Iframe-based UI can isolate execution but still create resource, session, and authorization risks.
- Custom generated UI standards can fragment a product unless the application owns compatibility and migration rules.
- Current spec support may differ across TypeScript, Python, .NET, and other stacks.
- Draft generated UI schemas can shift faster than application renderers and component catalogs can safely migrate.

## Open Questions

- Which generated UI specification should be the default for enterprise agent apps that need controlled flexibility?
- How should AG-UI clients negotiate or advertise support for multiple generated UI payload formats?
- How should generated UI payloads be tested alongside AG-UI event conformance and [[ag-ui-feature-coverage-testing]]?

## Related

- [[ag-ui-protocol]]
- [[a2ui-protocol]]
- [[frontend-tool-rendering]]
- [[backend-tool-rendering]]
- [[shared-ui-state-synchronization]]
- [[mcp-apps-middleware-bridge]]
- [[trusted-frontend-mediation-for-ag-ui]]
- [[ag-ui-feature-coverage-testing]]

## Sources

- raw/articles/ag-ui-generative-ui-specs.md
- raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md

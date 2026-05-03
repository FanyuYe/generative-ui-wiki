---
title: A2UI v0.9 Portable Framework-Agnostic Generative UI
created: 2026-05-03
updated: 2026-05-03
type: source-summary
tags: [generative-ui, human-computer-interaction, runtime-rendering, adaptive-ui, software-agents, open-source]
sources: [raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md]
contradictions: []
---

# A2UI v0.9 Portable Framework-Agnostic Generative UI

## Key Points

- Google presents [[a2ui-protocol|A2UI]] v0.9 as a framework-agnostic standard for declaring generated UI intent while keeping rendering inside the application's existing component catalog.
- The release strengthens [[generative-ui-specifications|Generative UI specification]] practice around custom catalogs, client-defined functions, client-to-server data syncing, error handling, and modular schemas.
- A2UI is adjacent to [[ag-ui-protocol|AG-UI]] rather than a replacement for it: AG-UI can carry the interaction/runtime loop, while A2UI describes the UI surface the user sees and manipulates.
- The current agent SDK example is Python-first, with Go and Kotlin described as future work, so this is not a .NET implementation guide.
- The practical planning signal is strong for any stack: controlled catalog rendering is the core trust boundary for portable generated UI.

## Detailed Notes

### Problem Addressed

The source argues that generative UI needs a clean separation of concerns before it can move from demos to production. Agents should be able to generate tailored widgets in real time, but applications still need to keep control over design systems, component catalogs, validation, and rendering.

### A2UI v0.9 Direction

A2UI v0.9 makes the "bring your own catalog" model more explicit. The optional component set is called "Basic" rather than "Standard" to avoid implying that applications should abandon their existing component systems. That reinforces a production pattern: the agent selects and fills approved components, while the trusted client renders them.

The release adds a shared web-core library, official React renderer work, version bumps across supported renderers, and a clearer community-renderer lane. It also adds an agent SDK intended to parse, repair, validate, and stream generated UI parts incrementally.

### Runtime and Transport Layering

The source explicitly names A2UI over MCP, WebSockets, REST, AG-UI, and A2A. That supports the wiki's existing separation between generated UI specifications and runtime interaction protocols. In this model:

- A2UI defines structured UI payloads and catalog constraints.
- AG-UI can carry agent-user interaction and middleware behavior.
- A2A can connect remote agents or bridge agent-to-frontend transport.
- MCP can expose tool and app resources that may contain or produce UI.

### AG-UI Compatibility

The source says agents that already speak AG-UI can drive A2UI v0.9 through AG-UI middleware. The middleware teaches the agent pipeline how to produce A2UI and wire the output to renderers or custom components. That makes A2UI a payload/specification layer that can ride on AG-UI rather than another competing runtime protocol.

### Evidence and Maturity

The article is an official Google Developers Blog announcement from the Google A2UI Team, published on 2026-04-17. It points to A2UI docs, GitHub samples, a roadmap, supported renderers, a React starter through CopilotKit, and ecosystem examples across AG2, A2A, Vercel json-renderer, Oracle Agent Spec, AG-UI, Flutter health, and financial-planning demos.

## Durable vs. Time-Sensitive

Durable:

- Generated UI needs a layer split among agent execution, UI payload specification, trusted rendering, and transport.
- Custom component catalogs are the trust and design-system boundary for declarative generative UI.
- A2UI is best understood as a generated UI specification, not the whole agent runtime.

Time-sensitive:

- v0.9 feature status, SDK language availability, renderer support, and named ecosystem integrations may change quickly.
- The current SDK example is Python-first; non-Python support should be rechecked before implementation planning.

## Connections

- Extends [[generative-ui-specifications]] from a taxonomy page into a concrete A2UI implementation direction.
- Updates [[ag-ui-protocol]] with a sharper AG-UI-plus-A2UI layering model.
- Complements [[mcp-apps-middleware-bridge]] because both are about composing generated UI surfaces around an interaction protocol.
- Strengthens [[trusted-frontend-mediation-for-ag-ui]] by showing why generated UI catalogs and validation belong in trusted application code.

## Open Questions

- How stable will A2UI v0.9 become relative to v0.8 stable?
- What would a practical .NET/Blazor renderer or adapter look like for A2UI catalogs?
- How should AG-UI clients negotiate support for A2UI, Open-JSON-UI, MCP-UI, or custom generated UI schemas?
- Which conformance tests should cover generated UI payload validation alongside AG-UI event behavior?

## Sources

- raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md

---
title: CopilotKit Generative UI for Agentic Apps
created: 2026-05-04
updated: 2026-05-04
type: source-summary
tags: [generative-ui, software-engineering, human-computer-interaction, runtime-rendering, adaptive-ui, framework, open-source]
sources: [raw/articles/copilotkit-generative-ui-for-agentic-apps.md]
contradictions: []
---

# CopilotKit Generative UI for Agentic Apps

## Key Points

- CopilotKit frames Generative UI as a control spectrum: controlled UI through [[ag-ui-protocol|AG-UI]], declarative UI through [[a2ui-protocol|A2UI]] or Open-JSON-UI, and open-ended UI through MCP Apps or custom iframe/component rendering.
- The source is developer-facing and implementation-oriented, but it is React/CopilotKit-first rather than .NET-first.
- AG-UI is positioned as the runtime interaction layer beneath multiple generated UI styles, not as the UI payload specification itself.
- Controlled Generative UI maps closely to [[frontend-tool-rendering]]: developers prebuild components, expose typed tools, and let the agent choose when to display them with validated data.
- Declarative Generative UI uses structured payloads such as A2UI JSONL envelopes and an application renderer such as `createA2UIMessageRenderer`.
- Open-ended Generative UI increases expressive power but shifts more weight to sandboxing, resource trust, performance, and style consistency.

## Detailed Notes

### Problem Addressed

The source addresses a practical developer question: how should agentic applications let agents generate, select, or control UI without collapsing everything into arbitrary HTML or a chat transcript?

Its answer is to split Generative UI by degree of application control. That gives teams a vocabulary for choosing between prebuilt component rendering, declarative component specifications, and open-ended app surfaces.

### Three Main Patterns

Controlled Generative UI is the highest-control option. The application owns the components and render states, while the agent selects a tool and supplies arguments or results. In CopilotKit this is shown with `useFrontendTool`, where the developer registers the tool schema, handler, and render function.

Declarative Generative UI is the middle option. The agent emits a structured UI description and the frontend renders it. The source uses A2UI and Open-JSON-UI as examples, with A2UI represented through `surfaceUpdate`, `dataModelUpdate`, and `beginRendering` message envelopes.

Open-ended Generative UI is the lowest-control option. The source discusses MCP Apps, where a server returns a UI surface such as an iframe-backed widget, and a custom `useComponent` path where generated HTML, SVG, Canvas, or WebGL content is rendered in a sandboxed iframe.

### Runtime and Specification Layering

The source reinforces the wiki's existing boundary between runtime protocols and generated UI specifications. AG-UI provides the bidirectional agent-to-application interaction layer. A2UI, Open-JSON-UI, MCP Apps, or custom component protocols describe or host the rendered interface.

That means a product can use the same AG-UI runtime while choosing different UI generation policies per feature. A dashboard KPI widget might stay controlled, a form builder might use A2UI, and a creative diagramming flow might use an MCP App.

### Evidence and Maturity

The raw capture is from the maintained CopilotKit monorepo example path. The standalone `CopilotKit/generative-ui` repository says the latest version now lives inside the CopilotKit monorepo. The monorepo HEAD checked during ingestion was dated 2026-05-03, while the standalone repository HEAD was dated 2026-03-20.

The source provides runnable example links, CopilotKit code snippets, and visual assets, but it is still a framework ecosystem example rather than a formal standard.

## Durable vs. Time-Sensitive

Durable:

- Generative UI should be evaluated by control level, not only by protocol name.
- Controlled, declarative, and open-ended UI create different safety, portability, and developer-effort trade-offs.
- AG-UI can remain the runtime layer even when the visible UI is produced by A2UI, Open-JSON-UI, MCP Apps, or custom renderers.

Time-sensitive:

- CopilotKit package names, hooks, sample paths, and monorepo layout may change.
- Open-JSON-UI and A2UI renderer maturity should be refreshed before implementation planning.
- The examples are React-first; .NET or Blazor teams need an adapter or custom renderer rather than a direct copy.

## Connections

- Creates [[generative-ui-control-spectrum]] as a comparison page for choosing among controlled, declarative, and open-ended UI approaches.
- Extends [[generative-ui-specifications]] with a practical control-spectrum framing.
- Updates [[copilotkit]] from a general AG-UI frontend runtime into a concrete Generative UI example host.
- Adds a CopilotKit `useFrontendTool` example to [[frontend-tool-rendering]].
- Complements [[mcp-apps-middleware-bridge]] by showing why MCP Apps belong on the open-ended side of the spectrum.

## Open Questions

- What is the right Blazor Server equivalent of CopilotKit `useFrontendTool` for controlled Generative UI?
- Should a .NET/Blazor AG-UI app add A2UI support through AG-UI state events, custom events, or a dedicated renderer pipeline?
- Which UI generation level should be default for enterprise operational dashboards where trust and consistency matter more than expressive freedom?

## Sources

- raw/articles/copilotkit-generative-ui-for-agentic-apps.md


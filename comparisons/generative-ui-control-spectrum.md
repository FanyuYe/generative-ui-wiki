---
title: Generative UI Control Spectrum
created: 2026-05-04
updated: 2026-05-04
type: comparison
tags: [generative-ui, comparison, runtime-rendering, adaptive-ui, human-computer-interaction, software-agents]
sources: [raw/articles/copilotkit-generative-ui-for-agentic-apps.md, raw/articles/ag-ui-generative-ui-specs.md, raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md]
contradictions: []
---

# Generative UI Control Spectrum

## What Is Being Compared

This page compares three practical Generative UI approaches by how much control the application keeps versus how much freedom the agent receives at runtime.

The source names the three main approaches as controlled Generative UI, declarative Generative UI, and open-ended Generative UI. A fourth custom component path, represented by CopilotKit `useComponent`, sits near the open-ended side because the agent can generate raw HTML, SVG, Canvas, or WebGL content for a sandboxed renderer.

## Comparison

| Approach | Typical mechanism | Application control | Agent freedom | Best fit | Main risk |
| --- | --- | --- | --- | --- | --- |
| Controlled Generative UI | Prebuilt components plus typed tool/render hooks such as CopilotKit `useFrontendTool` or AG-UI tool rendering | High | Low | Operational apps, dashboards, forms, approvals, regulated workflows | Too rigid if the task needs new layouts or component combinations |
| Declarative Generative UI | Structured UI payloads such as A2UI JSONL or Open-JSON-UI rendered by a trusted component catalog | Medium | Medium | Configurable forms, cards, lists, and multi-step task UIs | Schema drift, renderer gaps, and weak component allowlists |
| Open-ended Generative UI | MCP Apps, iframe-backed resources, or custom generated UI surfaces | Low to medium | High | Creative canvases, diagramming, exploratory visualizations, external app embedding | Security, styling inconsistency, portability, and performance |
| Custom generated component path | Tool call carries generated HTML/SVG/Canvas to a sandboxed renderer such as `useComponent` | Medium | High | Rich visualizations without a separate MCP app server | Generated content validation and iframe policy become central |

## Verdict

For production business applications, controlled Generative UI should usually be the default. It keeps layout, accessibility, authorization, and design-system behavior in developer-owned code while still letting the agent decide when a component is useful.

Declarative Generative UI is the next step when the component choices and layouts need more flexibility but the product still needs a validated schema and renderer. [[a2ui-protocol|A2UI]] is the clearest example already captured in this wiki.

Open-ended Generative UI is powerful but should be treated as an app-embedding or sandboxing problem, not just a rendering convenience. [[mcp-apps-middleware-bridge]] is useful here because it keeps external MCP app resources behind a mediation layer instead of making the core agent backend own every UI surface.

## Practical Implications

For [[ag-ui-protocol|AG-UI]] systems, the control spectrum separates runtime from rendering policy:

- AG-UI can carry run, text, tool, state, and user-interaction events.
- Controlled UI can be rendered through tool-call lifecycle components.
- Declarative UI can be represented by an A2UI, Open-JSON-UI, or custom schema payload rendered by a trusted client.
- Open-ended UI can be hosted through MCP Apps, iframe resources, or sandboxed component renderers.

For .NET and Blazor work, the source is a planning reference rather than a drop-in implementation. The closest production default remains a constrained widget schema or prebuilt Blazor components driven by AG-UI state and tool events. Declarative A2UI-style support would require a Blazor renderer and catalog validation layer.

## Open Questions

- Should a Blazor AG-UI client expose controlled components as frontend tools, AG-UI state renderers, or both?
- How should AG-UI feature coverage testing evaluate declarative payload rendering and sandboxed open-ended UI?
- Which trust boundary should own MCP Apps resource authorization when the backend is intentionally unaware of the iframe resource layer?

## Related

- [[ag-ui-protocol]]
- [[generative-ui-specifications]]
- [[a2ui-protocol]]
- [[frontend-tool-rendering]]
- [[backend-tool-rendering]]
- [[mcp-apps-middleware-bridge]]
- [[trusted-frontend-mediation-for-ag-ui]]

## Sources

- raw/articles/copilotkit-generative-ui-for-agentic-apps.md
- raw/articles/ag-ui-generative-ui-specs.md
- raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md


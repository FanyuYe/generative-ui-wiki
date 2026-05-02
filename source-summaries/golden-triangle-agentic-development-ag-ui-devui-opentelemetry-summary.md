---
title: The Golden Triangle of Agentic Development with Microsoft Agent Framework
created: 2026-05-01
updated: 2026-05-01
type: source-summary
tags: [artificial-intelligence, generative-ui, software-agents, software-engineering, framework, observability, runtime-rendering]
sources: [raw/articles/golden-triangle-agentic-development-ag-ui-devui-opentelemetry.md]
contradictions: []
author: Microsoft Agent Framework DevBlog
---

# The Golden Triangle of Agentic Development with Microsoft Agent Framework

## Key Points

- The source frames a developer lifecycle stack for Microsoft Agent Framework: GitHub Models for quick model access, DevUI for local debugging, [[ag-ui-protocol|AG-UI]] for user-facing interaction, and OpenTelemetry for performance and cost visibility.
- It is the strongest remaining non-duplicate resource for the wiki's ranking policy because it is official Microsoft developer content and includes a concrete .NET Agent Framework path, even though its publication date is 2025-12-01 rather than a newer 2026 page.
- DevUI is positioned as an inner-loop visual console for inspecting agent reasoning, actions, observations, and conversation state before exposing the agent to users.
- AG-UI is positioned as the delivery layer: a standard protocol endpoint that lets CopilotKit or other AG-UI clients render streaming responses, backend tool rendering, human approval, and shared state.
- OpenTelemetry is positioned as the operational feedback layer for traces, token/cost visibility, latency analysis, Aspire Dashboard, Application Insights, and Grafana dashboards.

## Detailed Notes

### Creation with GitHub Models

The article starts with a `GHModel.AI` sample that creates a two-agent travel recommendation workflow. Both Python and .NET examples use GitHub Models as the model backend through Agent Framework abstractions.

The durable point is not the travel example itself. The durable point is that Agent Framework is shown as a cross-language abstraction around model access and workflow composition, making the same agent-development lifecycle available to Python and .NET developers.

### DevUI as inner-loop debugger

The DevUI section focuses on local debugging before product exposure. The source says DevUI can show reasoning, actions, observations, and conversation state in a visual console.

For [[agent-execution-observability]], this adds an important distinction: not every observability surface is user-facing Generative UI. DevUI is a developer-facing observability layer for the agent builder, while AG-UI is the user-facing protocol layer for the application.

The .NET example runs from `GHModel.dotNET.AI/GHModel.dotNET.AI.Workflow.DevUI` and exposes a local DevUI endpoint. This makes the source more relevant to the wiki's `.NET > js > python > others` ranking rule than the current CopilotKit-only generative UI guides.

### AG-UI as delivery layer

The AG-UI section shows the move from developer inspection to user-facing interaction. In .NET, the example calls `builder.Services.AddAGUI()` and `app.MapAGUI("/", workflowAgent)` using `Microsoft.Agents.AI.Hosting.AGUI.AspNetCore`.

The article describes AG-UI as a way to connect Agent Framework agents to frontend clients without hand-writing a custom React UI for every demo. Its supported feature list overlaps existing wiki coverage: SSE streaming, backend tool rendering, human-in-the-loop approval, shared state synchronization, and CopilotKit integration.

The new contribution is lifecycle placement. AG-UI is not only a protocol feature list; it is the handoff point from local agent debugging to stakeholder- or user-visible interaction.

### OpenTelemetry as operational feedback

The OpenTelemetry section adds performance and cost visibility to the same loop. The .NET example creates a tracer provider with `.AddSource("*Microsoft.Agents.AI")` and an OTLP exporter. The article also points to Aspire Dashboard for local development, Application Insights for production monitoring, and Grafana dashboards for advanced visualization.

This turns observability into more than UI transparency. Developers need traces and metrics to know where latency, model calls, guardrails, routing, and token consumption occur before moving a prototype into production.

## Ingest Extraction

- Problem addressed: agentic applications are hard to debug, demo, and optimize when local behavior, user-facing UI, and telemetry are handled as separate ad hoc concerns.
- Practical lane: software engineering.
- Main artifact: official Microsoft developer blog and sample-driven architecture guidance.
- Core mechanism: a closed-loop developer stack where Agent Framework workflows move through model-backed creation, DevUI debugging, AG-UI presentation, and OpenTelemetry measurement.
- Evidence type: official Microsoft Agent Framework DevBlog walkthrough with Python and .NET code snippets.
- Why it matters: it connects Generative UI to the larger development lifecycle around inspection, demonstration, and operational readiness.
- When to use it: Agent Framework projects that need a path from prototype to interactive demo to measured production hardening, especially when .NET support matters.
- When not to over-apply it: simple single-agent scripts, UI-only generative component experiments, or projects outside Agent Framework that do not use DevUI or Microsoft telemetry conventions.
- Limitations and failure modes: promotional tone, older publication date than current April 2026 AG-UI docs, and sample-level guidance rather than a complete production deployment reference.

## Connections

- [[closed-loop-agentic-development-stack]] is the durable pattern extracted from the source.
- [[microsoft-agent-framework-ag-ui-integration]] gains lifecycle context around DevUI and OpenTelemetry, not just protocol endpoint mechanics.
- [[agent-execution-observability]] gains a clearer split between developer-facing inspection and user-facing protocol observability.
- [[workflow-driven-agent-ui-orchestration]] is the kind of workflow surface that benefits from the DevUI to AG-UI to OpenTelemetry loop.

## Open Questions

- How should teams decide which DevUI-visible reasoning details should never become user-visible AG-UI state?
- Which OpenTelemetry spans and metrics are stable enough to become production dashboards for Agent Framework applications?
- How does this lifecycle change once .NET workflow support and AG-UI workflow parity mature further?

## Sources

- raw/articles/golden-triangle-agentic-development-ag-ui-devui-opentelemetry.md
- raw/assets/golden-triangle-agentic-development-ag-ui-devui-opentelemetry/

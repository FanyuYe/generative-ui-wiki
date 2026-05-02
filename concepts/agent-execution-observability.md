---
title: Agent Execution Observability
created: 2026-04-20
updated: 2026-05-01
type: concept
tags: [generative-ui, software-agents, human-computer-interaction, observability, trust]
sources: [raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md, raw/articles/workflows-with-ag-ui.md, raw/articles/golden-triangle-agentic-development-ag-ui-devui-opentelemetry.md]
contradictions: []
---

# Agent Execution Observability

## Definition

Agent Execution Observability is the ability for a user interface to show what an AI agent is doing while it works: when a run starts, when text streams, when tools are called, when tool arguments and results appear, when errors occur, and when the run finishes.

In Generative UI, this matters because agent work is often multi-step and asynchronous. Without observable execution, users see a spinner while the agent performs hidden reasoning, tool calls, retrieval, or external actions.

## Current State of Knowledge

The AG-UI article frames observability as the main difference between custom streaming and a standardized protocol. Custom streaming may send partial text, but [[ag-ui-protocol|AG-UI]] sends typed events such as `RUN_STARTED`, `TEXT_MESSAGE_CONTENT`, `TOOL_CALL_START`, `TOOL_CALL_RESULT`, and `RUN_FINISHED`.

This creates a shared event vocabulary that different frontends can render without parsing unstructured text or writing backend-specific adapters. It also supports [[backend-tool-rendering]], where the interface can show tool execution as a visible part of the interaction.

The workflow tutorial extends observability from run-level visibility into step-level and pause-level visibility. In addition to normal text and tool events, clients can receive `STEP_STARTED`, `STEP_FINISHED`, `CUSTOM` status events, `CUSTOM` request-info events, workflow outputs, and resumable interrupts at `RUN_FINISHED`.

The Microsoft Agent Framework "Golden Triangle" article separates developer-facing and user-facing observability. DevUI is the inner-loop surface for inspecting reasoning, actions, observations, and conversation state while building an agent. AG-UI is the product-facing protocol surface that turns selected execution state into user-visible interaction. OpenTelemetry is the operational layer for traces, latency, token/cost visibility, and dashboards.

## Practical Implications for Generative UI

Observable execution turns agent progress into UI state. A client can show "looking up order," render streamed text, reveal tool results, show an error source, or hide loading indicators when a run ends.

For workflow-based interfaces, observability also means showing which specialist agent or stage is active, why the workflow paused, and what user action is required to resume. That pushes observability closer to navigation and control, not just transparency.

The article's mental model shift is from request/response to event subscription. For interface design, this means the UI is not just waiting for content; it is reacting to a stream of execution state. [[agent-ui-protocol-bridge]] provides the architecture needed to translate framework-native execution into those user-facing states.

[[closed-loop-agentic-development-stack]] adds a lifecycle view. Developers can inspect detailed internal behavior in DevUI, expose a cleaner version of execution state through AG-UI, and use OpenTelemetry to find latency and cost problems that are not visible in the chat or UI transcript alone.

## Failure Modes

- Too little observability leaves users with spinner-only uncertainty.
- Too much observability can overwhelm users with internal mechanics.
- Step-heavy workflows can create noisy status UIs if every backend transition is rendered literally.
- Raw tool arguments or results can leak sensitive data.
- Progress indicators can imply certainty before a tool result is confirmed.
- Framework-specific event formats can lock the UI to one backend if no common protocol is used.
- Developer-only reasoning traces can leak into product surfaces if DevUI and AG-UI audiences are not separated.
- Telemetry can expose sensitive prompts, tool arguments, or customer data if development observability settings are copied into production unchanged.

## Open Questions

- What level of tool visibility builds trust without exposing irrelevant implementation detail?
- How should generated interfaces summarize long tool chains?
- When should event streams support cancellation, pause, or user intervention?

## Related

- [[ag-ui-protocol]]
- [[agent-ui-protocol-bridge]]
- [[backend-tool-rendering]]
- [[microsoft-agent-framework-ag-ui-integration]]
- [[workflow-driven-agent-ui-orchestration]]
- [[closed-loop-agentic-development-stack]]

## Sources

- raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md
- raw/articles/workflows-with-ag-ui.md
- raw/articles/golden-triangle-agentic-development-ag-ui-devui-opentelemetry.md

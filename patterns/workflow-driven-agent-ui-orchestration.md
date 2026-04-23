---
title: Workflow-Driven Agent UI Orchestration
created: 2026-04-23
updated: 2026-04-23
type: pattern
tags: [generative-ui, pattern, software-agents, runtime-rendering, observability, human-computer-interaction]
sources: [raw/articles/workflows-with-ag-ui.md]
contradictions: []
status: observed
---

# Workflow-Driven Agent UI Orchestration

## Summary

Workflow-Driven Agent UI Orchestration is a Generative UI pattern where a multi-agent or multi-step workflow is exposed as a single AG-UI endpoint and the client renders workflow progress, interruptions, resumptions, and intermediate outputs as first-class interface state.

## When It Appears

This pattern appears when a single agent is not enough and the backend needs explicit execution stages, specialist handoffs, or resumable human input. It is especially relevant for customer support, case handling, approvals, research pipelines, and any AI workflow where users benefit from seeing which step is active and why execution paused.

## Mechanics

1. A backend workflow defines participants, handoffs, tools, and termination conditions.
2. An AG-UI wrapper exposes that workflow through the standard HTTP and SSE endpoint shape.
3. The workflow emits normal run, text, and tool events plus workflow-specific step and custom events.
4. The client renders active-step progress, intermediate status, and workflow outputs from those events.
5. If the workflow pauses for more information or approval, the run ends with interrupts instead of silent failure.
6. The client sends a resume payload keyed by interrupt ID, and the workflow continues from its paused state.

This pattern differs from simple streaming chat because the interface is tracking execution structure, not only token flow.

## Strengths

- Makes multi-agent execution legible instead of opaque.
- Turns interruptions and resumptions into a coherent UI contract.
- Supports richer progress tracking through named steps rather than generic spinners.
- Preserves a stable frontend transport even when backend execution becomes graph-shaped.
- Lets human input participate inside the workflow instead of breaking out into side channels.

## Failure Modes

- Too many step and status events can overwhelm users with internal process detail.
- Custom workflow events can fragment frontend rendering if teams do not standardize event meaning.
- Long-lived workflows need careful state isolation so one thread does not leak into another.
- Python-first examples can be copied into .NET assumptions that are not yet supported.
- Interrupt-heavy flows can feel stalled if the UI does not clearly explain what action is needed.

## Related

- [[ag-ui-protocol]]
- [[agent-execution-observability]]
- [[agent-ui-protocol-bridge]]
- [[human-in-the-loop-tool-approval]]
- [[microsoft-agent-framework-ag-ui-integration]]

## Sources

- raw/articles/workflows-with-ag-ui.md

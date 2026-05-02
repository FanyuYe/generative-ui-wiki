---
title: Multi-Agent Handoff UI Orchestration
created: 2026-04-30
updated: 2026-04-30
type: pattern
tags: [generative-ui, pattern, software-agents, runtime-rendering, observability, human-computer-interaction]
sources: [raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md]
contradictions: []
status: observed
---

# Multi-Agent Handoff UI Orchestration

## Summary

Multi-Agent Handoff UI Orchestration is a Generative UI pattern where a backend declares an explicit graph of specialist agents, exposes the graph through a protocol endpoint, and lets the frontend render active-agent state, case state, pending interrupts, and resumable user actions.

## When It Appears

This pattern appears when an AI application needs more than one specialist agent and users must understand which agent currently owns the work. It fits customer support, operations, triage, claims, refunds, replacements, research pipelines, and enterprise workflows where the user needs visibility into both progress and control points.

It is narrower than [[workflow-driven-agent-ui-orchestration]]: all handoff UIs are workflow UIs, but not all workflow UIs expose a named graph of agents handing control to one another.

## Mechanics

1. Developers define specialist agents with clear responsibility boundaries.
2. A handoff builder declares allowed routing edges and descriptions.
3. The workflow is wrapped as an AG-UI-compatible endpoint.
4. The endpoint streams run, step, text, tool, and finish events to the client.
5. The frontend renders the active specialist, current case state, streamed messages, and pending user decisions.
6. Tool approvals and information requests pause the workflow as interrupts.
7. The frontend submits resume payloads keyed by interrupt ID, and the active agent continues from the paused point.

The key design choice is to treat handoff state as user-interface state. The UI should answer "who is working?", "what are they waiting for?", and "what will happen if I approve this?" without exposing every backend implementation detail.

## Strengths

- Makes non-linear multi-agent routing legible.
- Gives users a clear mental model for agent ownership and handoff points.
- Supports sensitive actions through [[human-in-the-loop-tool-approval]] without leaving the workflow.
- Keeps long-running multi-agent work inside one protocol and thread model.
- Lets the UI combine chat, active-agent status, case snapshots, and approval surfaces.

## Failure Modes

- Showing the full routing graph can overwhelm users who only need the next action.
- Poor interrupt summaries can make approvals feel unsafe or mechanical.
- A queue of pending interrupts can become confusing if the UI does not preserve order and context.
- Thread state can leak across users if the workflow factory or session isolation is wrong.
- Python-first examples can be mistaken for current .NET support even when C# AG-UI workflow support is still in development.

## Related

- [[ag-ui-protocol]]
- [[workflow-driven-agent-ui-orchestration]]
- [[human-in-the-loop-tool-approval]]
- [[agent-execution-observability]]
- [[microsoft-agent-framework-ag-ui-integration]]

## Sources

- raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md

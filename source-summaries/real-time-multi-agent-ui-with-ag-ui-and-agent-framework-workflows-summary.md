---
title: Building a Real-Time Multi-Agent UI with AG-UI and Microsoft Agent Framework Workflows
created: 2026-04-30
updated: 2026-04-30
type: source-summary
tags: [generative-ui, software-agents, framework, observability, runtime-rendering, human-computer-interaction]
sources: [raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md]
contradictions: []
author: Microsoft Agent Framework DevBlog
---

# Building a Real-Time Multi-Agent UI with AG-UI and Microsoft Agent Framework Workflows

## Key Points

- The source shows a concrete multi-agent customer-support UI where Microsoft Agent Framework handoffs are exposed through [[ag-ui-protocol|AG-UI]] and rendered as real-time interface state.
- `HandoffBuilder` defines a directed routing graph among triage, refund, and order agents instead of relying only on prompt instructions or a fixed chain.
- Approval-required tools and information requests both become workflow interrupts that the client can render, queue, and resume through the same AG-UI mechanism.
- The frontend experience includes an active-agent indicator, case snapshot card, streaming chat panel, and approval modal, making workflow state visible rather than hidden in backend logs.
- The source is Python-first. It explicitly says C# support for MAF + AG-UI is still in development, so it should not be read as a current .NET implementation guide.

## Detailed Notes

### Handoff graph as product-visible execution structure

The demo uses three specialized agents: a triage agent, a refund agent, and an order agent. `HandoffBuilder` declares which agents may hand off to which other agents, and each edge carries a natural-language routing description. The framework turns those descriptions into constrained handoff behavior, so the backend has an explicit topology rather than an informal prompt-only policy.

That topology matters for Generative UI because the frontend can show which specialist is active, why the workflow is waiting, and what path the case has taken. It turns multi-agent orchestration into a renderable state model.

### AG-UI endpoint wrapper

The backend wraps the workflow with `AgentFrameworkWorkflow` and registers it through `add_agent_framework_fastapi_endpoint`. The source emphasizes a `workflow_factory` that creates a fresh workflow instance per thread, which keeps concurrent conversations isolated.

The endpoint streams normal AG-UI events such as `RUN_STARTED`, `STEP_STARTED`, `TEXT_MESSAGE_*`, `TOOL_CALL_*`, and `RUN_FINISHED`. The practical value is that a graph-shaped workflow still reaches the client through the same SSE protocol surface as other Agent Framework AG-UI examples.

### Two interrupt classes

The source distinguishes two interrupt classes:

- tool approval interrupts, where `@tool(approval_mode="always_require")` pauses execution before actions such as refund or replacement submission;
- information request interrupts, where an agent asks the user for missing context such as an order ID or shipping preference.

Both use AG-UI resume semantics. The client sends a resume payload with the interrupt ID and response value, and the workflow continues from the paused point.

### Frontend obligations

The frontend is not just a transcript renderer. It consumes the SSE stream, tracks workflow state, renders the active specialist, updates a case snapshot, streams messages, and manages an interrupt queue so approvals or information requests can be handled in order.

The article's substantive image is the HITL approval modal for `submit_refund`, captured locally at `raw/assets/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows/ag-ui-handoff-approval-modal.webp`.

## Ingest Extraction

- Problem addressed: multi-agent systems become opaque when users cannot see the active agent, pending action, or reason for a pause.
- Practical lane: software engineering.
- Main artifact: developer walkthrough and reusable UI orchestration pattern.
- Core mechanism: Agent Framework handoff workflow wrapped as an AG-UI endpoint, with streamed events and resumable interrupts driving frontend state.
- Evidence type: official Microsoft Agent Framework DevBlog implementation walkthrough.
- Why it matters: it demonstrates how handoff routing, approval gates, user questions, and state snapshots become product-visible UI.
- When to use it: multi-agent customer support, operations, case handling, refunds, replacements, or other workflows where specialists pass control and sensitive actions need review.
- When not to over-apply it: simple single-agent chat, direct request/response agents, or .NET-only implementations that need a currently documented C# path.
- Limitations and failure modes: Python-only implementation today, possible UI overload from too much workflow detail, and interrupt queues that can confuse users if the pending action is not clearly summarized.

## Connections

- [[multi-agent-handoff-ui-orchestration]] is the main reusable pattern extracted from the source.
- [[workflow-driven-agent-ui-orchestration]] is the broader workflow pattern this source makes more concrete with explicit handoff topology.
- [[human-in-the-loop-tool-approval]] appears as one interrupt type inside the handoff workflow.
- [[microsoft-agent-framework-ag-ui-integration]] gains a concrete handoff-workflow example but also a current C# parity caveat.
- [[agent-execution-observability]] extends from run/tool visibility to active-agent and case-state visibility.

## Open Questions

- How much of the handoff graph should be visible to end users versus abstracted into simpler status labels?
- What is the best frontend model for multiple pending interrupts without training users to approve reflexively?
- When C# support lands, will the same `HandoffBuilder` and interrupt semantics map cleanly into the .NET AG-UI integration?

## Sources

- raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md
- raw/assets/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows/

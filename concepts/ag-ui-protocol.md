---
title: AG-UI Protocol
created: 2026-04-20
updated: 2026-04-24
type: concept
tags: [generative-ui, software-agents, human-computer-interaction, state-management, tool-use, runtime-rendering]
sources: [raw/articles/ag-ui-integration-with-agent-framework.md, raw/articles/backend-tool-rendering-with-ag-ui.md, raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md, raw/articles/state-management-with-ag-ui.md, raw/articles/human-in-the-loop-with-ag-ui.md, raw/articles/workflows-with-ag-ui.md, raw/articles/frontend-tool-rendering-with-ag-ui.md]
contradictions: []
---

# AG-UI Protocol

## Definition

AG-UI is a protocol for connecting AI agents to interactive user interfaces. In the Agent Framework integration article, it is used to expose agents as remote HTTP services that stream structured events to web or mobile clients.

For Generative UI, AG-UI matters because it treats agent execution as an event stream that can drive interface state, tool-rendered components, approvals, and custom UI rendering rather than only final text responses.

## Current State of Knowledge

The source frames AG-UI around seven protocol features: agentic chat, backend tool rendering, human-in-the-loop approvals, agentic generative UI, tool-based generative UI, shared state, and predictive state updates.

The backend tool rendering source makes the tool-call part of the protocol more concrete. AG-UI can stream `TOOL_CALL_START`, `TOOL_CALL_ARGS`, `TOOL_CALL_END`, and `TOOL_CALL_RESULT` events, allowing clients to render server-side tool execution as visible UI state.

The frontend tool rendering tutorial fills in the opposite direction of the protocol. Instead of only showing backend work to the client, AG-UI can also carry client-declared tool metadata to the server, let the model request local execution, and route the result back into the run. That makes the protocol a two-way capability surface, not just a streaming output channel.

The human-in-the-loop tutorial makes the approval path more concrete. Instead of treating approvals as a vague capability, it shows a protocol-mediated loop where the backend converts framework approval requests into a client-visible tool call, the UI collects a decision, and middleware converts that result back into approval content so the run can continue safely.

The state-management tutorial fills in the protocol's state semantics. In the .NET implementation, clients send current state through `ag_ui_state`, the backend constrains the agent to emit structured state, and AG-UI converts JSON `DataContent` into `STATE_SNAPSHOT` events. In the Python implementation, `predict_state_config` enables `STATE_DELTA` events that stream optimistic state changes before the final committed snapshot.

The Microsoft Developer Community article explains the protocol's origin as a response to fragmented agent UI communication. It contrasts simple request/response, custom streaming, and standardized protocol stages, then positions AG-UI as the standardized stage that gives clients [[agent-execution-observability]] across compliant frameworks.

The workflow tutorial extends the protocol from single-agent runs to graph-shaped execution. It adds explicit step boundaries such as `STEP_STARTED` and `STEP_FINISHED`, plus `CUSTOM` events for workflow state, human-input requests, and intermediate outputs. It also makes interrupts and resume payloads part of the protocol contract for long-running multi-agent flows.

The protocol replaces ad hoc application-managed concerns with standard equivalents:

| Concern | Direct agent usage | AG-UI role |
| --- | --- | --- |
| Deployment | Agent embedded in one app | Agent exposed as a remote HTTP endpoint |
| Streaming | In-process async iteration | SSE protocol events |
| Session context | Application-managed | Session or thread identifiers |
| State | Application-specific snapshots | Protocol-level state synchronization |
| Approvals | Custom workflow | Human-in-the-loop protocol support |
| UI rendering | Bespoke client integration | Tool and state events that clients can render |

Design rationale from the article:

- Server-Sent Events fit many agent UIs because the dominant flow is server-to-client updates over standard HTTP.
- Protocol-managed threads reduce fragile client-side conversation-history management.
- Standard event types let UIs react to execution without custom text parsing.
- Workflow-aware events make multi-step progress, pausing, and resumption renderable without inventing a separate frontend state channel.

## Practical Implications for Generative UI

AG-UI pushes generated interfaces toward an explicit protocol boundary. Instead of a model producing free-form UI descriptions, the agent backend emits typed events, tool calls, and state updates that a frontend can render with predictable components.

This makes AG-UI a useful substrate for [[agent-ui-protocol-bridge|agent-UI protocol bridges]], where framework-specific agent execution is translated into frontend-friendly events. It also makes protocols and adapters part of the Generative UI architecture, alongside prompt design, component generation, and [[shared-ui-state-synchronization]].

[[backend-tool-rendering]] shows how this works at the tool layer: the backend owns the tool implementation, while the frontend receives enough structured event data to show progress, arguments, results, and errors.

[[frontend-tool-rendering]] shows the complementary direction: the frontend can expose typed local affordances such as location, preferences, or UI operations, while the backend agent remains the orchestrator that chooses when those capabilities are used.

Adoption guidance is context-dependent. AG-UI fits new agent projects, multi-step workflows that benefit from visible progress, and teams seeking framework flexibility. Simpler request/response agents, stable legacy systems, or mission-critical deployments that cannot tolerate evolving standards may be better served by alternatives.

## Failure Modes

- Event streams can expose partial progress that users misinterpret as final state.
- Step and custom workflow events can overexpose backend process detail if the UI lacks a clear abstraction layer.
- Predictive state updates can create optimism gaps if later tool execution disagrees with streamed arguments.
- Shared state can diverge if frontend and backend state models are not carefully versioned.
- Custom UI components can overfit to one agent framework unless the AG-UI contract remains stable.
- Approval flows can protect users but may slow down otherwise fluid interactions.
- Tool-call argument and result streams can expose sensitive data if the UI renders them without redaction or summarization.
- A young protocol can introduce migration or compatibility risk for teams that need long-term stability.

## Open Questions

- Which AG-UI event patterns produce the clearest user mental model for long-running agents?
- How should AG-UI clients reconcile optimistic state updates with corrected backend state?
- What test harnesses are needed for protocol-level UI behavior, not just backend agent correctness?
- How should clients degrade gracefully if a backend supports only part of the event vocabulary?

## Related

- [[microsoft-agent-framework-ag-ui-integration]]
- [[copilotkit]]
- [[agent-ui-protocol-bridge]]
- [[backend-tool-rendering]]
- [[frontend-tool-rendering]]
- [[human-in-the-loop-tool-approval]]
- [[shared-ui-state-synchronization]]
- [[agent-execution-observability]]
- [[workflow-driven-agent-ui-orchestration]]

## Sources

- raw/articles/ag-ui-integration-with-agent-framework.md
- raw/articles/backend-tool-rendering-with-ag-ui.md
- raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md
- raw/articles/state-management-with-ag-ui.md
- raw/articles/human-in-the-loop-with-ag-ui.md
- raw/articles/workflows-with-ag-ui.md

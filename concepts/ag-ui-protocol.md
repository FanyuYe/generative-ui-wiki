---
title: AG-UI Protocol
created: 2026-04-20
updated: 2026-05-03
type: concept
tags: [generative-ui, software-agents, human-computer-interaction, state-management, tool-use, runtime-rendering]
sources: [raw/articles/ag-ui-integration-with-agent-framework.md, raw/articles/backend-tool-rendering-with-ag-ui.md, raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md, raw/articles/state-management-with-ag-ui.md, raw/articles/human-in-the-loop-with-ag-ui.md, raw/articles/workflows-with-ag-ui.md, raw/articles/frontend-tool-rendering-with-ag-ui.md, raw/articles/security-considerations-for-ag-ui.md, raw/articles/testing-with-ag-ui-dojo.md, raw/articles/mcp-apps-compatibility-with-ag-ui.md, raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md, raw/articles/ag-ui-generative-ui-specs.md, raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md]
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

The Microsoft Agent Framework handoff demo makes that graph-shaped execution more product-visible. It shows a triage/refund/order agent workflow where a declared handoff topology, active-agent indicators, case snapshots, approval modals, and queued information requests all ride on the AG-UI event and interrupt model. This is the basis for [[multi-agent-handoff-ui-orchestration]].

The security guidance adds an important constraint to the protocol story. Because AG-UI clients can send not only user text but also structured messages, tools, state, context, and forwarded properties, the protocol boundary is also a security boundary. The recommended architecture is to keep end users behind a [[trusted-frontend-mediation-for-ag-ui|trusted frontend mediator]] rather than exposing the AG-UI server directly to untrusted browser or mobile clients.

The Dojo testing guide makes the protocol more operational. Instead of treating the seven AG-UI features as abstract capabilities, it turns them into a visible test surface with one endpoint per feature. That gives the protocol a concrete evaluation path through [[ag-ui-feature-coverage-testing]], even if the current documented harness is centered on the Python integration.

The MCP Apps compatibility page adds a composition case around the protocol. Agent Framework Python AG-UI endpoints can remain ordinary AG-UI backends while a TypeScript `MCPAppsMiddleware` layer in CopilotKit Runtime or a Node.js proxy handles MCP tool discovery, iframe resource proxying, and `ui/resourceUri` resolution. That makes [[mcp-apps-middleware-bridge]] a separate frontend/runtime bridge rather than a backend protocol extension.

The AG-UI Generative UI specs page clarifies the protocol's layer boundary. A2UI, Open-JSON-UI, and MCP-UI are [[generative-ui-specifications|generative UI specifications]] that describe dynamic UI payloads or surfaces. AG-UI is the bidirectional runtime protocol that connects the agent, application, and user, and can support those specs or custom standards rather than replacing them.

The Google A2UI v0.9 announcement adds a more concrete composition path. It says agents already speaking AG-UI can drive [[a2ui-protocol|A2UI]] through middleware, with AG-UI carrying the interaction/runtime path and A2UI describing the generated UI surface that a trusted renderer turns into components.

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
- Handoff-aware UIs can make agent ownership and sensitive pending actions visible without leaving the protocol stream.
- Richer protocol fields improve UI capability but widen the prompt-injection and data-exposure surface unless trusted code constructs and filters them.
- MCP Apps can be composed around an AG-UI endpoint without changing the backend, but the middleware/proxy layer becomes an important resource and tool boundary.
- Generated UI specifications are adjacent payload formats, while AG-UI remains the interaction protocol underneath them.
- A2UI can be layered on top of AG-UI when an application needs declarative generated UI while keeping the runtime connection, agent state, and user actions in an AG-UI-compatible flow.

## Practical Implications for Generative UI

AG-UI pushes generated interfaces toward an explicit protocol boundary. Instead of a model producing free-form UI descriptions, the agent backend emits typed events, tool calls, and state updates that a frontend can render with predictable components.

This makes AG-UI a useful substrate for [[agent-ui-protocol-bridge|agent-UI protocol bridges]], where framework-specific agent execution is translated into frontend-friendly events. It also makes protocols and adapters part of the Generative UI architecture, alongside prompt design, component generation, and [[shared-ui-state-synchronization]].

The generated-UI-spec distinction sharpens this architecture. AG-UI can carry the interaction loop, while [[a2ui-protocol|A2UI]], Open-JSON-UI, MCP-UI, or a custom schema can define what a dynamic UI payload looks like. That gives developers a layered design choice instead of forcing one protocol to solve transport, state, tools, and generated component representation at once.

[[backend-tool-rendering]] shows how this works at the tool layer: the backend owns the tool implementation, while the frontend receives enough structured event data to show progress, arguments, results, and errors.

[[frontend-tool-rendering]] shows the complementary direction: the frontend can expose typed local affordances such as location, preferences, or UI operations, while the backend agent remains the orchestrator that chooses when those capabilities are used.

The security tutorial sharpens that boundary: frontend tools, shared state, and rendered tool results should not be treated as neutral transport details. They are policy surfaces that need validation, authorization, and redaction if the client is not fully trusted.

The MCP Apps tutorial adds a related but distinct boundary. MCP-powered tools and resources may be introduced in a TypeScript runtime layer while the backend stays unaware, so teams need to reason about the AG-UI endpoint, the middleware, and the MCP app resource surface together.

Adoption guidance is context-dependent. AG-UI fits new agent projects, multi-step workflows that benefit from visible progress, and teams seeking framework flexibility. Simpler request/response agents, stable legacy systems, or mission-critical deployments that cannot tolerate evolving standards may be better served by alternatives.

## Failure Modes

- Event streams can expose partial progress that users misinterpret as final state.
- Step and custom workflow events can overexpose backend process detail if the UI lacks a clear abstraction layer.
- Predictive state updates can create optimism gaps if later tool execution disagrees with streamed arguments.
- Shared state can diverge if frontend and backend state models are not carefully versioned.
- Custom UI components can overfit to one agent framework unless the AG-UI contract remains stable.
- Approval flows can protect users but may slow down otherwise fluid interactions.
- Tool-call argument and result streams can expose sensitive data if the UI renders them without redaction or summarization.
- Directly exposing an AG-UI endpoint to untrusted clients can let attackers inject hidden instructions through messages, state, tools, context, or forwarded properties.
- Treating MCP Apps middleware as invisible plumbing can hide authorization, iframe proxying, and resource-resolution risks from backend developers.
- Multi-agent handoff UIs can confuse users if active-agent state and pending interrupts are surfaced as raw backend mechanics rather than task-level explanations.
- A young protocol can introduce migration or compatibility risk for teams that need long-term stability.
- Teams can misapply AG-UI if they treat it as the generated UI schema instead of the runtime protocol that can coordinate separate UI specifications.

## Open Questions

- Which AG-UI event patterns produce the clearest user mental model for long-running agents?
- How should AG-UI clients reconcile optimistic state updates with corrected backend state?
- How should [[ag-ui-feature-coverage-testing]] evolve from interactive exploration into stronger conformance and regression testing?
- How should clients degrade gracefully if a backend supports only part of the event vocabulary?
- How should MCP Apps middleware behavior be tested alongside AG-UI endpoint behavior when the backend is intentionally unaware of the MCP layer?
- What is the right abstraction level for showing handoff graphs and active-agent ownership to non-developer users?
- How should AG-UI clients advertise support for specific [[generative-ui-specifications]] such as A2UI, Open-JSON-UI, MCP-UI, or custom schemas?

## Related

- [[microsoft-agent-framework-ag-ui-integration]]
- [[copilotkit]]
- [[a2ui-protocol]]
- [[agent-ui-protocol-bridge]]
- [[backend-tool-rendering]]
- [[frontend-tool-rendering]]
- [[human-in-the-loop-tool-approval]]
- [[shared-ui-state-synchronization]]
- [[agent-execution-observability]]
- [[workflow-driven-agent-ui-orchestration]]
- [[multi-agent-handoff-ui-orchestration]]
- [[trusted-frontend-mediation-for-ag-ui]]
- [[ag-ui-feature-coverage-testing]]
- [[mcp-apps-middleware-bridge]]
- [[generative-ui-specifications]]

## Sources

- raw/articles/ag-ui-integration-with-agent-framework.md
- raw/articles/backend-tool-rendering-with-ag-ui.md
- raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md
- raw/articles/state-management-with-ag-ui.md
- raw/articles/human-in-the-loop-with-ag-ui.md
- raw/articles/workflows-with-ag-ui.md
- raw/articles/frontend-tool-rendering-with-ag-ui.md
- raw/articles/security-considerations-for-ag-ui.md
- raw/articles/testing-with-ag-ui-dojo.md
- raw/articles/mcp-apps-compatibility-with-ag-ui.md
- raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md
- raw/articles/ag-ui-generative-ui-specs.md
- raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md

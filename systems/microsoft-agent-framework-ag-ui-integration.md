---
title: Microsoft Agent Framework AG-UI Integration
created: 2026-04-20
updated: 2026-04-23
type: system
tags: [generative-ui, software-agents, framework, runtime-rendering, state-management, tool-use]
sources: [raw/articles/ag-ui-integration-with-agent-framework.md, raw/articles/backend-tool-rendering-with-ag-ui.md, raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md, raw/articles/state-management-with-ag-ui.md, raw/articles/human-in-the-loop-with-ag-ui.md, raw/articles/workflows-with-ag-ui.md]
contradictions: []
status: active
---

# Microsoft Agent Framework AG-UI Integration

## Overview

Microsoft Agent Framework AG-UI Integration is an adapter layer that exposes Agent Framework agents through the [[ag-ui-protocol|AG-UI protocol]]. It lets web or mobile clients call agents over HTTP and receive structured Server-Sent Events for text, tools, approvals, and state updates.

## Generative UI Role

The integration makes Agent Framework usable as a backend for Generative UI applications. Instead of embedding an agent directly in one application, developers can host the agent as a service and let clients render streamed agent state, backend tool results, approval prompts, and custom UI components.

This places the integration squarely in the [[agent-ui-protocol-bridge]] pattern: framework-native agent events are converted into a UI protocol that frontend runtimes can consume.

## Architecture Notes

The source describes two implementation paths.

### .NET path

- ASP.NET Core endpoint created with `MapAGUI("/", agent)`.
- `AIAgent` created from `IChatClient` or a custom implementation.
- Middleware pipeline handles approvals, state management, and custom logic.
- Protocol adapter converts `AgentResponseUpdate` into AG-UI events.
- Backend tools are created with `AIFunctionFactory.Create()`; complex parameter types require serializer options from the app's configured HTTP `JsonOptions`.
- Package: `Microsoft.Agents.AI.Hosting.AGUI.AspNetCore`.
- State middleware can read `ag_ui_state`, run a schema-constrained first pass for structured state, emit `STATE_SNAPSHOT` events via JSON `DataContent`, then run a second pass for user-facing summary text.
- Approval middleware can wrap tools with `ApprovalRequiredAIFunction`, translate `FunctionApprovalRequestContent` into a client-visible approval tool call, then convert the returned decision back into `FunctionApprovalResponseContent`.
- After approval is resolved, the temporary approval tool call and result must be removed from message history to keep model-provider tool-call sequencing valid.

### Python path

- FastAPI endpoint created with `add_agent_framework_fastapi_endpoint`.
- `AgentFrameworkAgent` wraps Agent Framework agents for AG-UI.
- Orchestrators manage execution flows such as default runs, approvals, and state management.
- Event bridge converts Agent Framework events into AG-UI event types.
- Message adapters convert between AG-UI and Agent Framework message formats.
- Backend tools are Python functions marked with `@tool`, typed with annotations and `Field` metadata, and attached to the agent before the FastAPI endpoint is registered.
- Package: `agent-framework-ag-ui --pre`.
- `state_schema` and `predict_state_config` can map tool arguments into protocol state, producing predictive `STATE_DELTA` events followed by final committed snapshots.
- `AgentFrameworkWorkflow` can wrap a native `Workflow` or `workflow_factory`, exposing multi-agent orchestration through the same FastAPI AG-UI endpoint surface.
- Workflow runs emit richer protocol signals such as `STEP_STARTED`, `STEP_FINISHED`, workflow-specific `CUSTOM` events, and `RUN_FINISHED` interrupts that clients can resume.

The workflow tutorial also states a current gap: workflow support for the .NET AG-UI integration is still "coming soon." That makes workflow orchestration a real part of the Agent Framework AG-UI story today, but primarily on the Python path.

An earlier Microsoft Developer Community walkthrough uses a Python `ChatAgent`, an `ai_function`-decorated order lookup tool, `AzureOpenAIChatClient`, and `add_agent_framework_fastapi_endpoint(app, agent, path="/chat")`. The later backend-tool-rendering documentation uses `@tool`; treat the older decorator as source-specific API context rather than the preferred current pattern.

### Backend tool rendering

The backend tool rendering tutorial shows the integration's server-side tool path. Developers define tools on the backend, the agent chooses when to call them, and AG-UI streams tool call progress and results to clients. In .NET clients, tool calls and results surface as `FunctionCallContent` and `FunctionResultContent`; in Python clients, they surface as `ToolCallContent` and `ToolResultContent`.

This makes [[backend-tool-rendering]] one of the clearest concrete Generative UI use cases for the integration.

## User Experience

Users can receive immediate feedback while an agent runs, inspect tool progress, approve sensitive actions, and see interface state synchronize with backend execution. With [[copilotkit]], a React frontend can register the endpoint as an `HttpAgent` and use AG-UI features without implementing a custom protocol client from scratch.

The state-management tutorial shows that this is not just passive synchronization. Clients can receive optimistic state deltas while the model is still preparing a tool call, then reconcile them with a committed final state. That makes [[shared-ui-state-synchronization]] part of the integration's core Generative UI value, not a side feature.

The approval tutorial shows the integration can also pause autonomy without leaving the protocol. That makes [[human-in-the-loop-tool-approval]] a first-class behavior of the stack rather than an application-specific patch.

The Python client example shows the intended interaction model: `AGUIChatClient` connects to the endpoint, the client keeps a protocol-managed thread, streamed text is printed as it arrives, and tool calls/results are rendered as separate visible events. This is a concrete implementation of [[agent-execution-observability]].

The workflow tutorial broadens that model. Instead of observing only one agent run, the client can render active workflow steps, pending interrupts, resumable requests for human input, and intermediate workflow outputs. That is the system-level substrate for [[workflow-driven-agent-ui-orchestration]].

## Evaluation

The source is documentation and does not provide measured evaluation. It does provide a feature-level comparison against direct agent usage, arguing that AG-UI is preferable when applications need remote hosting, multiple clients, SSE streaming, protocol-level state, session or thread context, and built-in approval workflows.

The Tech Community article adds adoption guidance rather than metrics: the integration is suitable for new multi-step agent projects that need observable streaming and framework flexibility, but less compelling for simple agents, working legacy streams, or environments that require guaranteed protocol stability.

## Failure Modes

- Middleware configuration can become a hidden UX dependency if approvals or state management are inconsistent across agents.
- Protocol adapters must preserve enough semantic detail from Agent Framework events for frontends to render trustworthy UI.
- Python and .NET behavior may diverge if package maturity or supported features differ.
- Workflow features can be assumed to exist on .NET because the broader AG-UI integration supports .NET, even though the current workflow tutorial explicitly limits workflow coverage to Python.
- Direct agent usage may remain simpler for single-client tools where remote hosting and protocol state are unnecessary.
- Tool registration can fail at runtime if complex parameter serialization is not aligned with the hosting application's JSON configuration.
- Development-friendly Azure credential flows can create production latency or security issues if copied without narrowing credential behavior.
- API examples can drift across documentation dates, so implementation pages should be checked against the current package version before copying decorator names or client constructor arguments.

## Related

- [[ag-ui-protocol]]
- [[copilotkit]]
- [[agent-ui-protocol-bridge]]
- [[backend-tool-rendering]]
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

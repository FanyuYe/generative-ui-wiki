---
title: Microsoft Agent Framework AG-UI Integration
created: 2026-04-20
updated: 2026-05-01
type: system
tags: [generative-ui, software-agents, framework, runtime-rendering, state-management, tool-use]
sources: [raw/articles/ag-ui-integration-with-agent-framework.md, raw/articles/backend-tool-rendering-with-ag-ui.md, raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md, raw/articles/state-management-with-ag-ui.md, raw/articles/human-in-the-loop-with-ag-ui.md, raw/articles/workflows-with-ag-ui.md, raw/articles/frontend-tool-rendering-with-ag-ui.md, raw/articles/security-considerations-for-ag-ui.md, raw/articles/testing-with-ag-ui-dojo.md, raw/articles/mcp-apps-compatibility-with-ag-ui.md, raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md, raw/articles/golden-triangle-agentic-development-ag-ui-devui-opentelemetry.md]
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
- Security guidance adds a deployment constraint: do not expose the AG-UI server directly to untrusted browser or mobile clients when a trusted frontend server can mediate protocol construction, validation, and output filtering.

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
- `HandoffBuilder` can define an explicit directed handoff topology among specialist agents, with `AgentFrameworkWorkflow` and a per-thread `workflow_factory` exposing the handoff workflow as an AG-UI endpoint.
- Approval-required tools and user information requests both surface as resumable interrupts in the handoff demo, so the frontend must preserve active-agent and case context while collecting the user's response.
- MCP Apps compatibility does not require Python-side changes. `add_agent_framework_fastapi_endpoint()` remains the backend shape while TypeScript `MCPAppsMiddleware` handles MCP Apps behavior in a CopilotKit Runtime or Node.js proxy.

The workflow tutorial states a current gap: workflow support for the .NET AG-UI integration is still "coming soon." The handoff demo sharpens the same caveat by saying the demo runs on MAF Python today and C# support for MAF + AG-UI is still in development. That makes workflow and handoff orchestration a real part of the Agent Framework AG-UI story today, but primarily on the Python path.

The MCP Apps compatibility page states a similar gap for .NET: .NET AG-UI MCP Apps compatibility documentation is still coming soon. Current documented MCP Apps compatibility is centered on Python Agent Framework endpoints plus a TypeScript middleware layer.

An earlier Microsoft Developer Community walkthrough uses a Python `ChatAgent`, an `ai_function`-decorated order lookup tool, `AzureOpenAIChatClient`, and `add_agent_framework_fastapi_endpoint(app, agent, path="/chat")`. The later backend-tool-rendering documentation uses `@tool`; treat the older decorator as source-specific API context rather than the preferred current pattern.

### MCP Apps middleware path

The MCP Apps page introduces a third integration layer around Agent Framework Python AG-UI endpoints:

- frontend application using CopilotKit or AG-UI;
- CopilotKit Runtime or Node.js proxy with `MCPAppsMiddleware`;
- Agent Framework FastAPI AG-UI endpoint.

In this path, the middleware owns MCP tool discovery, iframe-proxied resource requests, and `ui/resourceUri` resolution. The backend receives standard AG-UI traffic and does not implement Python-side MCP Apps middleware.

### Developer lifecycle stack

The Microsoft Agent Framework "Golden Triangle" DevBlog places the AG-UI integration inside a larger development loop: GitHub Models for rapid model-backed prototyping, DevUI for local workflow inspection, AG-UI for user-facing interaction, and OpenTelemetry for latency and cost feedback.

The .NET snippets matter because they show `AIAgentBuilder`, `WorkflowBuilder`, `builder.Services.AddAGUI()`, `app.MapAGUI("/", workflowAgent)`, and an OpenTelemetry tracer provider using `.AddSource("*Microsoft.Agents.AI")`. This makes the source a useful .NET-oriented complement to the newer Python-first workflow and handoff articles.

The practical design implication is captured by [[closed-loop-agentic-development-stack]]: teams should not treat AG-UI as the first debugging surface. They can inspect detailed behavior in DevUI, expose selected execution state through AG-UI, and use telemetry dashboards to optimize what neither UI surface can fully explain.

### Backend tool rendering

The backend tool rendering tutorial shows the integration's server-side tool path. Developers define tools on the backend, the agent chooses when to call them, and AG-UI streams tool call progress and results to clients. In .NET clients, tool calls and results surface as `FunctionCallContent` and `FunctionResultContent`; in Python clients, they surface as `ToolCallContent` and `ToolResultContent`.

This makes [[backend-tool-rendering]] one of the clearest concrete Generative UI use cases for the integration.

### Frontend tool rendering

The frontend tool rendering tutorial shows the matching client-side tool path. Developers register local tools on the client, AG-UI forwards their metadata to the server, the model requests execution when needed, and the client returns structured results back into the run.

In .NET, the client uses `AIFunctionFactory.Create()` plus `AsAIAgent(..., tools: ...)`, and `AGUIChatClient` forwards the resulting metadata through `ChatAgentRunOptions.ChatOptions.Tools`. The source also shows middleware inspecting those tool declarations before execution, which makes client tool exposure a governable part of the stack.

This makes [[frontend-tool-rendering]] another first-class Generative UI behavior of the system, especially for location, sensor, preference, or UI-local actions that do not belong on the server.

The security guidance qualifies that behavior. Client-declared tools are powerful, but they also create an injection and authorization surface. In production, the system should treat tool metadata and results from untrusted clients as policy-controlled inputs, not as inherently safe protocol features.

## User Experience

Users can receive immediate feedback while an agent runs, inspect tool progress, approve sensitive actions, and see interface state synchronize with backend execution. With [[copilotkit]], a React frontend can register the endpoint as an `HttpAgent` and use AG-UI features without implementing a custom protocol client from scratch.

The state-management tutorial shows that this is not just passive synchronization. Clients can receive optimistic state deltas while the model is still preparing a tool call, then reconcile them with a committed final state. That makes [[shared-ui-state-synchronization]] part of the integration's core Generative UI value, not a side feature.

The approval tutorial shows the integration can also pause autonomy without leaving the protocol. That makes [[human-in-the-loop-tool-approval]] a first-class behavior of the stack rather than an application-specific patch.

The Python client example shows the intended interaction model: `AGUIChatClient` connects to the endpoint, the client keeps a protocol-managed thread, streamed text is printed as it arrives, and tool calls/results are rendered as separate visible events. This is a concrete implementation of [[agent-execution-observability]].

The frontend-tool tutorial extends that interaction model by giving the client an active role in capability execution. The UI can provide local functions, execute them on request, and send structured results back without turning the backend into a proxy for every device-local action.

The workflow tutorial broadens that model. Instead of observing only one agent run, the client can render active workflow steps, pending interrupts, resumable requests for human input, and intermediate workflow outputs. That is the system-level substrate for [[workflow-driven-agent-ui-orchestration]].

The handoff demo makes the workflow UI more product-shaped. A React frontend renders the active specialist, case snapshot, chat stream, approval modal, and interrupt queue for a triage/refund/order workflow. That is the concrete substrate for [[multi-agent-handoff-ui-orchestration]].

The security article also reframes the intended production topology. Instead of thinking only in terms of "frontend" and "AG-UI backend," teams should often model three layers: end user, trusted frontend application server, and AG-UI server. That topology aligns well with [[trusted-frontend-mediation-for-ag-ui]].

The Dojo testing guide adds a practical validation loop around the integration. Developers can point a known AG-UI client at their endpoint, confirm feature discovery through the dropdown, and exercise protocol-visible behaviors instead of relying only on backend traces. That makes [[ag-ui-feature-coverage-testing]] a useful companion to the integration, especially during feature bring-up and debugging.

The MCP Apps compatibility page broadens the frontend/runtime role. A CopilotKit Runtime or Node.js proxy can introduce MCP-powered tools and resources without changing the Agent Framework backend, which makes [[mcp-apps-middleware-bridge]] a useful companion pattern for applications that need embedded MCP Apps alongside AG-UI agents.

The Golden Triangle article adds a broader lifecycle around those surfaces. DevUI is the local developer console for inspecting reasoning and state, AG-UI is the standard user-facing presentation layer, and OpenTelemetry supplies traces and metrics for production hardening. This helps separate [[agent-execution-observability]] for developers from product-facing Generative UI observability for users.

## Evaluation

The source is documentation and does not provide measured evaluation. It does provide a feature-level comparison against direct agent usage, arguing that AG-UI is preferable when applications need remote hosting, multiple clients, SSE streaming, protocol-level state, session or thread context, and built-in approval workflows.

The Tech Community article adds adoption guidance rather than metrics: the integration is suitable for new multi-step agent projects that need observable streaming and framework flexibility, but less compelling for simple agents, working legacy streams, or environments that require guaranteed protocol stability.

The Dojo guide extends that picture with interactive evaluation rather than performance measurement. It gives teams a way to validate the integration against a shared client and a seven-feature example matrix, but it should not be mistaken for a formal interoperability or .NET parity test.

## Failure Modes

- Middleware configuration can become a hidden UX dependency if approvals or state management are inconsistent across agents.
- Protocol adapters must preserve enough semantic detail from Agent Framework events for frontends to render trustworthy UI.
- Python and .NET behavior may diverge if package maturity or supported features differ.
- Workflow features can be assumed to exist on .NET because the broader AG-UI integration supports .NET, even though the current workflow tutorial explicitly limits workflow coverage to Python.
- Handoff demos can intensify that confusion because they look like complete product patterns while the current documented implementation is Python-first.
- Direct agent usage may remain simpler for single-client tools where remote hosting and protocol state are unnecessary.
- Tool registration can fail at runtime if complex parameter serialization is not aligned with the hosting application's JSON configuration.
- Development-friendly Azure credential flows can create production latency or security issues if copied without narrowing credential behavior.
- API examples can drift across documentation dates, so implementation pages should be checked against the current package version before copying decorator names or client constructor arguments.
- If teams expose the AG-UI endpoint directly to untrusted clients, they inherit responsibility for validating messages, state, context, tools, forwarded properties, session ownership, and streamed outputs at the backend boundary.
- Teams may assume MCP Apps are implemented in the Agent Framework backend, but the current documented path keeps MCP Apps behavior in TypeScript middleware and has no published .NET guidance yet.
- Teams may assume DevUI, AG-UI, and OpenTelemetry are interchangeable observability layers. They serve different audiences and should not expose the same data by default.

## Related

- [[ag-ui-protocol]]
- [[copilotkit]]
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
- [[closed-loop-agentic-development-stack]]

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
- raw/articles/golden-triangle-agentic-development-ag-ui-devui-opentelemetry.md

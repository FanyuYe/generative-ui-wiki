---
title: AG-UI Integration with Agent Framework
created: 2026-04-20
updated: 2026-04-20
type: source-summary
tags: [generative-ui, software-agents, framework, state-management, tool-use]
sources: [raw/articles/ag-ui-integration-with-agent-framework.md]
contradictions: []
author: Microsoft Learn
---

# AG-UI Integration with Agent Framework

## Key Points

- [[ag-ui-protocol|AG-UI]] is presented as a standardized protocol for web-based AI agent interfaces, covering remote agent hosting, SSE streaming, session management, shared state, human approvals, and custom UI rendering.
- [[microsoft-agent-framework-ag-ui-integration|Microsoft Agent Framework AG-UI Integration]] exposes agents as HTTP endpoints and adapts Agent Framework concepts into AG-UI protocol events.
- The integration supports both .NET and Python stacks: ASP.NET Core with `MapAGUI` for .NET, and FastAPI with `agent-framework-ag-ui` for Python.
- [[copilotkit|CopilotKit]] can connect React frontends to an Agent Framework AG-UI backend by registering the endpoint as an `HttpAgent`.
- The article frames AG-UI as a better fit than direct in-process agent calls when multiple clients, remote hosting, streaming observability, protocol-managed sessions, approvals, or bidirectional state synchronization are required.

## Detailed Notes

### Supported protocol features

The article lists seven AG-UI protocol capabilities: agentic chat, backend tool rendering, human-in-the-loop approvals, agentic generative UI for long-running operations, tool-based generative UI, shared state, and predictive state updates.

For Generative UI, the most important capabilities are custom UI rendering from tool calls, state synchronization, and predictive state updates. These make agent actions visible as interface state rather than only as text.

### .NET architecture

The .NET integration uses an ASP.NET Core endpoint that maps an `AIAgent` via `MapAGUI`. The endpoint receives HTTP POST requests and streams protocol events back over SSE. A middleware pipeline can add approvals, state management, and custom logic, while a protocol adapter converts between Agent Framework response updates and AG-UI events.

The .NET package is `Microsoft.Agents.AI.Hosting.AGUI.AspNetCore`, and the agent can be created from `IChatClient` implementations such as Azure OpenAI, OpenAI, or Ollama.

### Python architecture

The Python integration uses a FastAPI endpoint and an `AgentFrameworkAgent` wrapper. Orchestrators handle execution flows for normal runs, human-in-the-loop approval, and state management. Event bridges and message adapters translate between Agent Framework messages and AG-UI protocol events.

The Python package is `agent-framework-ag-ui --pre`, and the article maps `agent.run(..., stream=True)` to SSE-backed AG-UI streaming.

### Direct agent usage vs. AG-UI

Direct usage embeds the agent inside one application and leaves streaming, state, session context, and approval workflows to application code. AG-UI turns the agent into a remote service reachable by multiple web or mobile clients, with protocol-level streaming, thread/session IDs, state snapshots, and approval semantics.

### CopilotKit frontend role

CopilotKit provides frontend components for AG-UI-based agent interfaces. The article specifically describes connecting a CopilotKit React frontend to an Agent Framework AG-UI backend by registering the endpoint as an `HttpAgent`, allowing frontend tools to flow through as AG-UI client tools.

## Ingest Extraction

- UI generated or adapted: streaming chat, backend-rendered tool results, human approval UI, custom components generated from tool calls, shared client/server state views, and optimistic state updates.
- Initiator: user requests start runs; the agent chooses tool calls; developers expose agent endpoints and define tools; middleware can request user confirmation before execution.
- Generation substrate: HTTP/SSE protocol events, structured messages, tool calls, state snapshots, component rendering hooks, ASP.NET Core endpoints, and FastAPI endpoints.
- Intent capture: user messages are sent through HTTP POST requests and associated with protocol-managed session or thread identifiers.
- State representation: .NET maps structured output to state events; Python maps conversation history to thread management; both support protocol-level state synchronization.
- Guardrails: human-in-the-loop approval middleware, confirmation strategies, typed event/message adapters, and backend execution boundaries.
- Evaluation evidence: the article is documentation rather than an evaluation; it lists supported features and integration architecture but does not report usability metrics or benchmark results.
- Failure modes implied: custom UI rendering can drift from agent/tool semantics, streaming can expose partial or misleading progress, state synchronization can become inconsistent, and approval flows can add latency or friction.

## Connections

- [[ag-ui-protocol|AG-UI]] is the common protocol substrate.
- [[microsoft-agent-framework-ag-ui-integration]] is the concrete Agent Framework adapter covered by the source.
- [[copilotkit]] is the frontend component/runtime path called out for React applications.
- [[agent-ui-protocol-bridge]] captures the architecture pattern of translating agent framework events into UI protocol events.

## Open Questions

- How complete is feature parity between the .NET and Python implementations over time?
- What user experience patterns make predictive state updates understandable rather than surprising?
- How should teams test AG-UI state synchronization and approval flows in complex multi-tool agents?

## Sources

- raw/articles/ag-ui-integration-with-agent-framework.md

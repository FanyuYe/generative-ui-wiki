---
title: Backend Tool Rendering with AG-UI
created: 2026-04-20
updated: 2026-04-20
type: source-summary
tags: [generative-ui, software-agents, tool-use, runtime-rendering, observability]
sources: [raw/articles/backend-tool-rendering-with-ag-ui.md]
contradictions: []
author: Microsoft Learn
---

# Backend Tool Rendering with AG-UI

## Key Points

- The source defines [[backend-tool-rendering|Backend Tool Rendering]] as an AG-UI pattern where function tools are defined and executed on the server while tool-call progress and results stream to the client.
- The agent, not the client, decides when to call backend tools; the client receives tool execution events and can render progress or results in real time.
- In the .NET path, tools are C# methods registered through `AIFunctionFactory.Create()` and exposed through [[microsoft-agent-framework-ag-ui-integration]].
- In the Python path, tools are functions decorated with `@tool`, typed with annotations and `Field` metadata, then attached to an Agent Framework agent exposed through FastAPI.
- AG-UI exposes the tool lifecycle through events such as `TOOL_CALL_START`, `TOOL_CALL_ARGS`, `TOOL_CALL_END`, and `TOOL_CALL_RESULT`, strengthening [[ag-ui-protocol|AG-UI]] as an observable interface protocol.

## Detailed Notes

### Pattern definition

Backend tool rendering means the server owns tool definitions and execution. The AI agent decides when to invoke those tools, and AG-UI streams both the tool call and tool result to the client.

The source lists practical benefits: sensitive operations remain server-side, all clients share the same tool implementation, clients can display execution progress, and tools can change without requiring client code changes.

### .NET implementation

The .NET tutorial defines request and response types, uses `System.ComponentModel.Description` attributes to describe the tool and its parameters, creates an `AITool` with `AIFunctionFactory.Create()`, attaches the tool to an `AIAgent`, and maps the agent with `app.MapAGUI("/", agent)`.

For complex parameter types such as objects and arrays, the source calls out a specific implementation guardrail: pass `serializerOptions` from the app's configured `JsonOptions` into `AIFunctionFactory.Create()` so tool serialization matches the rest of the HTTP application.

The .NET client can detect streamed tool activity by handling `FunctionCallContent`, `FunctionResultContent`, and `ErrorContent` inside the streaming loop.

### Python implementation

The Python tutorial uses the `@tool` decorator, type annotations, `Annotated`, `Field`, and docstrings to expose functions to the agent. The complete server example attaches weather and restaurant tools to an Agent Framework agent and hosts it through `add_agent_framework_fastapi_endpoint`.

The Python client uses `AGUIChatClient` and detects `ToolCallContent` and `ToolResultContent` while streaming an agent run.

### Event lifecycle

The source shows the tool event sequence as:

1. `TOOL_CALL_START` when tool execution begins.
2. `TOOL_CALL_ARGS` as tool arguments stream, possibly in chunks.
3. `TOOL_CALL_END` when the argument stream is complete.
4. `TOOL_CALL_RESULT` when execution finishes with result content.

This lifecycle gives the UI enough structure to display progress, arguments, results, and errors without waiting for final assistant text.

## Ingest Extraction

- UI generated or adapted: tool execution indicators, streamed arguments, result displays, error messages, and agent response text that incorporates backend tool results.
- Initiator: user requests start the run; the model chooses tool calls; developers define server-side tools and metadata.
- Generation substrate: server-side function tools, typed parameters, AG-UI events, SSE streaming, Agent Framework content objects, and client rendering loops.
- Intent capture: user messages are processed by an agent that uses tool descriptions, type annotations, parameter descriptions, and docstrings to decide whether and how to call tools.
- State representation: tool call IDs link starts, arguments, endings, and results; sessions/threads preserve continuity across streamed runs.
- Guardrails: server-side tool execution, Azure credential cautions, serializer option consistency, explicit tool descriptions, structured return values, and graceful tool error handling.
- Evaluation evidence: tutorial examples demonstrate expected event flow but do not provide benchmark or usability measurements.
- Failure modes: poor tool descriptions can cause wrong calls, serialization mismatches can break complex parameters, sensitive credentials can be misconfigured, and streamed tool results can expose confusing intermediate state.

## Connections

- [[backend-tool-rendering]] is the main pattern extracted from the source.
- [[ag-ui-protocol]] provides the event vocabulary that makes tool execution visible.
- [[microsoft-agent-framework-ag-ui-integration]] provides the .NET and Python hosting path.
- [[agent-ui-protocol-bridge]] explains the broader adapter pattern that carries these tool events from backend framework to frontend UI.

## Open Questions

- What UI treatments best distinguish model reasoning, tool-call arguments, tool execution, and final answers?
- How should AG-UI clients redact or summarize sensitive tool arguments and results?
- What automated tests should verify the full `TOOL_CALL_*` lifecycle across server and client implementations?

## Sources

- raw/articles/backend-tool-rendering-with-ag-ui.md

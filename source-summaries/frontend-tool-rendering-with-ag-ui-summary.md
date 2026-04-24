---
title: Frontend Tool Rendering with AG-UI
created: 2026-04-24
updated: 2026-04-24
type: source-summary
tags: [generative-ui, software-agents, tool-use, runtime-rendering, human-computer-interaction]
sources: [raw/articles/frontend-tool-rendering-with-ag-ui.md]
contradictions: []
author: Microsoft Learn
---

# Frontend Tool Rendering with AG-UI

## Key Points

- The source defines [[frontend-tool-rendering|Frontend Tool Rendering]] as an AG-UI pattern where tool definitions live on the client, execute in the client's environment, and return results to the server so the agent can continue the run.
- In the .NET path, client tools are created with `AIFunctionFactory.Create()` and passed into `chatClient.AsAIAgent(..., tools: frontendTools)`, which automatically forwards tool metadata to the server on each request.
- The server remains implementation-blind: it sees only tool names, descriptions, and parameter schemas, then requests execution when the model decides a client-side capability is needed.
- The protocol loop is explicit: the server emits a frontend tool call, the client matches the local function, executes it, returns a result payload, and the run resumes with that result in context.
- The source positions frontend tools as the complement to [[backend-tool-rendering|Backend Tool Rendering]]: they enable device-local or UI-local actions such as GPS lookup, preferences access, or interface mutations without pushing those capabilities onto the backend.

## Detailed Notes

### Pattern definition

Frontend tool rendering means the client owns the tool implementation. The backend agent still decides when a tool should be invoked, but the execution happens in the user's environment and the result is sent back into the AG-UI run.

This makes AG-UI more than server-to-client rendering. It also becomes a protocol for safely routing local capabilities into an agent conversation without forcing the server to know implementation details for every device or UI-specific operation.

### .NET implementation

The .NET tutorial registers client tools with `AIFunctionFactory.Create()` and passes them into `AsAIAgent()`. `AGUIChatClient` automatically captures the tool definitions and maps them into `ChatAgentRunOptions.ChatOptions.Tools` for each request.

The source also highlights a middleware hook on the client-side agent builder. Middleware can inspect the run options, enumerate registered tools, and validate or modify tool exposure before the inner agent continues streaming. That makes tool declaration itself part of the policy surface, not just runtime execution.

On the response path, the client receives `FunctionCallContent`, locates the corresponding local function, deserializes arguments, executes the function, serializes the result, and sends `FunctionResultContent` back so the server-side agent can continue.

### Python implementation

The Python portion mirrors the same pattern with plain local functions and an explicit client loop. The client sends tool declarations to the server, listens for `TOOL_CALL_REQUEST`, dispatches to a local registry, then POSTs tool results or errors back to the server.

The full example makes the control flow more concrete than the .NET sketch: tool declarations are included in the request body, the client stores `thread_id`, and tool errors are returned as structured error payloads rather than collapsing into generic agent failure.

### Practical role in Generative UI

The source's example tools are intentionally local: location, sensors, preferences, and UI operations. That makes the pattern important for generative interfaces where the agent needs client-side context or needs to trigger a visible UI action in the running application.

In this model, the UI is not only passively rendering backend events. It can also expose controlled affordances back to the agent as typed local capabilities. That creates a bidirectional runtime boundary: backend tools for trusted server-side work, frontend tools for user-environment work.

## Ingest Extraction

- UI generated or adapted: client-local notifications, theme or layout changes, sensor-driven displays, location-aware responses, and UI state that depends on device or browser-local capabilities.
- Initiator: developers register client tools; the model decides when to invoke them; the end user experiences the resulting local action or context-aware response.
- Generation substrate: `AIFunctionFactory.Create()`, `AsAIAgent()`, tool metadata capture, `ChatAgentRunOptions.ChatOptions.Tools`, SSE tool-call events, and client-side function dispatch.
- Intent capture: tool descriptions, parameter schemas, and function metadata tell the model which local affordances exist and when to use them.
- State representation: tool names, parameters, and per-call result payloads provide the structured boundary between agent intent and client execution.
- Guardrails: validate tool declarations in middleware, keep permission-sensitive local tools explicit, return user-facing errors, and avoid exposing arbitrary local capabilities without policy checks.
- Evaluation evidence: tutorial examples demonstrate the protocol and expected output but do not provide formal testing or usability data.
- Failure modes: overexposed local tools can create privacy or security risk, poor descriptions can trigger wrong client actions, client/server type mismatches can break result handling, and local execution failures can confuse users if surfaced as generic assistant errors.

## Connections

- [[frontend-tool-rendering]] is the main pattern extracted from the source.
- [[ag-ui-protocol]] provides the event contract that lets the server request client-side execution.
- [[microsoft-agent-framework-ag-ui-integration]] is the system layer that carries client tool declarations and results.
- [[backend-tool-rendering]] is the closest complement: backend tools keep server-side capabilities centralized, while frontend tools surface local capabilities to the agent.

## Open Questions

- How should teams decide which tools belong on the client versus the server?
- What permission and consent patterns make local tool execution legible to users without overloading the interface?
- How should AG-UI clients test frontend tool security, schema drift, and error recovery across browser and device environments?

## Sources

- raw/articles/frontend-tool-rendering-with-ag-ui.md

---
title: Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework
created: 2026-04-20
updated: 2026-04-20
type: source-summary
tags: [generative-ui, software-agents, human-computer-interaction, observability, framework]
sources: [raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md]
contradictions: []
author: pratikpanda
---

# Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework

## Key Points

- The article frames [[ag-ui-protocol|AG-UI]] as a response to the communication gap between backend agent execution and user-facing interface feedback.
- It describes a three-phase evolution: simple request/response, custom streaming, and standardized event protocols.
- AG-UI's main value is [[agent-execution-observability]]: clients receive typed events for run start, text chunks, tool calls, tool results, errors, and run completion.
- The article explains AG-UI design choices: Server-Sent Events for simpler server-to-client streaming, protocol-managed threads for conversation state, and standardized event types for framework-agnostic clients.
- It demonstrates a Python [[microsoft-agent-framework-ag-ui-integration]] with FastAPI, `ChatAgent`, `AGUIChatClient`, and streamed `FunctionCallContent` / `FunctionResultContent`.
- The adoption guidance is pragmatic: choose AG-UI for new multi-step agent projects needing streaming observability or framework flexibility; avoid it for simple request/response agents, stable legacy systems, or risk-averse environments.

## Detailed Notes

### Why AG-UI exists

The article starts from a UX failure mode: a capable backend agent may parse files, call APIs, aggregate data, or generate visualizations, while the user only sees a spinner. The problem is not agent capability but the lack of a structured communication layer between backend execution and frontend feedback.

The article contrasts three stages:

- Simple request/response works for quick answers but fails for multi-step or long-running work.
- Custom streaming improves feedback but fragments client implementations and usually lacks standard visibility into tool calls, errors, and state.
- AG-UI standardizes event types so clients can react consistently across compliant backends.

### Protocol design decisions

The article argues for Server-Sent Events because agent UI updates are usually server-to-client, SSE travels over standard HTTP, and browser reconnection support is built in. It positions WebSockets as useful for bidirectional real-time apps but heavier than necessary for many agent response streams.

Protocol-managed threads shift conversation history and continuity away from fragile client-side arrays. Clients can hold a thread identifier while the server maintains context.

Standardized event types avoid parsing unstructured text. The core events include `RUN_STARTED`, `TEXT_MESSAGE_START`, `TEXT_MESSAGE_CONTENT`, `TEXT_MESSAGE_END`, `TOOL_CALL_START`, `TOOL_CALL_ARGS`, `TOOL_CALL_END`, `TOOL_CALL_RESULT`, `RUN_FINISHED`, and `RUN_ERROR`.

### Microsoft Agent Framework implementation

The Python example exposes a FastAPI endpoint with `add_agent_framework_fastapi_endpoint`. The architecture layers are FastAPI endpoint, `AgentFrameworkAgent` wrapper, orchestrators, `ChatAgent`, and chat client. The example agent handles customer support order lookup through a tool and streams the call/result lifecycle to clients.

The client uses `AGUIChatClient`, creates a local `ChatAgent` representation, starts a thread, and displays streamed text plus tool-call/result content. It also deduplicates tool call displays because content can arrive across multiple streaming updates.

### Mental model shift

The article emphasizes moving from "call and wait" to "subscribe to events." The UI becomes reactive: run-start events show loading, text-content events stream output, tool-call events show progress, and run-finished events close the interaction.

This mental model supports observability, transparency, and future interruptibility. It also makes protocol design part of the user experience instead of a hidden transport detail.

### Adoption guidance

The article says AG-UI is stable enough for production core use but still evolving. It recommends AG-UI for new agent projects, multi-step workflows needing visible progress, and teams that want framework flexibility. It recommends alternatives for simple agents, mature custom streaming implementations, mission-critical stability requirements, or large risk-averse deployments.

## Ingest Extraction

- UI generated or adapted: streaming chat text, loading states, tool execution indicators, intermediate results, error displays, and final responses.
- Initiator: user requests trigger runs; the agent chooses tool calls; developers define tools, instructions, endpoint paths, and client event handling.
- Generation substrate: SSE event stream, standardized AG-UI event types, protocol-managed threads, FastAPI endpoint, Agent Framework wrappers, tool content objects, and frontend rendering callbacks.
- Intent capture: user messages enter an agent thread; instructions and tool metadata guide whether the agent calls a tool.
- State representation: thread IDs, run IDs, message IDs, tool call IDs, streamed argument chunks, and structured result events.
- Guardrails: standardized event schema, protocol-managed state, explicit tool metadata, deduplication of streamed tool content, and adoption guidance about when not to use AG-UI.
- Evaluation evidence: the article provides design rationale and a working example, but no controlled usability study or benchmark.
- Failure modes: spinner-only UX, fragmented custom streaming clients, framework lock-in, protocol churn, excessive tool telemetry, hidden errors, and migration cost for legacy systems.

## Connections

- [[ag-ui-protocol]] is the standard event protocol described throughout the article.
- [[agent-execution-observability]] captures the central UX problem and benefit.
- [[agent-ui-protocol-bridge]] explains how framework-native execution becomes frontend-consumable events.
- [[backend-tool-rendering]] is demonstrated through order-status tool calls and results.
- [[microsoft-agent-framework-ag-ui-integration]] is the concrete Python implementation path in the article.

## Open Questions

- Which AG-UI events should be shown directly to users versus summarized into higher-level progress language?
- How should clients handle protocol evolution while keeping UI behavior stable?
- What design patterns prevent observable agent execution from becoming noisy or anxiety-inducing?

## Sources

- raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md

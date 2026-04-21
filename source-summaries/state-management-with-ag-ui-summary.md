---
title: State Management with AG-UI
created: 2026-04-21
updated: 2026-04-21
type: source-summary
tags: [generative-ui, software-agents, state-management, runtime-rendering, adaptive-ui]
sources: [raw/articles/state-management-with-ag-ui.md]
contradictions: []
author: Microsoft Learn
---

# State Management with AG-UI

## Key Points

- The source defines AG-UI state management as bidirectional synchronization between client and server so interfaces can carry forward structured UI state instead of regenerating everything from text alone.
- It introduces a two-phase response flow: first generate a structured state update, then emit a short natural-language summary for the user.
- The .NET path uses middleware that reads `ag_ui_state`, injects current state into the prompt, enforces JSON-schema output, and emits `STATE_SNAPSHOT` events as JSON `DataContent`.
- The Python path adds `state_schema` plus `predict_state_config`, allowing predictive `STATE_DELTA` events that stream optimistic state changes before tool execution completes.
- The article makes [[shared-ui-state-synchronization]] a concrete Generative UI mechanism layered on top of [[ag-ui-protocol|AG-UI]] and [[microsoft-agent-framework-ag-ui-integration]].

## Detailed Notes

### Core state flow

In the .NET tutorial, middleware first checks whether the client supplied `ag_ui_state` in `ChatOptions.AdditionalProperties`. If present, it runs the agent once to produce a structured state object matching a declared schema, emits that state as JSON data, then runs the agent a second time to produce a concise user-facing summary.

This separation matters because it treats machine-readable UI state and human-readable assistant communication as different outputs with different reliability requirements.

### Structured state in .NET

The C# example defines explicit state models, uses `ChatResponseFormat.ForJsonSchema<T>()` for structured output, and serializes the result into `DataContent` with `application/json`. AG-UI then turns this into a `STATE_SNAPSHOT` event for the client.

The article also notes a client-side contract: initialize state as an empty object, send state through `DataContent`, and receive updated snapshots as JSON payloads.

### Predictive state in Python

The Python section extends the pattern with `state_schema` and `predict_state_config`. These map specific tool arguments into state fields so the client can receive `STATE_DELTA` events while the model is still generating tool arguments.

That makes the UI optimistic: users can see structured interface changes before the backend tool fully completes. The final `STATE_SNAPSHOT` then confirms or corrects the resulting state.

### Guardrails and implementation constraints

The source stresses that tools should write complete state rather than partial deltas. Parameter names must align with `predict_state_config`, and instructions should explicitly tell the agent how to update existing state without accidentally dropping fields.

The article also shows that approval workflows can be combined with predictive state updates. In that flow, optimistic deltas stream first, the user approves or rejects the change, and only approved changes become final state.

## Ingest Extraction

- UI generated or adapted: structured recipe/editor state, optimistic partial state updates, final state snapshots, and conversational summaries describing state changes.
- Initiator: users initiate the run, clients provide current state, developers define schemas and tool mappings, and the model decides how to update structured state.
- Generation substrate: JSON schema responses, protocol state events, `DataContent`, JSON Patch deltas, typed state models, tool argument streams, and middleware/orchestrators.
- Intent capture: the current client state is injected into the run context, and the agent is instructed to update full state objects rather than free-form text descriptions.
- State representation: `ag_ui_state` on input, `STATE_DELTA` for predictive updates, and `STATE_SNAPSHOT` for committed state.
- Guardrails: full-state writes, schema validation, parameter-name alignment, optional approval gates, and explicit agent instructions about preserving existing state.
- Evaluation evidence: tutorial examples illustrate implementation patterns but do not provide benchmark or usability data.
- Failure modes: optimistic state can mislead users before confirmation, partial updates can accidentally drop fields, and client/server schema drift can cause inconsistent rendered interfaces.

## Connections

- [[shared-ui-state-synchronization]] is the main concept extracted from the source.
- [[ag-ui-protocol|AG-UI]] supplies the event and transport layer for state exchange.
- [[microsoft-agent-framework-ag-ui-integration]] is the hosting and middleware path used in both .NET and Python examples.
- [[agent-ui-protocol-bridge]] remains the broader pattern that makes protocol-level state visible to frontend clients.

## Open Questions

- What UX patterns best distinguish predictive state from committed state?
- How should clients roll back or annotate optimistic deltas that are later rejected?
- What schema-versioning strategy prevents client and server state drift in long-lived generative UI systems?

## Sources

- raw/articles/state-management-with-ag-ui.md

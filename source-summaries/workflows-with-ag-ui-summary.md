---
title: Workflows with AG-UI
created: 2026-04-23
updated: 2026-04-23
type: source-summary
tags: [generative-ui, software-agents, framework, observability, runtime-rendering]
sources: [raw/articles/workflows-with-ag-ui.md]
contradictions: []
author: Microsoft Learn
---

# Workflows with AG-UI

## Key Points

- The source extends AG-UI beyond single-agent chat into workflow-driven Generative UI where multiple agents and tools execute as a graph and stream step-level progress to the client.
- `AgentFrameworkWorkflow` is the key adapter: it wraps a native Agent Framework `Workflow` so AG-UI endpoints can expose multi-agent orchestration through the same HTTP and SSE surface used for single agents.
- The article adds a richer event vocabulary for workflow UIs: `STEP_STARTED`, `STEP_FINISHED`, `CUSTOM` status events, `CUSTOM` request-info events, `CUSTOM` workflow-output events, and `RUN_FINISHED` interrupts.
- Human input becomes resumable protocol state rather than a one-off UI hack. The client receives interrupts, renders a prompt or approval surface, then resumes the run with a `resume.interrupts` payload keyed by interrupt ID.
- The source is highly relevant to Generative UI because it turns workflow structure itself into visible interface state: active step, pending handoff, waiting-for-input, and intermediate output are all renderable.

## Detailed Notes

### Workflow wrapper and endpoint model

The tutorial's main abstraction is `AgentFrameworkWorkflow`, a lightweight wrapper that adapts a native `Workflow` to AG-UI. Developers can either provide a pre-built workflow instance for stateless usage or a `workflow_factory` that creates per-thread workflow state.

Registration uses the same `add_agent_framework_fastapi_endpoint(...)` surface as single-agent examples. That keeps the transport boundary stable while allowing the backend execution model to become graph-shaped instead of single-run linear.

### Step-aware event streaming

The most durable addition is the event model. Compared with single-agent runs, workflow runs emit explicit step boundaries plus custom workflow events:

- `STEP_STARTED` and `STEP_FINISHED` for progress tracking
- `CUSTOM` `status` for workflow state changes
- `CUSTOM` `request_info` when the workflow needs human input
- `CUSTOM` `workflow_output` for intermediate or final workflow data
- `RUN_FINISHED` with `interrupts` when the run pauses waiting for user action

This lets a UI show which agent or stage is active, whether the workflow is waiting, and what intermediate output has been produced so far.

### Interrupt and resume as Generative UI control flow

The source makes interrupt handling concrete. A workflow may pause because a handoff agent needs more information or because a tool requires approval. AG-UI then returns an interrupt object inside `RUN_FINISHED`, and the client sends a follow-up request with `resume.interrupts` to continue.

That matters because user intervention is no longer external to the workflow. The interface can render an input prompt, approval action, or clarification form as part of the same protocol-driven experience.

### Multi-agent handoff example

The worked example builds a customer-support workflow with triage, refund, and order agents plus approval-gated tools. A `HandoffBuilder` defines participant routing and termination conditions, while the AG-UI endpoint exposes the whole flow to the client as a stepwise event stream.

This is a practical illustration of workflow-driven Generative UI: the backend orchestrates specialized agents, and the frontend renders progress, tool use, pauses, and resumptions as user-visible interface state.

## Ingest Extraction

- UI generated or adapted: step indicators, active-agent badges, waiting-for-input prompts, approval dialogs, intermediate workflow-output panels, and resumable progress views.
- Initiator: the agent workflow initiates steps, tool calls, and interruption requests; the user may resume execution by replying to a rendered interrupt surface.
- Generation substrate: AG-UI SSE events, `AgentFrameworkWorkflow`, workflow graph execution, interrupt payloads, and resume requests.
- Intent capture: workflow state is preserved through `thread_id`, named steps, interrupt IDs, and custom event payloads rather than informal prompt text.
- State representation: execution state includes active step, workflow status, pending interrupts, and intermediate output in addition to normal message and tool events.
- Guardrails: choose `workflow` versus `workflow_factory` correctly, isolate per-thread state when needed, and keep interrupt IDs stable so resume requests map to the correct pending action.
- Evaluation evidence: implementation tutorial and event model only; no measured latency, completion, or UX outcomes.
- Failure modes: step-heavy workflows can overwhelm users, Python-specific support limits direct .NET reuse today, and poorly designed custom events can fragment client rendering logic.

## Connections

- [[workflow-driven-agent-ui-orchestration]] is the main reusable pattern extracted from the source.
- [[ag-ui-protocol|AG-UI]] expands here from single-agent event streaming into workflow-aware event semantics.
- [[microsoft-agent-framework-ag-ui-integration]] gains a new Python workflow wrapper path but still lacks .NET workflow support in the current source.
- [[agent-execution-observability]] becomes step-aware rather than run-aware only.
- [[human-in-the-loop-tool-approval]] remains relevant because approval-required tools can surface as workflow interrupts.

## Open Questions

- Which workflow events should be standardized versus left as custom extension points?
- How much workflow structure should a UI expose before users feel overloaded?
- What is the right client model for resumable workflows that may pause multiple times across long sessions?

## Sources

- raw/articles/workflows-with-ag-ui.md

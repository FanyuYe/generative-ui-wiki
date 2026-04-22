---
title: Human-in-the-Loop with AG-UI
created: 2026-04-22
updated: 2026-04-22
type: source-summary
tags: [generative-ui, software-agents, tool-use, safety, runtime-rendering]
sources: [raw/articles/human-in-the-loop-with-ag-ui.md]
contradictions: []
author: Microsoft Learn
---

# Human-in-the-Loop with AG-UI

## Key Points

- The source turns AG-UI approvals from a checklist feature into a concrete .NET implementation pattern built around `ApprovalRequiredAIFunction`.
- Approval is modeled as a protocol-mediated round trip: the backend converts approval requests into AG-UI client tool calls, the frontend renders the approval UI, and the response returns as a tool result that resumes agent execution.
- The article makes approval UX part of Generative UI architecture rather than a separate business-process layer, because approvals are rendered and resolved through the same event stream as text, tools, and state.
- It adds a practical implementation caveat: after an approval response is converted back into Agent Framework content, the temporary `request_approval` tool call and result must be removed from message history to avoid Azure OpenAI tool-call sequencing errors.
- The source strengthens [[human-in-the-loop-tool-approval]] as a reusable pattern inside the broader [[ag-ui-protocol|AG-UI]] and [[microsoft-agent-framework-ag-ui-integration]] stack.

## Detailed Notes

### Approval as a client-visible protocol loop

The tutorial's .NET flow has five stages: mark a tool as approval-required, intercept the framework's approval request content in middleware, convert it into a client tool call, let the client collect the user's decision, then convert the returned tool result back into `FunctionApprovalResponseContent` so the agent can continue.

That matters because the approval UI is not bolted on beside the agent. It is part of the same protocol path that already carries streaming text, tool activity, and state updates.

### Tool registration and approval models

The C# example wraps a tool created by `AIFunctionFactory.Create(...)` with `ApprovalRequiredAIFunction`. It then defines explicit request and response models carrying the approval ID, function name, serialized function arguments, optional message, and the final approval decision.

Those typed models make the approval step durable enough for UI rendering, auditing, and agent resumption without relying on natural-language parsing.

### Middleware translation details

The tutorial's main engineering move is middleware that translates in both directions:

- inbound client approval results become `FunctionApprovalResponseContent`
- outbound framework approval requests become AG-UI client tool calls

The source also highlights a subtle history-rewrite requirement: after approval has been resolved, the temporary `request_approval` tool call/result pair should be removed from message history. Otherwise the upstream model API can reject the conversation because tool-call bookkeeping no longer matches expected tool-result order.

### Practical safety and UX implications

This pattern is useful for sensitive operations such as sending email, moving money, deleting files, or applying important state changes. The benefit is not only safety. It also makes the pending action legible to the user before execution, which supports trust and compliance-oriented review.

The source suggests approval is especially powerful when combined with other AG-UI features: backend tool rendering makes pending work visible, and state management can stage optimistic state before a user confirms or rejects it.

## Ingest Extraction

- UI generated or adapted: approval prompts, confirmation dialogs, pending-action cards, and post-decision continuation states within the same AG-UI client.
- Initiator: the agent initiates a tool call, middleware turns it into a client-visible approval step, and the user decides whether execution may continue.
- Generation substrate: AG-UI client tool calls, `FunctionApprovalRequestContent`, `FunctionApprovalResponseContent`, JSON-serialized approval payloads, and middleware transforms.
- Intent capture: the original tool name and arguments are preserved in the approval request payload so the user can review the exact proposed action.
- State representation: pending approval state lives in typed request/response objects plus protocol events rather than only prompt text.
- Guardrails: explicit approval-required tool wrapping, history cleanup after approval resolution, typed serialization, and the ability to customize confirmation strategies.
- Evaluation evidence: documentation-level implementation guidance only; no measured usability or latency data.
- Failure modes: approval prompts can add friction, exposed tool arguments may leak sensitive data, and incorrect history rewriting can break tool-call sequencing.

## Connections

- [[human-in-the-loop-tool-approval]] is the main reusable pattern extracted from the source.
- [[ag-ui-protocol|AG-UI]] provides the event and message boundary that lets approvals behave like client-visible protocol steps.
- [[microsoft-agent-framework-ag-ui-integration]] supplies the .NET middleware and hosting surface where approval translation is implemented.
- [[backend-tool-rendering]] remains adjacent because approvals often gate the execution of backend-owned tools.
- [[shared-ui-state-synchronization]] becomes safer when optimistic state or state-changing tools can be confirmed before commitment.

## Open Questions

- Which approval UI shapes are most understandable for long-running or multi-step agent actions?
- How should clients redact or summarize risky tool arguments without hiding too much decision context?
- Should approval events become a more explicit first-class AG-UI event family rather than client-tool-call conventions?

## Sources

- raw/articles/human-in-the-loop-with-ag-ui.md

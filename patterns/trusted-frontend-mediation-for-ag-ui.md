---
title: Trusted Frontend Mediation for AG-UI
created: 2026-04-25
updated: 2026-04-28
type: pattern
tags: [generative-ui, pattern, software-agents, safety, trust, tool-use, state-management]
sources: [raw/articles/security-considerations-for-ag-ui.md, raw/articles/frontend-tool-rendering-with-ag-ui.md, raw/articles/state-management-with-ag-ui.md, raw/articles/mcp-apps-compatibility-with-ag-ui.md]
contradictions: []
status: observed
---

# Trusted Frontend Mediation for AG-UI

## Summary

Trusted Frontend Mediation for AG-UI is a Generative UI architecture pattern where a trusted application server sits between untrusted end users and the AG-UI backend. The mediator accepts only narrow user input, constructs the AG-UI protocol messages itself, controls tool exposure, validates state and context, and filters streamed outputs before they reach the UI.

## When It Appears

This pattern appears when AG-UI endpoints are consumed from browsers, mobile apps, or any environment where end users could tamper with protocol payloads.

It becomes especially important when the interface supports [[frontend-tool-rendering]], [[shared-ui-state-synchronization]], or other flows where the client can influence structured state beyond plain user text.

The MCP Apps compatibility source adds another instance: a CopilotKit Runtime or Node.js proxy with `MCPAppsMiddleware` may sit between the frontend and Agent Framework AG-UI backend, owning MCP app tool discovery, iframe resource proxying, and `ui/resourceUri` resolution.

## Mechanics

1. The end user sends limited input to an application-owned frontend server.
2. The frontend server authenticates the user and checks session ownership.
3. The frontend server constructs the AG-UI message list and other protocol fields in a controlled way.
4. The frontend server allowlists tools, validates schemas, and shapes any client-derived state or context.
5. The AG-UI backend executes the run and streams events back to the trusted frontend.
6. The trusted frontend filters, summarizes, or redacts sensitive outputs before rendering them to the user.

In direct-exposure deployments, the AG-UI server itself must absorb all of these responsibilities. The source treats that as a fallback rather than the preferred design.

For [[mcp-apps-middleware-bridge]], the mediation role also includes MCP app configuration and resource proxying. That means access control and tenant/session scoping must cover both AG-UI protocol fields and MCP app resources.

## Strengths

- Narrows the prompt-injection surface from arbitrary protocol fields to mostly user message content.
- Prevents clients from inventing unauthorized tools, hidden message roles, or unsafe state payloads.
- Creates a clear policy point for auth, session ownership, and output filtering.
- Preserves AG-UI interactivity without forcing the backend to trust browser-originated protocol structure.
- Composes well with [[human-in-the-loop-tool-approval]] for high-risk actions.

## Failure Modes

- If the mediator forwards raw protocol fields too directly, the pattern becomes security theater.
- Over-filtering can hide useful execution context and damage user trust.
- Weak schema validation can let malicious state, context, or tool metadata cross the boundary anyway.
- Sensitive tool results can still leak if only inbound validation is implemented.
- Teams may assume frontend tools are safe because they run client-side, even though their metadata and results still shape model behavior.
- MCP app resources can bypass backend policy assumptions if the middleware layer is not treated as a trusted authorization boundary.

## Related

- [[ag-ui-protocol]]
- [[microsoft-agent-framework-ag-ui-integration]]
- [[frontend-tool-rendering]]
- [[shared-ui-state-synchronization]]
- [[human-in-the-loop-tool-approval]]
- [[agent-execution-observability]]
- [[mcp-apps-middleware-bridge]]

## Sources

- raw/articles/security-considerations-for-ag-ui.md
- raw/articles/frontend-tool-rendering-with-ag-ui.md
- raw/articles/state-management-with-ag-ui.md
- raw/articles/mcp-apps-compatibility-with-ag-ui.md

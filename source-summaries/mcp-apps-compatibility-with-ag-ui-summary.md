---
title: MCP Apps Compatibility with AG-UI
created: 2026-04-28
updated: 2026-04-28
type: source-summary
tags: [generative-ui, software-agents, framework, tool-use, runtime-rendering]
sources: [raw/articles/mcp-apps-compatibility-with-ag-ui.md]
contradictions: []
author: Microsoft Learn
---

# MCP Apps Compatibility with AG-UI

## Key Points

- The source documents how Agent Framework Python AG-UI endpoints can participate in MCP Apps through a TypeScript middleware layer rather than backend changes.
- The important architecture is a three-part path: frontend, CopilotKit Runtime or Node.js proxy with `MCPAppsMiddleware`, then the Agent Framework FastAPI AG-UI endpoint.
- The Python endpoint remains a normal AG-UI backend created with `add_agent_framework_fastapi_endpoint()`.
- The middleware handles MCP tool discovery, iframe-proxied resource requests, and `ui/resourceUri` resolution.
- The page explicitly says .NET AG-UI MCP Apps compatibility documentation is still coming soon, so this should not be treated as a current .NET implementation guide.

## Detailed Notes

### Architecture

The source places MCP Apps support outside the Agent Framework backend. A CopilotKit Runtime or Node.js proxy runs `MCPAppsMiddleware` from `@ag-ui/mcp-apps-middleware`, receives frontend traffic, handles MCP Apps concerns, and forwards normal AG-UI protocol traffic to the backend.

This makes [[mcp-apps-middleware-bridge]] the durable pattern: MCP Apps are added through a trusted TypeScript mediation layer while the backend stays focused on the AG-UI run contract.

### Backend invariants

For Python, the backend uses the same `FastAPI`, `Agent`, and `add_agent_framework_fastapi_endpoint()` setup as a regular AG-UI endpoint. No Python-specific middleware, resource proxying, or `ui/resourceUri` discovery is required.

That distinction matters for [[microsoft-agent-framework-ag-ui-integration]] because it prevents developers from looking for MCP Apps behavior in the FastAPI integration itself. The backend receives standard AG-UI requests and does not know that MCP Apps are being layered into the frontend path.

### Middleware responsibilities

The TypeScript middleware is responsible for:

- registering AG-UI agent names and endpoint URLs;
- attaching MCP app configurations;
- discovering MCP-powered tools and resources;
- proxying iframe resource requests;
- resolving `ui/resourceUri` values for the frontend experience.

This overlaps with [[trusted-frontend-mediation-for-ag-ui]] because the middleware/proxy is not only convenience plumbing. It is also the place where frontend-side MCP resources are introduced into the AG-UI application boundary.

## Durable Versus Time-Sensitive

Durable:

- MCP Apps compatibility is a middleware/proxy concern rather than an Agent Framework backend concern.
- AG-UI endpoints can remain protocol-focused while adjacent UI resource and tool surfaces are composed in a frontend runtime layer.
- The bridge pattern strengthens the case for treating AG-UI application servers as explicit mediation points.

Time-sensitive:

- Package names and setup details for `@ag-ui/mcp-apps-middleware` may change quickly.
- .NET guidance is explicitly not yet documented in this source.
- The AG-UI docs link in the Microsoft page currently points to a path that may have moved, so implementation teams should verify current docs before following secondary links.

## Ingest Extraction

- Problem addressed: how to use MCP Apps with Agent Framework AG-UI endpoints.
- Practical lane: software engineering.
- Main artifact: compatibility guidance and runtime/proxy architecture.
- Core mechanism: TypeScript `MCPAppsMiddleware` handles MCP discovery, iframe-proxied resource requests, and `ui/resourceUri` resolution while forwarding standard AG-UI protocol traffic to a FastAPI Agent Framework backend.
- Evidence type: official Microsoft Learn documentation.
- Why it matters: it shows how open-ended MCP app UI can be composed around AG-UI without making the backend own MCP Apps resource mediation.
- When to use it: when an AG-UI frontend needs MCP-powered interactive tools or resources beside an Agent Framework Python endpoint.
- When not to over-apply it: when a direct AG-UI client is enough, or when a team needs documented .NET MCP Apps parity today.
- Limitations and failure modes: current guidance is Python plus TypeScript middleware, not .NET; the proxy becomes an extra production and security boundary.

## Connections

- [[ag-ui-protocol]] gains a concrete example of composing MCP Apps around AG-UI without changing the backend endpoint contract.
- [[copilotkit]] becomes more than a React UI layer here; it can host the runtime/proxy layer that introduces MCP Apps middleware.
- [[trusted-frontend-mediation-for-ag-ui]] is reinforced because the middleware/proxy owns important tool and resource boundary decisions.
- [[frontend-tool-rendering]] is related but distinct: frontend tools expose local functions to the agent, while MCP Apps middleware exposes MCP-powered tools and resources alongside an AG-UI agent.

## Open Questions

- When Microsoft publishes .NET guidance, will MCP Apps remain purely TypeScript-mediated or gain .NET-hosted middleware options?
- How should teams test MCP Apps resource resolution and iframe proxying alongside the existing [[ag-ui-feature-coverage-testing]] flow?
- What authorization model should govern MCP app resource access when the AG-UI backend is unaware of the MCP Apps layer?

## Sources

- raw/articles/mcp-apps-compatibility-with-ag-ui.md

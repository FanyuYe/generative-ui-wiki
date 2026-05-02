---
title: Blazor Server AG-UI Production Architecture
created: 2026-04-24
updated: 2026-04-24
type: query-result
tags: [artificial-intelligence, generative-ui, software-agents, framework, runtime-rendering, state-management, tool-use]
sources: [raw/articles/ag-ui-integration-with-agent-framework.md, raw/articles/backend-tool-rendering-with-ag-ui.md, raw/articles/frontend-tool-rendering-with-ag-ui.md, raw/articles/state-management-with-ag-ui.md]
contradictions: []
query: If Blazor Server runs on the same server as existing .NET backend services, how should an AG-UI-compatible server/client pair be designed, when are charts or UI actions frontend tools versus backend tools, and how do the lifetimes relate per user circuit?
---

# Blazor Server AG-UI Production Architecture

## Question

If Blazor Server runs on the same server as existing .NET backend services, how should an [[ag-ui-protocol|AG-UI]]-compatible server/client pair be designed, when are charts or UI actions [[frontend-tool-rendering|frontend tools]] versus [[backend-tool-rendering|backend tools]], and how do the lifetime scopes relate per user session circuit?

## Short Answer

The practical first production design is to host both the Blazor Server UI and the AG-UI endpoint inside the same ASP.NET Core deployment, while keeping them as separate logical layers.

- Blazor Server acts as the AG-UI client layer for each user circuit.
- The AG-UI server side is an HTTP plus SSE endpoint hosted in the same ASP.NET Core app through [[microsoft-agent-framework-ag-ui-integration]].
- Existing backend services stay behind the AG-UI server as the trusted business and integration layer.
- A separate AG-UI web server is optional, not required.

This means AG-UI is mainly a protocol boundary, not necessarily a process or machine boundary.

## Recommended Same-Host Deployment Shape

```text
Browser
  <-> SignalR circuit
Blazor Server app
  - Razor components
  - per-circuit AG-UI client session
  - per-circuit UI state
  - frontend tool handlers
  <-> HTTP POST + SSE
AG-UI endpoint in same ASP.NET Core host
  - MapAGUI(...)
  - agent middleware
  - backend tool registry
  -> existing domain services / APIs / DB / model clients
```

The main design rule is: keep the AG-UI server side UI-agnostic even when it is hosted in the same process as Blazor Server.

Do not let the AG-UI server reach sideways into Blazor component state or circuit-only services. If the agent needs page-specific state or UI-specific actions, pass them through AG-UI state or a frontend tool boundary instead.

## AG-UI Client and Server Roles

### Blazor Server as the AG-UI client

In this architecture, "client" means the side that owns the user-session interaction state for AG-UI:

- current thread or conversation ID
- active run state
- visible cards, approvals, and progress UI
- registration of client-owned tools available in this session

In Blazor Server, this AG-UI client layer should usually live in a circuit-scoped service, not directly in components.

### AG-UI server side

The AG-UI server side should own:

- agent orchestration
- middleware for approvals, state, and policy
- backend tool definitions
- calls into existing business services
- model access and durable execution behavior

Even on the same host, it should still be treated as the backend contract that any future client could use.

## Do You Need a Dedicated AG-UI Web Server?

No, not for the first practical production design.

Host AG-UI endpoints in the same ASP.NET Core application when:

- the same team owns the UI and agent stack
- current scale is manageable in one deployment
- existing backend services are already reachable from that host
- Blazor Server is the primary client

Split AG-UI into a dedicated service later only if one of these becomes important:

- agent traffic scales differently from normal page traffic
- model execution or tool execution needs separate resource isolation
- security or network policy needs a harder boundary
- non-Blazor clients must consume the same AG-UI endpoints independently
- deployment cadence for agent logic diverges from the web app

## Frontend Tool or Backend Tool?

The practical boundary is ownership and execution location.

### Backend tool

Use a backend tool when the capability belongs to trusted server-side application logic:

- database queries
- internal API calls
- report generation
- domain operations
- access to secrets or privileged infrastructure
- reusable business logic shared across all clients

In this case the AG-UI client renders progress, results, errors, or derived UI from the server-owned tool execution.

### Frontend tool

Use a frontend tool when the capability belongs to the current UI session or browser environment:

- navigate to a route in the current session
- open a modal, drawer, or tab
- set filters or select records in the current page
- read browser-local preferences
- access browser or device features through JS interop

In Blazor Server, note the extra nuance: a "frontend tool" is often circuit-owned rather than browser-native. If the action needs true browser-local APIs, the Blazor handler still has to cross the final boundary through `IJSRuntime`.

## Dynamic Charts in This Architecture

Dynamic chart generation is usually not a frontend tool call.

The common pattern is:

1. The user asks for a chart.
2. The agent calls one or more backend tools to fetch or compute the data.
3. The AG-UI response carries typed chart data, chart specification, or card payload.
4. Blazor renders that payload as a chart component.

That is mainly [[backend-tool-rendering]] plus generative UI composition.

Only treat charts as frontend-tool work when the "tool" is really about UI-session behavior, such as:

- opening a chart panel that already exists in the page
- binding a chart to a client-only selection model
- reading browser-local display preferences before rendering

The safer first design is to keep chart data generation on the backend and keep rendering in Blazor with a constrained component schema such as KPI cards, tables, and chart cards.

## Agent-Driven Page Navigation and UI Operation

If the user asks the agent to operate the currently rendered Blazor page, that is usually a frontend-tool scenario.

The tool should be semantic, not DOM-oriented.

Good examples:

- `navigate_to_route`
- `open_orders_panel`
- `set_failed_jobs_filter`
- `select_job_row`
- `show_chart_card`

Avoid exposing arbitrary click or selector tools. Blazor owns the render tree, and UI actions are much more stable when represented as semantic application operations instead of DOM manipulation.

## Recommended Lifetime Scopes

### Browser tab or circuit

A Blazor Server circuit is the natural lifetime for user-specific AG-UI client state.

This is the right scope for:

- current AG-UI thread ID
- visible assistant panel state
- pending approvals shown to the user
- session-local frontend tool registry
- UI-local state shared across components

### Blazor scoped services

Use scoped services for the AG-UI client session layer because, in server-side Blazor, scoped services persist for the lifetime of the circuit rather than being recreated on each component navigation.

This is the best home for a service such as `AgUiClientSession`.

### AG-UI thread

An AG-UI thread is the logical conversation context. It normally lives inside the circuit-scoped AG-UI client session and may be:

- one per assistant surface
- one per work area
- one per page context if the application needs stronger separation

### AG-UI run

A run is a single request or turn flowing through the AG-UI endpoint. Runs are short-lived and should not be treated as the main owner of user session state.

### AG-UI server endpoint

The endpoint is application-wide and shared across all users. It should remain stateless apart from explicit thread, run, cache, or persistence mechanisms that are not tied to one Blazor circuit implementation.

### Existing backend services

Existing application services remain the durable business layer. Backend tools should call them using normal backend lifetime rules rather than storing per-circuit UI state inside them.

## Practical Boundary Rules

- Circuit-specific state belongs in Blazor scoped services.
- Agent orchestration belongs behind the AG-UI server boundary.
- Business and integration logic belongs in existing backend services.
- Browser-only capability belongs behind frontend tools plus JS interop.
- Shared data retrieval and computation belong behind backend tools.

This separation keeps the architecture usable even if Blazor Server is later joined by another UI client.

## Recommended First Implementation Shape

1. Add AG-UI endpoints to the same ASP.NET Core application.
2. Create a scoped Blazor service that owns AG-UI thread and run interaction for the current circuit.
3. Keep backend tools close to existing application services.
4. Expose only a small semantic set of frontend tools for navigation, panel control, filtering, approval, and other UI-session actions.
5. Render agent-produced charts or cards through typed component payloads instead of arbitrary UI generation.

This is the lowest-friction path for a production .NET stack that already runs Blazor Server and backend services together.

## Extension Direction

This topic is a good base for later extensions into:

- A2UI patterns over Blazor Server
- A2A and multi-agent orchestration rendered through the same UI
- workflow-oriented agent panels and handoff displays
- approval, interruption, and resume flows across multiple agents

## Evidence and External Verification

Externally verified on 2026-04-24 against current official documentation:

- AG-UI architecture and tool concepts
- Microsoft Agent Framework AG-UI integration and frontend/backend tool tutorials
- Microsoft Blazor guidance on scoped service lifetime, `NavigationManager`, and `IJSRuntime` on server-side Blazor

## Related

- [[blazor-server-in-ag-ui-ecosystem]]
- [[ag-ui-protocol]]
- [[microsoft-agent-framework-ag-ui-integration]]
- [[backend-tool-rendering]]
- [[frontend-tool-rendering]]
- [[shared-ui-state-synchronization]]
- [[workflow-driven-agent-ui-orchestration]]

## Sources

- raw/articles/ag-ui-integration-with-agent-framework.md
- raw/articles/backend-tool-rendering-with-ag-ui.md
- raw/articles/frontend-tool-rendering-with-ag-ui.md
- raw/articles/state-management-with-ag-ui.md

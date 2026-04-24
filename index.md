# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-04-24 | Total pages: 18

## Source Summaries
<!-- Alphabetical within section. Format: - [[page-name]] — one-line summary -->
- [[ag-ui-integration-with-agent-framework-summary|AG-UI Integration with Agent Framework]] — Microsoft Learn overview of AG-UI integration for Agent Framework across .NET, Python, and CopilotKit frontends.
- [[backend-tool-rendering-with-ag-ui-summary|Backend Tool Rendering with AG-UI]] — Microsoft Learn tutorial on server-side function tools whose calls and results stream to AG-UI clients.
- [[building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework-summary|Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework]] — Microsoft Tech Community walkthrough of AG-UI rationale, event streams, and Python Agent Framework integration.
- [[frontend-tool-rendering-with-ag-ui-summary|Frontend Tool Rendering with AG-UI]] — Microsoft Learn tutorial on client-defined tools that execute locally and feed results back into AG-UI agent runs.
- [[human-in-the-loop-with-ag-ui-summary|Human-in-the-Loop with AG-UI]] — Microsoft Learn tutorial showing approval-gated tool execution as a client-visible AG-UI flow in .NET.
- [[state-management-with-ag-ui-summary|State Management with AG-UI]] — Microsoft Learn tutorial on shared state, snapshots, and predictive state updates for AG-UI applications.
- [[workflows-with-ag-ui-summary|Workflows with AG-UI]] — Microsoft Learn tutorial on exposing multi-agent workflows through AG-UI with step events, interrupts, and resume flows.

## Entities
<!-- People, organizations, products, models, places -->

## Concepts
<!-- Topics, techniques, theories, ideas -->
- [[ag-ui-protocol]] — Standardized event protocol for connecting AI agents to streaming, stateful, interactive user interfaces.
- [[agent-execution-observability]] — Ability for generated agent UIs to expose run, text, tool, error, and completion state as visible interface feedback.
- [[shared-ui-state-synchronization]] — Concept for keeping structured UI state aligned between agent backend and client through snapshots and predictive deltas.

## Patterns
<!-- Recurring Generative UI interaction or architecture patterns -->
- [[agent-ui-protocol-bridge]] — Pattern for translating framework-native agent events into frontend-consumable UI protocol events.
- [[backend-tool-rendering]] — Pattern where server-defined tool execution is streamed to the client as visible progress and result UI.
- [[frontend-tool-rendering]] — Pattern where client-defined local tools are exposed to the agent and executed on the user's device or UI runtime.
- [[human-in-the-loop-tool-approval]] — Pattern where agent-initiated tool actions are surfaced for explicit user approval before execution.
- [[workflow-driven-agent-ui-orchestration]] — Pattern where multi-agent workflows are exposed as AG-UI endpoints and rendered as stepwise, interruptible interface flows.

## Systems
<!-- Concrete products, prototypes, frameworks, or research systems -->
- [[copilotkit]] — Frontend component/runtime layer for AG-UI-based React agent interfaces.
- [[microsoft-agent-framework-ag-ui-integration]] — Adapter layer exposing Microsoft Agent Framework agents as AG-UI HTTP/SSE endpoints.

## Evaluations
<!-- Benchmarks, rubrics, studies, and quality frameworks -->

## Comparisons
<!-- Side-by-side analyses -->

## Query Results
<!-- Filed answers to notable questions -->
- [[blazor-server-in-ag-ui-ecosystem|How Blazor Server Fits into the AG-UI Ecosystem]] — Practical answer on Blazor Server as a custom AG-UI client shell around a .NET AG-UI backend, with current ecosystem caveats.

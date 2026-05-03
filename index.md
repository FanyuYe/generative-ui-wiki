# Wiki Index

> Content catalog. Every wiki page listed under its type with a one-line summary.
> Read this first to find relevant pages for any query.
> Last updated: 2026-05-03 | Total pages: 38

## Source Summaries
<!-- Alphabetical within section. Format: - [[page-name]] — one-line summary -->
- [[a2ui-v0-9-portable-framework-agnostic-generative-ui-summary|A2UI v0.9 Portable Framework-Agnostic Generative UI]] — Google Developers Blog announcement of A2UI v0.9 as a catalog-driven generated UI specification that can ride over AG-UI, MCP, WebSockets, REST, or A2A.
- [[ag-ui-integration-with-agent-framework-summary|AG-UI Integration with Agent Framework]] — Microsoft Learn overview of AG-UI integration for Agent Framework across .NET, Python, and CopilotKit frontends.
- [[ag-ui-generative-ui-specs-summary|Generative UI - Agent User Interaction Protocol]] — AG-UI documentation clarifying A2UI, Open-JSON-UI, and MCP-UI as generated UI specs while AG-UI remains the runtime interaction protocol.
- [[backend-tool-rendering-with-ag-ui-summary|Backend Tool Rendering with AG-UI]] — Microsoft Learn tutorial on server-side function tools whose calls and results stream to AG-UI clients.
- [[real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows-summary|Building a Real-Time Multi-Agent UI with AG-UI and Microsoft Agent Framework Workflows]] — Microsoft Agent Framework DevBlog walkthrough of handoff-based multi-agent AG-UI with active-agent UI, case state, approvals, and resumable interrupts.
- [[building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework-summary|Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework]] — Microsoft Tech Community walkthrough of AG-UI rationale, event streams, and Python Agent Framework integration.
- [[frontend-tool-rendering-with-ag-ui-summary|Frontend Tool Rendering with AG-UI]] — Microsoft Learn tutorial on client-defined tools that execute locally and feed results back into AG-UI agent runs.
- [[golden-triangle-agentic-development-ag-ui-devui-opentelemetry-summary|The Golden Triangle of Agentic Development with Microsoft Agent Framework]] — Microsoft Agent Framework DevBlog walkthrough tying GitHub Models, DevUI, AG-UI, and OpenTelemetry into a closed-loop developer lifecycle.
- [[human-in-the-loop-with-ag-ui-summary|Human-in-the-Loop with AG-UI]] — Microsoft Learn tutorial showing approval-gated tool execution as a client-visible AG-UI flow in .NET.
- [[mcp-apps-compatibility-with-ag-ui-summary|MCP Apps Compatibility with AG-UI]] — Microsoft Learn guidance on using TypeScript MCP Apps middleware with Agent Framework Python AG-UI endpoints.
- [[security-considerations-for-ag-ui-summary|Security Considerations for AG-UI]] — Microsoft Learn security guidance on trusted frontend mediation, protocol-field validation, and output filtering for AG-UI systems.
- [[state-management-with-ag-ui-summary|State Management with AG-UI]] — Microsoft Learn tutorial on shared state, snapshots, and predictive state updates for AG-UI applications.
- [[testing-with-ag-ui-dojo-summary|Testing with AG-UI Dojo]] — Microsoft Learn guide for using the Dojo app to interactively validate AG-UI feature coverage against example or custom Agent Framework endpoints.
- [[workflows-with-ag-ui-summary|Workflows with AG-UI]] — Microsoft Learn tutorial on exposing multi-agent workflows through AG-UI with step events, interrupts, and resume flows.

## Paper Summaries
<!-- Alphabetical within section. Format: - [[page-name]] — one-line summary -->
- [[deepseek-v4-technical-report|DeepSeek-V4: Towards Highly Efficient Million-Token Context Intelligence]] — Official DeepSeek technical report for the V4 model family, emphasizing million-token efficiency, hybrid attention, and specialist-to-distilled post-training.

## Entities
<!-- People, organizations, products, models, places -->
- [[deepseek-ai]] — Research organization behind the DeepSeek model line and the V4 architecture and infrastructure stack.

## Concepts
<!-- Topics, techniques, theories, ideas -->
- [[ag-ui-protocol]] — Standardized event protocol for connecting AI agents to streaming, stateful, interactive user interfaces.
- [[a2ui-protocol]] — Google declarative generated UI specification for catalog-constrained, streaming agent-authored interfaces across renderers and transports.
- [[agent-execution-observability]] — Ability for generated agent UIs to expose run, text, tool, error, and completion state as visible interface feedback.
- [[generative-ui-specifications]] — Layering concept distinguishing generated UI payload specifications such as A2UI, Open-JSON-UI, and MCP-UI from runtime interaction protocols such as AG-UI.
- [[shared-ui-state-synchronization]] — Concept for keeping structured UI state aligned between agent backend and client through snapshots and predictive deltas.

## Patterns
<!-- Recurring Generative UI interaction or architecture patterns -->
- [[agent-ui-protocol-bridge]] — Pattern for translating framework-native agent events into frontend-consumable UI protocol events.
- [[backend-tool-rendering]] — Pattern where server-defined tool execution is streamed to the client as visible progress and result UI.
- [[closed-loop-agentic-development-stack]] — Pattern where teams use model access, DevUI, AG-UI, and OpenTelemetry as one loop for building, debugging, presenting, and measuring agentic apps.
- [[frontend-tool-rendering]] — Pattern where client-defined local tools are exposed to the agent and executed on the user's device or UI runtime.
- [[human-in-the-loop-tool-approval]] — Pattern where agent-initiated tool actions are surfaced for explicit user approval before execution.
- [[mcp-apps-middleware-bridge]] — Pattern where a TypeScript runtime or Node.js proxy composes MCP Apps around a standard AG-UI backend.
- [[multi-agent-handoff-ui-orchestration]] — Pattern where specialist agents hand off through an explicit graph while the UI renders active ownership, case state, interrupts, and resumable actions.
- [[trusted-frontend-mediation-for-ag-ui]] — Pattern where a trusted application server constructs and filters AG-UI traffic between untrusted users and the AG-UI backend.
- [[workflow-driven-agent-ui-orchestration]] — Pattern where multi-agent workflows are exposed as AG-UI endpoints and rendered as stepwise, interruptible interface flows.

## Systems
<!-- Concrete products, prototypes, frameworks, or research systems -->
- [[copilotkit]] — Frontend component/runtime layer for AG-UI-based React agent interfaces.
- [[deepseek-v4]] — Preview DeepSeek model family focused on efficient one-million-token context, stronger reasoning, and agent-oriented runtime design.
- [[microsoft-agent-framework-ag-ui-integration]] — Adapter layer exposing Microsoft Agent Framework agents as AG-UI HTTP/SSE endpoints.

## Evaluations
<!-- Benchmarks, rubrics, studies, and quality frameworks -->
- [[ag-ui-feature-coverage-testing]] — Emerging evaluation method for interactively validating whether an AG-UI endpoint exposes the seven feature classes coherently through a real client.

## Comparisons
<!-- Side-by-side analyses -->

## Query Results
<!-- Filed answers to notable questions -->
- [[blazor-server-ag-ui-production-architecture|Blazor Server AG-UI Production Architecture]] — Same-host production design for Blazor Server as the AG-UI client layer with backend/frontend tool boundaries and circuit lifetimes.
- [[blazor-server-in-ag-ui-ecosystem|How Blazor Server Fits into the AG-UI Ecosystem]] — Practical answer on Blazor Server as a custom AG-UI client shell around a .NET AG-UI backend, with current ecosystem caveats.
- [[llm-prompt-tags-vs-harness-instructions|LLM Prompt Tags vs Harness Instructions]] — Practical distinction between harness-recognized Copilot instruction syntax and model-readable prompt structure such as XML-like tags.
- [[wcs-dashboard-generative-ui-ag-ui-blazor|WCS Dashboard Generative UI with AG-UI and Blazor]] — Practical architecture for a warehouse control system dashboard using AG-UI, Blazor, backend tools, and a constrained widget schema.

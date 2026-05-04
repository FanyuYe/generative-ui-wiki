# Wiki Log

> Chronological record of all wiki actions. Append-only.
> Format: `## [YYYY-MM-DD] action | subject`
> Actions: init, ingest, update, query, lint, archive, delete
> When this file exceeds 500 entries, rotate: rename to log-YYYY.md, start fresh.

## [2026-04-19] init | Wiki initialized
- Domain: Generative UI systems, patterns, tools, research, products, and design practices where AI models generate, adapt, or orchestrate user interfaces.
- Structure created with SCHEMA.md, index.md, log.md, dashboard.md, and .gitignore.
- Directories: raw/, entities/, concepts/, comparisons/, queries/.
- Raw source subdirectories: raw/articles/, raw/papers/, raw/transcripts/, raw/assets/.
- Domain setup: custom Generative UI schema with page types for patterns, systems, and evaluations.

## [2026-04-20] ingest | AG-UI Integration with Agent Framework
- Source: raw/articles/ag-ui-integration-with-agent-framework.md
- Created: source-summaries/ag-ui-integration-with-agent-framework-summary.md
- Created: concepts/ag-ui-protocol.md
- Created: systems/microsoft-agent-framework-ag-ui-integration.md
- Created: systems/copilotkit.md
- Created: patterns/agent-ui-protocol-bridge.md
- Updated: index.md
- Updated: log.md

## [2026-04-20] ingest | Backend Tool Rendering with AG-UI
- Source: raw/articles/backend-tool-rendering-with-ag-ui.md
- Created: source-summaries/backend-tool-rendering-with-ag-ui-summary.md
- Created: patterns/backend-tool-rendering.md
- Updated: concepts/ag-ui-protocol.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: patterns/agent-ui-protocol-bridge.md
- Updated: index.md
- Updated: log.md

## [2026-04-20] ingest | Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework
- Source: raw/articles/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework.md
- Created: source-summaries/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework-summary.md
- Created: concepts/agent-execution-observability.md
- Updated: concepts/ag-ui-protocol.md
- Updated: patterns/backend-tool-rendering.md
- Updated: patterns/agent-ui-protocol-bridge.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md

## [2026-04-21] ingest | State Management with AG-UI
- Source: raw/articles/state-management-with-ag-ui.md
- Created: source-summaries/state-management-with-ag-ui-summary.md
- Created: concepts/shared-ui-state-synchronization.md
- Updated: concepts/ag-ui-protocol.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md

## [2026-04-21] update | Broaden schema from Generative UI to Artificial Intelligence
- Updated: SCHEMA.md
- Scope broadened in place from a Generative UI-only domain to a practical Artificial Intelligence domain.
- Preserved: existing AG-UI raw sources and compiled markdown pages without content rewrites.
- Added: lean AI tag taxonomy, practical ingest rules, and `paper-summary` as a new domain-specific page type.

## [2026-04-21] query | How Blazor Server Fits into the AG-UI Ecosystem
- Created: queries/blazor-server-in-ag-ui-ecosystem.md
- Updated: index.md
- Updated: log.md
- Used existing AG-UI wiki pages plus current external documentation to distinguish explicit AG-UI support from Blazor-specific inference.

## [2026-04-22] ingest | Human-in-the-Loop with AG-UI
- Source: raw/articles/human-in-the-loop-with-ag-ui.md
- Created: source-summaries/human-in-the-loop-with-ag-ui-summary.md
- Created: patterns/human-in-the-loop-tool-approval.md
- Updated: concepts/ag-ui-protocol.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md

## [2026-04-23] ingest | Workflows with AG-UI
- Source: raw/articles/workflows-with-ag-ui.md
- Created: source-summaries/workflows-with-ag-ui-summary.md
- Created: patterns/workflow-driven-agent-ui-orchestration.md
- Updated: concepts/ag-ui-protocol.md
- Updated: concepts/agent-execution-observability.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md

## [2026-04-24] ingest | Frontend Tool Rendering with AG-UI
- Source: raw/articles/frontend-tool-rendering-with-ag-ui.md
- Created: source-summaries/frontend-tool-rendering-with-ag-ui-summary.md
- Created: patterns/frontend-tool-rendering.md
- Updated: concepts/ag-ui-protocol.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md

## [2026-04-24] query | Blazor Server AG-UI production architecture
- Created: queries/blazor-server-ag-ui-production-architecture.md
- Updated: index.md
- Updated: log.md
- Stored the same-host ASP.NET Core architecture guidance for Blazor Server as the AG-UI client layer, including backend/frontend tool boundaries and per-circuit lifetime scopes.

## [2026-04-24] ingest | DeepSeek-V4 technical report
- Source: raw/papers/deepseek-v4-technical-report-2026-04-24.md
- Downloaded: raw/papers/deepseek-v4-technical-report-2026-04-24.pdf
- Created: source-summaries/deepseek-v4-technical-report.md
- Created: entities/deepseek-ai.md
- Created: systems/deepseek-v4.md
- Updated: index.md
- Updated: log.md
- Captured the official DeepSeek-V4 PDF published on 2026-04-24 and compiled a paper summary plus durable company and model pages.

## [2026-04-25] ingest | Security Considerations for AG-UI
- Source: raw/articles/security-considerations-for-ag-ui.md
- Created: source-summaries/security-considerations-for-ag-ui-summary.md
- Created: patterns/trusted-frontend-mediation-for-ag-ui.md
- Updated: concepts/ag-ui-protocol.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md
- Captured current Microsoft Learn guidance on AG-UI trust boundaries, validation surfaces, and response filtering, then compiled the trusted-frontend mediation pattern into the AG-UI cluster.

## [2026-04-26] ingest | Testing with AG-UI Dojo
- Source: raw/articles/testing-with-ag-ui-dojo.md
- Created: source-summaries/testing-with-ag-ui-dojo-summary.md
- Created: evaluations/ag-ui-feature-coverage-testing.md
- Updated: concepts/ag-ui-protocol.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md
- Captured current Microsoft Learn guidance on AG-UI Dojo as an interactive test harness, then compiled a reusable AG-UI feature-coverage testing method into the wiki.

## [2026-04-26] query | LLM Prompt Tags vs Harness Instructions
- Created: queries/llm-prompt-tags-vs-harness-instructions.md
- Updated: index.md
- Updated: log.md
- Filed the answer distinguishing Copilot harness-recognized instruction syntax from model-readable prompt delimiters such as XML-like tags, with external verification against GitHub, VS Code, Anthropic, OpenAI, and AGENTS.md documentation.

## [2026-04-28] ingest | MCP Apps Compatibility with AG-UI
- Source: raw/articles/mcp-apps-compatibility-with-ag-ui.md
- Created: source-summaries/mcp-apps-compatibility-with-ag-ui-summary.md
- Created: patterns/mcp-apps-middleware-bridge.md
- Updated: concepts/ag-ui-protocol.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: systems/copilotkit.md
- Updated: patterns/trusted-frontend-mediation-for-ag-ui.md
- Updated: index.md
- Updated: log.md
- Captured current Microsoft Learn guidance on MCP Apps compatibility for AG-UI, then compiled the TypeScript middleware/proxy bridge pattern into the AG-UI cluster.

## [2026-04-30] ingest | Real-Time Multi-Agent UI with AG-UI and Microsoft Agent Framework Workflows
- Source: raw/articles/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows.md
- Assets: raw/assets/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows/
- Created: source-summaries/real-time-multi-agent-ui-with-ag-ui-and-agent-framework-workflows-summary.md
- Created: patterns/multi-agent-handoff-ui-orchestration.md
- Updated: concepts/ag-ui-protocol.md
- Updated: patterns/workflow-driven-agent-ui-orchestration.md
- Updated: patterns/human-in-the-loop-tool-approval.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md
- Captured the current Microsoft Agent Framework DevBlog walkthrough on AG-UI handoff workflows, downloaded its referenced images, and compiled the handoff-specific UI orchestration pattern into the AG-UI cluster.

## [2026-05-01] ingest | Golden Triangle of Agentic Development with Microsoft Agent Framework
- Source: raw/articles/golden-triangle-agentic-development-ag-ui-devui-opentelemetry.md
- Assets: raw/assets/golden-triangle-agentic-development-ag-ui-devui-opentelemetry/
- Created: source-summaries/golden-triangle-agentic-development-ag-ui-devui-opentelemetry-summary.md
- Created: patterns/closed-loop-agentic-development-stack.md
- Updated: concepts/agent-execution-observability.md
- Updated: systems/microsoft-agent-framework-ag-ui-integration.md
- Updated: index.md
- Updated: log.md
- Captured the Microsoft Agent Framework DevBlog walkthrough on GitHub Models, DevUI, AG-UI, and OpenTelemetry, then compiled the developer lifecycle pattern into the AG-UI cluster.

## [2026-05-02] ingest | AG-UI Generative UI Specs
- Source: raw/articles/ag-ui-generative-ui-specs.md
- Assets: raw/assets/ag-ui-generative-ui-specs/
- Created: source-summaries/ag-ui-generative-ui-specs-summary.md
- Created: concepts/generative-ui-specifications.md
- Updated: concepts/ag-ui-protocol.md
- Updated: index.md
- Updated: log.md
- Captured the AG-UI documentation page that distinguishes A2UI, Open-JSON-UI, and MCP-UI from the AG-UI runtime protocol, then compiled the specification-versus-protocol layer boundary into the wiki.

## [2026-05-02] query | WCS Dashboard Generative UI with AG-UI and Blazor
- Created: queries/wcs-dashboard-generative-ui-ag-ui-blazor.md
- Updated: index.md
- Updated: log.md
- Filed the architecture answer for a warehouse control system dashboard that uses AG-UI as the runtime protocol, Blazor as a controlled renderer, backend tools for operational facts, and a constrained WCS widget schema for dynamic charts and data grids.

## [2026-05-03] ingest | A2UI v0.9 Portable Framework-Agnostic Generative UI
- Source: raw/articles/a2ui-v0-9-portable-framework-agnostic-generative-ui.md
- Assets: raw/assets/a2ui-v0-9-portable-framework-agnostic-generative-ui/
- Created: source-summaries/a2ui-v0-9-portable-framework-agnostic-generative-ui-summary.md
- Created: concepts/a2ui-protocol.md
- Updated: concepts/generative-ui-specifications.md
- Updated: concepts/ag-ui-protocol.md
- Updated: index.md
- Updated: log.md
- Captured the Google Developers Blog A2UI v0.9 announcement and article visual assets, then compiled A2UI as a concrete generated-UI specification layer that can work alongside AG-UI rather than replacing it.

## [2026-05-04] query ingest | WCS Dashboard AG-UI Widget State Events
- Source: raw/transcripts/wcs-dashboard-ag-ui-widget-state-event-session-2026-05-04.md
- Updated: queries/wcs-dashboard-generative-ui-ag-ui-blazor.md
- Updated: index.md
- Updated: log.md
- Ingested the follow-up session clarifying that WCS dashboard widget choice and datasource binding should be a structured dashboard state carried by `STATE_SNAPSHOT`, with optional `STATE_DELTA`, while backend tool events remain progress/result visibility rather than the final render contract.

## [2026-05-04] ingest | CopilotKit Generative UI for Agentic Apps
- Source: raw/articles/copilotkit-generative-ui-for-agentic-apps.md
- Assets: raw/assets/copilotkit-generative-ui-for-agentic-apps/
- Created: source-summaries/copilotkit-generative-ui-for-agentic-apps-summary.md
- Created: comparisons/generative-ui-control-spectrum.md
- Updated: concepts/generative-ui-specifications.md
- Updated: patterns/frontend-tool-rendering.md
- Updated: systems/copilotkit.md
- Updated: index.md
- Updated: log.md
- Captured the maintained CopilotKit monorepo Generative UI showcase and visual assets, then compiled the controlled, declarative, and open-ended Generative UI control spectrum into the wiki.

---
title: Testing with AG-UI Dojo
created: 2026-04-26
updated: 2026-04-26
type: source-summary
tags: [generative-ui, software-agents, evaluation, observability, tool-use]
sources: [raw/articles/testing-with-ag-ui-dojo.md]
contradictions: []
author: Microsoft Learn
---

# Testing with AG-UI Dojo

## Key Points

- The source introduces AG-UI Dojo as an interactive environment for testing Microsoft Agent Framework agents that implement the AG-UI protocol.
- Its most durable contribution is not the local setup itself, but the explicit seven-feature coverage model: one example endpoint per AG-UI capability.
- The guide turns protocol support into something developers can inspect visually rather than infer from backend code alone.
- The current implementation path in the article is Python-first: FastAPI backend examples, `add_agent_framework_fastapi_endpoint`, and a Dojo frontend started separately with `pnpm`.
- The article also gives a minimal validation loop for custom agents: register an endpoint, point Dojo at the server, and confirm the endpoint appears and behaves correctly in the UI.

## Detailed Notes

### What problem the source addresses

The article addresses a gap in AG-UI development workflows: developers may have an endpoint that compiles and streams, but still lack an easy way to verify whether the UI-visible protocol behaviors actually work across chat, tools, approvals, state, and generative UI flows.

Dojo is presented as a visual test harness for that gap. Instead of reading raw SSE traffic or writing a custom frontend for every experiment, teams can connect a known UI and inspect how their agent behaves under the full protocol surface.

### Feature coverage model

The guide organizes testing around seven example endpoints:

- `/agentic_chat`
- `/backend_tool_rendering`
- `/human_in_the_loop`
- `/agentic_generative_ui`
- `/tool_based_generative_ui`
- `/shared_state`
- `/predictive_state_updates`

That matters because it turns AG-UI from a vague "supports the protocol" claim into a concrete checklist of observable behaviors. It also aligns well with the existing wiki cluster, where those features have already been documented across separate pages.

### Development setup

The setup is split into two runtimes:

- backend example agents run on `http://localhost:8888`
- Dojo frontend runs on `http://localhost:3000`

The backend examples live in the AG-UI repository under `integrations/microsoft-agent-framework/python/examples`, while the frontend lives under `apps/dojo`. The article uses `uv` for Python dependencies and `pnpm` for the frontend.

This is a useful system boundary for understanding AG-UI testing: the backend and frontend are intentionally separate, so developers can validate protocol compatibility rather than only in-process framework behavior.

### Testing your own agents

The source gives a small but durable recipe for custom-agent validation:

1. Create an Agent Framework agent.
2. Register it as an AG-UI endpoint with `add_agent_framework_fastapi_endpoint`.
3. Start the server.
4. Open Dojo and point it at the server URL.
5. Select the endpoint from the dropdown and exercise it interactively.

This is less about tutorial completeness and more about a practical engineering loop: expose a candidate AG-UI surface, then validate whether a standard client can discover it, stream it, and render its features coherently.

### Limits and caveats

The page is explicitly useful for AG-UI testing, but its current evidence is operational rather than evaluative in the benchmark sense. It does not define pass/fail metrics, latency thresholds, or formal conformance tests.

It is also Python-first today. Although the article shows a language selector, the captured instructions center on FastAPI examples and the Python integration tree. That means the page is strong evidence for AG-UI testing patterns, but not for current .NET Dojo parity.

## Ingest Extraction

- Problem addressed: how to interactively test and explore AG-UI protocol behavior for Agent Framework agents.
- Practical lane: software engineering.
- Main artifact: testing workflow and feature-coverage method grounded in a shared sample app.
- Core mechanism: connect a known AG-UI frontend to example or custom endpoints and inspect all seven protocol features through visible UI behavior.
- Evidence type: official documentation plus runnable example application structure.
- Why it matters: AG-UI quality depends on what the client can discover, stream, render, and reconcile, not just what the backend emits in theory.
- When to use it: during initial AG-UI integration, feature bring-up, regression checks, or debugging protocol-visible behavior.
- When not to over-apply it: it does not replace production telemetry, load testing, or formal security validation.
- Limitations and failure modes: current guidance is Python-first, interactive rather than automated, and vulnerable to false confidence if teams validate only happy-path demos.

## Connections

- [[ag-ui-feature-coverage-testing]] is the reusable evaluation method extracted from the source.
- [[ag-ui-protocol]] is the protocol surface that Dojo helps validate interactively.
- [[microsoft-agent-framework-ag-ui-integration]] is the concrete system under test.
- [[backend-tool-rendering]], [[human-in-the-loop-tool-approval]], [[shared-ui-state-synchronization]], and [[workflow-driven-agent-ui-orchestration]] map to the feature-specific behaviors developers can exercise in Dojo.
- [[agent-execution-observability]] matters because visual testing depends on whether protocol events are legible in the client.

## Open Questions

- What would a stronger automated conformance layer on top of Dojo look like?
- Which AG-UI failures are best caught visually in Dojo versus with protocol fixtures or snapshot tests?
- How should .NET-specific AG-UI testing guidance evolve once Dojo examples cover that path directly?

## Sources

- raw/articles/testing-with-ag-ui-dojo.md

---
title: WCS Dashboard Generative UI with AG-UI and Blazor
created: 2026-05-02
updated: 2026-05-04
type: query-result
tags: [artificial-intelligence, generative-ui, software-agents, software-engineering, applications, runtime-rendering, state-management, tool-use, trust]
sources: [raw/articles/ag-ui-generative-ui-specs.md, raw/articles/ag-ui-integration-with-agent-framework.md, raw/articles/backend-tool-rendering-with-ag-ui.md, raw/articles/frontend-tool-rendering-with-ag-ui.md, raw/articles/state-management-with-ag-ui.md, raw/articles/security-considerations-for-ag-ui.md, raw/transcripts/wcs-dashboard-ag-ui-widget-state-event-session-2026-05-04.md]
contradictions: []
query: Considering generative UI specification choices using AG-UI and a .NET frontend stack such as Blazor, how should a warehouse control system dashboard dynamically render charts and data grids in response to user questions such as "What is today's picking rate and any major issues?"
---

# WCS Dashboard Generative UI with AG-UI and Blazor

## Question

The user is considering generative UI specification choices using [[ag-ui-protocol|AG-UI]] and a .NET frontend stack such as Blazor for a warehouse control system dashboard. The target experience is a dashboard that can dynamically render charts and data grids based on an agent response to user questions such as:

> What is today's picking rate and any major issues?

The practical decision is whether to use AG-UI alone, a generative UI specification such as A2UI, Open-JSON-UI, or MCP-UI, or a narrower application-owned schema for WCS dashboard components.

## Short Answer

Use [[ag-ui-protocol|AG-UI]] as the runtime protocol, Blazor as the controlled renderer, backend tools for warehouse facts and calculations, and a narrow WCS-specific widget schema as the first generated UI payload format.

Do not let the model directly generate arbitrary UI. The model should choose from approved component types and fill typed payloads that Blazor validates and renders.

The concrete AG-UI event for the committed widget payload is `STATE_SNAPSHOT`. If the UI needs incremental predictive updates, use `STATE_DELTA`. Do not make the final widget plan a frontend tool call.

## Recommended Architecture

```text
User
  -> Blazor Server dashboard
     -> trusted AG-UI client/session layer
        -> AG-UI endpoint in ASP.NET Core
           -> LLM or agent runtime
           -> backend tools
              -> WCS database / telemetry / alerts / equipment APIs

AG-UI stream back:
  text answer
  tool progress
  typed dashboard widget payloads
  state updates
  errors or approvals if needed

Blazor renders:
  approved chart, grid, KPI, alert, and drilldown components
```

AG-UI is the bidirectional interaction protocol. It carries the conversation, streaming progress, backend tool calls, tool results, state updates, and final answer. It is not itself the widget schema. [[generative-ui-specifications]] captures this layer boundary: A2UI, Open-JSON-UI, MCP-UI, or a custom schema describe generated UI payloads, while AG-UI connects the agent to the application at runtime.

## Backend Tools for WCS Facts

The warehouse control system facts should be behind [[backend-tool-rendering|backend tools]], because they involve trusted operational data, business rules, databases, equipment telemetry, and privileged integrations.

Useful WCS tools might include:

- `get_picking_rate(date, warehouse_area)`
- `get_active_exceptions(severity, since)`
- `get_order_backlog(area)`
- `get_equipment_status(zone)`
- `get_recent_alerts(severity, since)`
- `get_wave_progress(wave_id)`

For the user question "What is today's picking rate and any major issues?", the agent should call tools such as picking-rate, active-exceptions, and equipment-status retrieval, then summarize the operational state and return validated dashboard widgets.

## Blazor as Controlled Renderer

Blazor should render a small set of approved dashboard components:

- KPI card
- line chart
- bar chart
- data grid
- alert list
- equipment status panel
- drilldown link or action button

The LLM can choose which components to return, but Blazor owns the actual component implementation, validation, layout, styling, and permission checks.

Example generated payload:

```json
{
  "answer": "Today's picking rate is 842 lines/hour, down 11% from the 7-day average. The main issue is conveyor zone B3 faults causing staging delays.",
  "widgets": [
    {
      "type": "kpi",
      "title": "Picking Rate",
      "value": 842,
      "unit": "lines/hour",
      "trend": "down",
      "deltaPercent": -11
    },
    {
      "type": "lineChart",
      "title": "Picking Rate Today",
      "x": "time",
      "y": "linesPerHour",
      "dataSource": "tool:get_picking_rate"
    },
    {
      "type": "dataGrid",
      "title": "Major Issues",
      "columns": ["severity", "area", "issue", "startedAt", "impact"],
      "rows": []
    }
  ]
}
```

This is not free-form UI generation. It is constrained generative composition over a known dashboard vocabulary.

## Exact AG-UI Event Contract

The widget choice and datasource binding should be a structured dashboard state object carried by AG-UI state events.

Use `STATE_SNAPSHOT` when the agent has produced the committed dashboard model that Blazor should render. Use `STATE_DELTA` only if the client supports incremental updates, usually as JSON Patch-style changes to the current dashboard state. Keep backend tool events for visibility into data fetching and computation; they are not the canonical format for the final widget layout.

Minimal committed event:

```json
{
  "type": "STATE_SNAPSHOT",
  "snapshot": {
    "schema": "wcs.dashboard.v1",
    "answer": "Today's picking rate is 842 lines/hour. Main issue: conveyor B3 faults.",
    "datasets": {
      "pickRateToday": [
        { "time": "08:00", "linesPerHour": 790 },
        { "time": "09:00", "linesPerHour": 842 }
      ],
      "majorIssues": [
        { "severity": "high", "area": "B3", "issue": "Conveyor fault", "impact": "staging delay" }
      ]
    },
    "widgets": [
      {
        "id": "w1",
        "type": "kpi",
        "title": "Picking Rate",
        "dataSource": "pickRateToday",
        "valueField": "linesPerHour",
        "unit": "lines/hour"
      },
      {
        "id": "w2",
        "type": "dataGrid",
        "title": "Major Issues",
        "dataSource": "majorIssues",
        "columns": ["severity", "area", "issue", "impact"]
      }
    ]
  }
}
```

Minimal delta event:

```json
{
  "type": "STATE_DELTA",
  "delta": [
    {
      "op": "add",
      "path": "/widgets/-",
      "value": {
        "id": "w3",
        "type": "barChart",
        "title": "Issues by Area",
        "dataSource": "issuesByArea",
        "xField": "area",
        "yField": "count"
      }
    }
  ]
}
```

In the .NET Agent Framework path, the backend should constrain the model with `ChatResponseFormat.ForJsonSchema<DashboardState>()`, validate the result, then emit JSON `DataContent` with media type `application/json`. The AG-UI hosting layer maps that structured JSON to a `STATE_SNAPSHOT`.

`CUSTOM` is a fallback for application-specific one-off signals, such as a workflow output, toast, or request for additional user input. It is weaker than `STATE_SNAPSHOT` for a WCS dashboard because widget layout, datasource references, and visible dashboard state need a durable state model that can be validated and reconciled.

Minimal Blazor event handling shape:

```csharp
public sealed record DashboardState(
    string Schema,
    string Answer,
    Dictionary<string, JsonElement[]> Datasets,
    List<DashboardWidget> Widgets);

public sealed record DashboardWidget(
    string Id,
    string Type,
    string Title,
    string DataSource,
    string[]? Columns,
    string? ValueField,
    string? Unit);

async Task OnAgUiEvent(JsonElement ev)
{
    var type = ev.GetProperty("type").GetString();

    if (type == "STATE_SNAPSHOT")
    {
        dashboard = ev.GetProperty("snapshot")
            .Deserialize<DashboardState>(jsonOptions);

        await InvokeAsync(StateHasChanged);
    }

    // If STATE_DELTA is supported, apply RFC 6902 JSON Patch to dashboard.
}
```

Minimal Blazor rendering shape:

```razor
@if (dashboard is not null)
{
    <p>@dashboard.Answer</p>

    @foreach (var widget in dashboard.Widgets)
    {
        if (widget.Type == "kpi")
        {
            <KpiCard Widget="widget"
                     Rows="dashboard.Datasets[widget.DataSource]" />
        }
        else if (widget.Type == "dataGrid")
        {
            <WcsDataGrid Widget="widget"
                         Rows="dashboard.Datasets[widget.DataSource]" />
        }
    }
}
```

## Specification Choice

| Choice | Recommendation for WCS dashboard |
| --- | --- |
| Custom typed WCS schema | Best first production choice. It is testable, secure, and easy for Blazor to render. |
| A2UI | Consider later if portable, platform-agnostic generated layouts become important. |
| Open-JSON-UI | Watch as an ecosystem option, but do not make it the first dependency unless the .NET renderer story is clear. |
| MCP-UI | Use when iframe/resource-style MCP user experiences are central, not merely for KPI charts and grids. |
| Free-form HTML or JavaScript from the LLM | Avoid for production WCS dashboards. |

The main reason to start with a custom WCS schema is that warehouse operations dashboards need reliability, authorization, auditability, and stable visual behavior more than maximum open-ended generation.

## Frontend Tool Boundary

Use [[frontend-tool-rendering|frontend tools]] for UI-session actions, not for fetching trusted warehouse metrics.

Good frontend tools:

- `show_issue_drilldown(issue_id)`
- `set_dashboard_filter(area)`
- `navigate_to_wave(wave_id)`
- `open_equipment_panel(equipment_id)`
- `pin_widget(widget_id)`

Avoid exposing arbitrary DOM or click tools. Blazor should expose semantic application operations instead.

## State and Security

Use [[shared-ui-state-synchronization]] for dashboard context that the agent needs to reason over, such as current warehouse, selected area, active filters, shift, and visible dashboard mode.

Use [[trusted-frontend-mediation-for-ag-ui]] as the security pattern. End users should not be able to send arbitrary AG-UI protocol fields, tools, state, or context to the backend. The trusted application layer should authenticate the user, construct the AG-UI request, allowlist backend and frontend tools, validate widget payloads, and redact sensitive tool results before rendering.

## Practical First Implementation

1. Host the Blazor app and AG-UI endpoint in the same ASP.NET Core deployment if that matches the existing WCS stack.
2. Define backend tools around WCS read models and operational summaries.
3. Define a small `DashboardWidget` schema with versioning and strict validation.
4. Let the agent produce a schema-valid dashboard state such as `{ answer, datasets, widgets, followUpActions }` instead of arbitrary UI.
5. Render each widget through approved Blazor components.
6. Add frontend tools only for semantic UI operations such as drilldown, filter changes, navigation, and panel opening.
7. Log the tool calls, widget schema version, and rendered widget set for audit and regression testing.

## Follow-Up Directions

- Refine the exact WCS dashboard widget schema and its `STATE_SNAPSHOT`/`STATE_DELTA` versioning rules.
- Decide whether the first implementation should be Blazor Server, Blazor Web App, or a separate React/CopilotKit client.
- Add an evaluation checklist for generated dashboard correctness, authorization, and operator trust.
- Compare custom WCS schema against A2UI, Open-JSON-UI, and MCP-UI once .NET renderer support is clearer.
- Extend the architecture toward multi-agent WCS workflows such as picking, replenishment, equipment health, and exception triage.

## Related

- [[ag-ui-protocol]]
- [[generative-ui-specifications]]
- [[blazor-server-ag-ui-production-architecture]]
- [[backend-tool-rendering]]
- [[frontend-tool-rendering]]
- [[shared-ui-state-synchronization]]
- [[trusted-frontend-mediation-for-ag-ui]]
- [[agent-execution-observability]]

## Sources

- raw/articles/ag-ui-generative-ui-specs.md
- raw/articles/ag-ui-integration-with-agent-framework.md
- raw/articles/backend-tool-rendering-with-ag-ui.md
- raw/articles/frontend-tool-rendering-with-ag-ui.md
- raw/articles/state-management-with-ag-ui.md
- raw/articles/security-considerations-for-ag-ui.md
- raw/transcripts/wcs-dashboard-ag-ui-widget-state-event-session-2026-05-04.md

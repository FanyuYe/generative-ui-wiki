# WCS Dashboard AG-UI Widget State Event Session

Date: 2026-05-04
Type: conversation transcript / synthesis
Topic: AG-UI, WCS dashboards, Blazor, constrained widget schemas, state events

## Context

The user followed up on the existing WCS dashboard architecture question:

- If a custom render JSON schema requires the LLM to choose between predefined widgets such as data grids and charts, and the widget data comes from backend databases, which AG-UI capability does this fit into?
- The user suspected it should not be frontend tool calling.
- The user then asked for the exact response format and AG-UI event the client should consume, with a minimal Blazor example.

## Answer Captured

The durable conclusion is that the widget choice and datasource binding should be treated as structured application state, not as a frontend tool call and not as JSON embedded in normal assistant text.

For a WCS dashboard:

- Backend tools fetch trusted WCS facts, KPIs, alerts, exceptions, and equipment status.
- The agent uses those tool results to produce a typed dashboard state object.
- AG-UI should carry the committed widget plan through a `STATE_SNAPSHOT` event.
- If incremental streaming updates are needed, AG-UI can carry partial widget-state changes through `STATE_DELTA`.
- `TOOL_CALL_START`, `TOOL_CALL_ARGS`, `TOOL_CALL_END`, and `TOOL_CALL_RESULT` remain useful for showing backend tool execution progress, but they are not the canonical event for the final renderable widget plan.
- `CUSTOM` events are better for one-off workflow or application signals, not the main durable dashboard render state.

## Minimal Event Shape

Committed dashboard state:

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

Incremental widget-state update:

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

## Minimal Blazor Shape

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

## .NET Agent Framework Mapping

In the .NET Agent Framework path, the server should constrain the model with `ChatResponseFormat.ForJsonSchema<DashboardState>()`, validate the object, then emit JSON `DataContent` with `application/json`. The AG-UI hosting layer maps that JSON data content to `STATE_SNAPSHOT`.

## Sources Mentioned During Session

- AG-UI Events: https://docs.ag-ui.com/concepts/events
- AG-UI State Management: https://docs.ag-ui.com/concepts/state
- Microsoft Agent Framework AG-UI State Management: https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/state-management

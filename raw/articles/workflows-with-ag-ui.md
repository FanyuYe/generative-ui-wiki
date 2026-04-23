Title: Workflows with AG-UI

URL Source: https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/workflows

Markdown Content:
Note

Workflow support for the .NET AG-UI integration is coming soon.

This tutorial shows you how to expose Agent Framework workflows through an AG-UI endpoint. Workflows orchestrate multiple agents and tools in a defined execution graph, and the AG-UI integration streams rich workflow events — step tracking, activity snapshots, interrupts, and custom events — to web clients in real time.

Before you begin, ensure you have:

*   Python 3.10 or later
*   `agent-framework-ag-ui` installed
*   Familiarity with the [Getting Started](https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/getting-started) tutorial
*   Basic understanding of Agent Framework [workflows](https://learn.microsoft.com/en-us/agent-framework/workflows/)

Use a workflow instead of a single agent when you need:

*   **Multi-agent orchestration**: Route tasks between specialized agents (for example, triage → refund → order)
*   **Structured execution steps**: Track progress through defined stages with `STEP_STARTED` / `STEP_FINISHED` events
*   **Interrupt / resume flows**: Pause execution to collect human input or approvals, then resume
*   **Custom event streaming**: Emit domain-specific events (`request_info`, `status`, `workflow_output`) to the client

`AgentFrameworkWorkflow` is a lightweight wrapper that adapts a native `Workflow` to the AG-UI protocol. You can provide either a pre-built workflow instance or a factory that creates a new workflow per thread.

Use a direct instance when a single workflow object can safely serve all requests (for example, stateless pipelines):

```
from agent_framework import Workflow
from agent_framework.ag_ui import AgentFrameworkWorkflow

workflow = build_my_workflow()  # returns a Workflow

ag_ui_workflow = AgentFrameworkWorkflow(
    workflow=workflow,
    name="my-workflow",
    description="Single-instance workflow.",
)
```

Use `workflow_factory` when each conversation thread needs its own workflow state. The factory receives the `thread_id` and returns a fresh `Workflow`:

```
from agent_framework.ag_ui import AgentFrameworkWorkflow

ag_ui_workflow = AgentFrameworkWorkflow(
    workflow_factory=lambda thread_id: build_my_workflow(),
    name="my-workflow",
    description="Thread-scoped workflow.",
)
```

Important

You must pass **either**`workflow`**or**`workflow_factory`, not both. The wrapper raises a `ValueError` if both are provided.

Register the workflow with `add_agent_framework_fastapi_endpoint` the same way you would register a single agent:

```
from fastapi import FastAPI
from agent_framework.ag_ui import (
    AgentFrameworkWorkflow,
    add_agent_framework_fastapi_endpoint,
)

app = FastAPI(title="Workflow AG-UI Server")

ag_ui_workflow = AgentFrameworkWorkflow(
    workflow_factory=lambda thread_id: build_my_workflow(),
    name="handoff-demo",
    description="Multi-agent handoff workflow.",
)

add_agent_framework_fastapi_endpoint(
    app=app,
    agent=ag_ui_workflow,
    path="/workflow",
)
```

You can also pass a bare `Workflow` directly — the endpoint auto-wraps it in `AgentFrameworkWorkflow`:

```
add_agent_framework_fastapi_endpoint(app, my_workflow, "/workflow")
```

Workflow runs emit a richer set of AG-UI events compared to single-agent runs:

| Event | When emitted | Description |
| --- | --- | --- |
| `RUN_STARTED` | Run begins | Marks the start of workflow execution |
| `STEP_STARTED` | An executor or superstep begins | `step_name` identifies the agent or step (for example, `"triage_agent"`) |
| `TEXT_MESSAGE_*` | Agent produces text | Standard streaming text events |
| `TOOL_CALL_*` | Agent invokes a tool | Standard tool call events |
| `STEP_FINISHED` | An executor or superstep completes | Closes the step for UI progress tracking |
| `CUSTOM` (`status`) | Workflow state changes | Contains `{"state": "<value>"}` in the event value |
| `CUSTOM` (`request_info`) | Workflow requests human input | Contains the request payload for the client to render a prompt |
| `CUSTOM` (`workflow_output`) | Workflow produces output | Contains the final or intermediate output data |
| `RUN_FINISHED` | Run completes | May include `interrupts` if the workflow is waiting for input |

Clients can use `STEP_STARTED` / `STEP_FINISHED` events to render progress indicators showing which agent is currently active.

Workflows can pause execution to collect human input or tool approvals. The AG-UI integration handles this through the interrupt/resume protocol.

1.   During execution, the workflow raises a pending request (for example, a `HandoffAgentUserRequest` asking for more details, or a tool with `approval_mode="always_require"`).

2.   The AG-UI bridge emits a `CUSTOM` event with `name="request_info"` containing the request data.

3.   The run finishes with a `RUN_FINISHED` event whose `interrupts` field contains a list of pending request objects:

```
{
  "type": "RUN_FINISHED",
  "threadId": "abc123",
  "runId": "run_xyz",
  "interrupts": [
    {
      "id": "request-id-1",
      "value": { "request_type": "HandoffAgentUserRequest", "data": "..." }
    }
  ]
}
```
4.   The client renders UI for the user to respond (a text input, an approval button, etc.).

The client sends a new request with the `resume` payload containing the user's responses keyed by interrupt ID:

```
{
  "threadId": "abc123",
  "messages": [],
  "resume": {
    "interrupts": [
      {
        "id": "request-id-1",
        "value": "User's response text or approval decision"
      }
    ]
  }
}
```

The server converts the resume payload into workflow responses and continues execution from where it paused.

This example shows a customer-support workflow with three agents that hand off work to each other, use tools requiring approval, and request human input when needed.

```
"""AG-UI workflow server with multi-agent handoff."""

import os

from agent_framework import Agent, Message, Workflow, tool
from agent_framework.ag_ui import (
    AgentFrameworkWorkflow,
    add_agent_framework_fastapi_endpoint,
)
from agent_framework.azure import AzureOpenAIResponsesClient
from agent_framework.orchestrations import HandoffBuilder
from azure.identity import AzureCliCredential
from fastapi import FastAPI
from fastapi.middleware.cors import CORSMiddleware

@tool(approval_mode="always_require")
def submit_refund(refund_description: str, amount: str, order_id: str) -> str:
    """Capture a refund request for manual review before processing."""
    return f"Refund recorded for order {order_id} (amount: {amount}): {refund_description}"

@tool(approval_mode="always_require")
def submit_replacement(order_id: str, shipping_preference: str, replacement_note: str) -> str:
    """Capture a replacement request for manual review before processing."""
    return f"Replacement recorded for order {order_id} (shipping: {shipping_preference}): {replacement_note}"

@tool(approval_mode="never_require")
def lookup_order_details(order_id: str) -> dict[str, str]:
    """Return order details for a given order ID."""
    return {
        "order_id": order_id,
        "item_name": "Wireless Headphones",
        "amount": "$129.99",
        "status": "delivered",
    }
```

```
def create_handoff_workflow() -> Workflow:
    """Build a handoff workflow with triage, refund, and order agents."""
    client = AzureOpenAIResponsesClient(
        project_endpoint=os.environ["AZURE_AI_PROJECT_ENDPOINT"],
        deployment_name=os.environ["AZURE_AI_MODEL_DEPLOYMENT_NAME"],
        credential=AzureCliCredential(),
    )

    triage = Agent(id="triage_agent", name="triage_agent", instructions="...", client=client)
    refund = Agent(id="refund_agent", name="refund_agent", instructions="...", client=client,
                   tools=[lookup_order_details, submit_refund])
    order = Agent(id="order_agent", name="order_agent", instructions="...", client=client,
                  tools=[lookup_order_details, submit_replacement])

    def termination_condition(conversation: list[Message]) -> bool:
        for msg in reversed(conversation):
            if msg.role == "assistant" and (msg.text or "").strip().lower().endswith("case complete."):
                return True
        return False

    builder = HandoffBuilder(
        name="support_workflow",
        participants=[triage, refund, order],
        termination_condition=termination_condition,
    )
    builder.add_handoff(triage, [refund], description="Route refund requests.")
    builder.add_handoff(triage, [order], description="Route replacement requests.")
    builder.add_handoff(refund, [order], description="Route to order after refund.")
    builder.add_handoff(order, [triage], description="Route back after completion.")

    return builder.with_start_agent(triage).build()
```

```
app = FastAPI(title="Workflow AG-UI Demo")
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)

ag_ui_workflow = AgentFrameworkWorkflow(
    workflow_factory=lambda _thread_id: create_handoff_workflow(),
    name="support_workflow",
    description="Customer support handoff workflow.",
)

add_agent_framework_fastapi_endpoint(
    app=app,
    agent=ag_ui_workflow,
    path="/support",
)

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8888)
```

A typical multi-turn interaction produces events like:

```
RUN_STARTED           threadId=abc123
STEP_STARTED          stepName=triage_agent
TEXT_MESSAGE_START     role=assistant
TEXT_MESSAGE_CONTENT   delta="I'll look into your refund..."
TEXT_MESSAGE_END
STEP_FINISHED         stepName=triage_agent
STEP_STARTED          stepName=refund_agent
TOOL_CALL_START       toolCallName=lookup_order_details
TOOL_CALL_ARGS        delta='{"order_id":"12345"}'
TOOL_CALL_END
TOOL_CALL_START       toolCallName=submit_refund
TOOL_CALL_ARGS        delta='{"order_id":"12345","amount":"$129.99",...}'
TOOL_CALL_END
RUN_FINISHED          interrupts=[{id: "...", value: {function_approval_request}}]
```

The client can then display an approval dialog and resume with the user's decision.

*   [AG-UI Overview](https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/)
*   [Getting Started](https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/getting-started)
*   [Agent Framework Workflows](https://learn.microsoft.com/en-us/agent-framework/workflows/)
*   [Agent Framework GitHub Repository](https://github.com/microsoft/agent-framework)

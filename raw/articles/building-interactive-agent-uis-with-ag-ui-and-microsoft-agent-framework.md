Title: Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework

URL Source: https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249

Published Time: 1/29/2026, 8:00:00 AM

Markdown Content:
# Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework | Microsoft Community Hub

Open Side Menu

[Skip to content](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249#main-content)[![Image 1: Brand Logo](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/themes/customTheme1/favicon-1730836271365.png?time=1730836274203)](https://techcommunity.microsoft.com/)

[Tech Community](https://techcommunity.microsoft.com/)[Community Hubs](https://techcommunity.microsoft.com/Directory)

[Products](https://techcommunity.microsoft.com/)

[Topics](https://techcommunity.microsoft.com/)

[Blogs](https://techcommunity.microsoft.com/Blogs)[Events](https://techcommunity.microsoft.com/Events)

[Skills Hub](https://techcommunity.microsoft.com/category/skills-hub)

[Community](https://techcommunity.microsoft.com/)

[Register](https://techcommunity.microsoft.com/t5/s/gxcuf89792/auth/oidcss/sso_login_redirect/provider/default?referer=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)[Sign In](https://techcommunity.microsoft.com/t5/s/gxcuf89792/auth/oidcss/sso_login_redirect/provider/default?referer=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)

1.   [Microsoft Community Hub](https://techcommunity.microsoft.com/)

3.   [Communities](https://techcommunity.microsoft.com/category/communities)[Products](https://techcommunity.microsoft.com/category/products-services)  

5.   [Azure](https://techcommunity.microsoft.com/category/azure)

7.   [Microsoft Developer Community Blog](https://techcommunity.microsoft.com/category/azure/blog/azuredevcommunityblog)

[Report](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249#)

## Microsoft Developer Community Blog

## Blog Post

![Image 2](https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LVYxckRmVQ?revision=3&image-dimensions=2000x2000&constrain-image=true)

Microsoft Developer Community Blog 

18 MIN READ

# Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework

[![Image 3: pratikpanda's avatar](https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/dS0xMjc0MjQzLTMzOTI1MGk4NTAyREFGNDFEQzkxQTE2?image-dimensions=50x50)](https://techcommunity.microsoft.com/users/pratikpanda/1274243)

[pratikpanda](https://techcommunity.microsoft.com/users/pratikpanda/1274243)

![Image 4: Icon for Microsoft rank](https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/cmstNC05WEo0blc?image-dimensions=100x16&constrain-image=true)Microsoft

Jan 29, 2026

## Introduction

Picture this: You've built an AI agent that analyzes financial data. A user uploads a quarterly report and asks: _"What are the top three expense categories?"_ Behind the scenes, your agent parses the spreadsheet, aggregates thousands of rows, and generates visualizations. All in 20 seconds. But the user? They see a loading spinner. Nothing else. No "reading file" message, no "analyzing data" indicator, no hint that progress is being made. They start wondering: Is it frozen? Should I refresh?

The problem isn't the agent's capabilities - it's the communication gap between the agent running on the backend and the user interface. When agents perform multi-step reasoning, call external APIs, or execute complex tool chains, users deserve to see what's happening. They need streaming updates, intermediate results, and transparent progress indicators. Yet most agent frameworks force developers to choose between simple request/response patterns or building custom solutions to stream updates to their UIs.

This is where**AG-UI**comes in. AG-UI is a fairly new event-based protocol that standardizes how agents communicate with user interfaces. Instead of every framework and development team inventing their own streaming solution, AG-UI provides a shared vocabulary of structured events that work consistently across different agent implementations. When an agent starts processing, calls a tool, generates text, or encounters an error, the UI receives explicit, typed events in real time.

The beauty of AG-UI is its framework-agnostic design. While this blog post demonstrates integration with**Microsoft Agent Framework (MAF)**, the same AG-UI protocol works with LangGraph, CrewAI, or any other compliant framework. Write your UI code once, and it works with any AG-UI-compliant backend. (Note: MAF supports both Python and .NET - this blog post focuses on the Python implementation.)

## TL;DR

**The Problem:** Users don't get real-time updates while AI agents work behind the scenes - no progress indicators, no transparency into tool calls, and no insight into what's happening.

**The Solution:** AG-UI is an open, event-based protocol that standardizes real-time communication between AI agents and user interfaces. Instead of each development team and framework inventing custom streaming solutions, AG-UI provides a shared vocabulary of structured events (like TOOL_CALL_START, TEXT_MESSAGE_CONTENT, RUN_FINISHED) that work across any compliant framework.

**Key Benefits:**

*   **Framework-agnostic**- Write UI code once, works with LangGraph, Microsoft Agent Framework, CrewAI, and more
*   **Real-time observability**- See exactly what your agent is doing as it happens
*   **Server-Sent Events**- Built on standard HTTP for universal compatibility
*   **Protocol-managed state**- No manual conversation history tracking

**In This Post:** You'll learn why AG-UI exists, how it works, and build a complete working application using Microsoft Agent Framework with Python - from server setup to client implementation.

## What You'll Learn

This blog post walks through:

*   **Why AG-UI exists** - how agent-UI communication has evolved and what problems current approaches couldn't solve
*   **How the protocol works**- the key design choices that make AG-UI simple, reliable, and framework-agnostic
*   **Protocol architecture**- the generic components and how AG-UI integrates with agent frameworks
*   **Building an AG-UI application**- a complete working example using Microsoft Agent Framework with server, client, and step-by-step setup
*   **Understanding events**- what happens under the hood when your agent runs and how to observe it
*   **Thinking in events**- how building with AG-UI differs from traditional APIs, and what benefits this brings
*   **Making the right choice**- when AG-UI is the right fit for your project and when alternatives might be better

**Estimated reading time:**15 minutes

**Who this is for:**Developers building AI agents who want to provide real-time feedback to users, and teams evaluating standardized approaches to agent-UI communication

To appreciate why AG-UI matters, we need to understand the journey that led to its creation. Let's trace how agent-UI communication has evolved through three distinct phases.

## The Evolution of Agent-UI Communication

AI agents have become more capable over time. As they evolved, the way they communicated with user interfaces had to evolve as well. Here's how this evolution unfolded.

### Phase 1: Simple Request/Response

In the early days of AI agent development, the interaction model was straightforward: send a question, wait for an answer, display the result. This synchronous approach mirrored traditional API calls and worked fine for simple scenarios.

```python
# Simple, but limiting
response = agent.run("What's the weather in Paris?")
display(response)  # User waits... and waits...
```

**Works for:**Quick queries that complete in seconds, simple Q&A interactions where immediate feedback and interactivity aren't critical.

**Breaks down:**When agents need to call multiple tools, perform multi-step reasoning, or process complex queries that take 30+ seconds. Users see nothing but a loading spinner, with no insight into what's happening or whether the agent is making progress. This creates a poor user experience and makes it impossible to show intermediate results or allow user intervention.

Recognizing these limitations, development teams began experimenting with more sophisticated approaches.

### Phase 2: Custom Streaming Solutions

As agents became more sophisticated, teams recognized the need for incremental feedback and interactivity. Rather than waiting for the complete response, they implemented custom streaming solutions to show partial results as they became available.

```python
# Every team invents their own format
for chunk in agent.stream("What's the weather?"):
    display(chunk)  # But what about tool calls? Errors? Progress?
```

This was a step forward for building interactive agent UIs, but each team solved the problem differently. Also, different frameworks had incompatible approaches - some streamed only text tokens, others sent structured JSON, and most provided no visibility into critical events like tool calls or errors.

**The problem:**

*   No standardization across frameworks - client code that works with LangGraph won't work with Crew AI, requiring separate implementations for each agent backend
*   Each implementation handles tool calls differently - some send nothing during tool execution, others send unstructured messages
*   Complex state management - clients must track conversation history, manage reconnections, and handle edge cases manually

The industry needed a better solution - a common protocol that could work across all frameworks while maintaining the benefits of streaming.

### Phase 3: Standardized Protocol (AG-UI)

AG-UI emerged as a response to the fragmentation problem. Instead of each framework and development team inventing their own streaming solution, AG-UI provides a shared vocabulary of events that work consistently across different agent implementations.

```python
# Standardized events everyone understands
async for event in agent.run_stream("What's the weather?"):
    if event.type == "TEXT_MESSAGE_CONTENT":
        display_text(event.delta)
    elif event.type == "TOOL_CALL_START":
        show_tool_indicator(event.tool_name)
    elif event.type == "TOOL_CALL_RESULT":
        show_tool_result(event.result)
```

The key difference is**structured observability**. Rather than guessing what the agent is doing from unstructured text, clients receive explicit events for every stage of execution: when the agent starts, when it generates text, when it calls a tool, when that tool completes, and when the entire run finishes.

**What's different:**A standardized vocabulary of event types, complete observability into agent execution, and framework-agnostic clients that work with any AG-UI-compliant backend. You write your UI code once, and it works whether the backend uses Microsoft Agent Framework, LangGraph, or any other framework that speaks AG-UI.

Now that we've seen why AG-UI emerged and what problems it solves, let's examine the specific design decisions that make the protocol work. These choices weren't arbitrary - each one addresses concrete challenges in building reliable, observable agent-UI communication.

## The Design Decisions Behind AG-UI

### Why Server-Sent Events (SSE)?

| Aspect | WebSockets | SSE (AG-UI) |
| --- | --- | --- |
| Complexity | Bidirectional | Unidirectional (simpler) |
| Firewall/Proxy | Sometimes blocked | Standard HTTP |
| Reconnection | Manual implementation | Built-in browser support |
| Use case | Real-time games, chat | Agent responses (one-way) |

For agent interactions, you typically only need server→client communication, making SSE a simpler choice.

SSE solves the _transport_ problem - how events travel from server to client. But once connected, how does the protocol handle conversation state across multiple interactions?

### Why Protocol-Managed Threads?

```python
# Without protocol threads (client manages):
conversation_history = []
conversation_history.append({"role": "user", "content": message})
response = agent.complete(conversation_history)
conversation_history.append({"role": "assistant", "content": response})
# Complex, error-prone, doesn't work with multiple clients

# With AG-UI (protocol manages):
thread = agent.get_new_thread()  # Server creates and manages thread
agent.run_stream(message, thread=thread)  # Server maintains context
# Simple, reliable, shareable across clients
```

With transport and state management handled, the final piece is the actual messages flowing through the connection. What information should the protocol communicate, and how should it be structured?

### Why Standardized Event Types?

Instead of parsing unstructured text, clients get typed events:

*   RUN_STARTED- Agent begins (start loading UI)
*   TEXT_MESSAGE_CONTENT- Text chunk (stream to user)
*   TOOL_CALL_START- Tool invoked (show "searching...", "calculating...")
*   TOOL_CALL_RESULT- Tool finished (show result, update UI)
*   RUN_FINISHED- Complete (hide loading)

This lets UIs react intelligently without custom parsing logic.

Now that we understand the protocol's design choices, let's see how these pieces fit together in a complete system.

## Architecture Overview

Here's how the components interact:

![Image 5](https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWQ0WU5kZA?image-dimensions=277x765&revision=3)
The communication between these layers relies on a well-defined set of event types. Here are the core events that flow through the SSE connection:

### Core Event Types

AG-UI provides a standardized set of event types to describe what's happening during an agent's execution:

*   RUN_STARTED- agent begins execution
*   TEXT_MESSAGE_START,TEXT_MESSAGE_CONTENT,TEXT_MESSAGE_END- streaming segments of text
*   TOOL_CALL_START,TOOL_CALL_ARGS,TOOL_CALL_END,TOOL_CALL_RESULT- tool execution events
*   RUN_FINISHED- agent has finished execution
*   RUN_ERROR- error information

This model lets the UI update as the agent runs, rather than waiting for the final response.

The generic architecture above applies to any AG-UI implementation. Now let's see how this translates to Microsoft Agent Framework.

## AG-UI with Microsoft Agent Framework

While AG-UI is framework-agnostic, this blog post demonstrates integration with Microsoft Agent Framework (MAF) using Python. MAF is available in both**Python**and**.NET**, giving you flexibility to build AG-UI applications in your preferred language. Understanding how MAF implements the protocol will help you build your own applications or work with other compliant frameworks.

### Integration Architecture

The Microsoft Agent Framework integration involves several specialized layers that handle protocol translation and execution orchestration:

![Image 6](https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWpEdm5abA?image-dimensions=378x999&revision=3)
**Understanding each layer:**

*   **FastAPI Endpoint**- Handles HTTP requests and establishes SSE connections for streaming
*   **AgentFrameworkAgent**- Protocol wrapper that translates between AG-UI events and Agent Framework operations
*   **Orchestrators**- Manage execution flow, coordinate tool calling sequences, and handle state transitions
*   **ChatAgent**- Your agent implementation with instructions, tools, and business logic
*   **ChatClient**- Interface to the underlying language model (Azure OpenAI, OpenAI, or other providers)

The good news? When you call add_agent_framework_fastapi_endpoint, all the middleware layers are configured automatically. You simply provide your ChatAgent, and the integration handles protocol translation, event streaming, and state management behind the scenes.

Now that we understand both the protocol architecture and the Microsoft Agent Framework integration, let's build a working application.

## Hands-On: Building Your First AG-UI Application

This section demonstrates how to build an AG-UI server and client using Microsoft Agent Framework and FastAPI.

### Prerequisites

Before building your first AG-UI application, ensure you have:

*   **Python 3.10 or later**installed
*   **Basic understanding**of async/await patterns in Python
*   **Azure CLI**installed and authenticated (az login)
*   **Azure OpenAI service**endpoint and deployment configured ([setup guide](https://learn.microsoft.com/en-us/azure/ai-foundry/openai/how-to/create-resource))
*   **Cognitive Services OpenAI Contributor**role for your Azure OpenAI resource

You'll also need to install the AG-UI integration package:

```bash
pip install agent-framework-ag-ui --pre
```

This automatically installs agent-framework-core,fastapi, and uvicorn as dependencies.

With your environment configured, let's create the server that will host your agent and expose it via the AG-UI protocol.

### Building the Server

Let's create a FastAPI server that hosts an AI agent and exposes it via AG-UI:

```python
# server.py
import os
from typing import Annotated
from dotenv import load_dotenv
from fastapi import FastAPI
from pydantic import Field
from agent_framework import ChatAgent, ai_function
from agent_framework.azure import AzureOpenAIChatClient
from agent_framework_ag_ui import add_agent_framework_fastapi_endpoint
from azure.identity import DefaultAzureCredential

# Load environment variables from .env file
load_dotenv()

# Validate environment configuration
openai_endpoint = os.getenv("AZURE_OPENAI_ENDPOINT")
model_deployment = os.getenv("AZURE_OPENAI_DEPLOYMENT_NAME")

if not openai_endpoint:
    raise RuntimeError("Missing required environment variable: AZURE_OPENAI_ENDPOINT")
if not model_deployment:
    raise RuntimeError("Missing required environment variable: AZURE_OPENAI_DEPLOYMENT_NAME")

# Define tools the agent can use
@ai_function
def get_order_status(
    order_id: Annotated[str, Field(description="The order ID to look up (e.g., ORD-001)")]
) -> dict:
    """Look up the status of a customer order.
    
    Returns order status, tracking number, and estimated delivery date.
    """
    # Simulated order lookup
    orders = {
        "ORD-001": {"status": "shipped", "tracking": "1Z999AA1", "eta": "Jan 25, 2026"},
        "ORD-002": {"status": "processing", "tracking": None, "eta": "Jan 23, 2026"},
        "ORD-003": {"status": "delivered", "tracking": "1Z999AA3", "eta": "Delivered Jan 20"},
    }
    return orders.get(order_id, {"status": "not_found", "message": "Order not found"})

# Initialize Azure OpenAI client
chat_client = AzureOpenAIChatClient(
    credential=DefaultAzureCredential(),
    endpoint=openai_endpoint,
    deployment_name=model_deployment,
)

# Configure the agent with custom instructions and tools
agent = ChatAgent(
    name="CustomerSupportAgent",
    instructions="""You are a helpful customer support assistant. 
    
    You have access to a get_order_status tool that can look up order information.
    
    IMPORTANT: When a user mentions an order ID (like ORD-001, ORD-002, etc.), 
    you MUST call the get_order_status tool to retrieve the actual order details.
    Do NOT make up or guess order information.
    
    After calling get_order_status, provide the actual results to the user in a friendly format.""",
    chat_client=chat_client,
    tools=[get_order_status],
)

# Initialize FastAPI application
app = FastAPI(
    title="AG-UI Customer Support Server",
    description="Interactive AI agent server using AG-UI protocol with tool calling"
)

# Mount the AG-UI endpoint
add_agent_framework_fastapi_endpoint(app, agent, path="/chat")

def main():
    """Entry point for the AG-UI server."""
    import uvicorn
    print("Starting AG-UI server on http://localhost:8000")
    uvicorn.run(app, host="0.0.0.0", port=8000, log_level="info")

# Run the application
if __name__ == "__main__":
    main()
```

**What's happening here:**

*   We define a tool:get_order_status with the[AI​](https://techcommunity.microsoft.com/users/ai/2777526)_function decorator
*   Use Annotated and Field for parameter descriptions to help the agent understand when and how to use the tool
*   We create an Azure OpenAI chat client with credential authentication
*   The ChatAgent is configured with domain-specific instructions and the tools parameter
*   add_agent_framework_fastapi_endpoint automatically handles SSE streaming and tool execution
*   The server exposes the agent at the/chat endpoint

> **Note:**This example uses Azure OpenAI, but AG-UI works with any chat model. You can also integrate with Azure AI Foundry's model catalog or use other LLM providers. Tool calling is supported by most modern LLMs including GPT-4, GPT-4o, and Claude models.

To run this server:

```bash
# Set your Azure OpenAI credentials
export AZURE_OPENAI_ENDPOINT="https://your-resource.openai.azure.com/"
export AZURE_OPENAI_DEPLOYMENT_NAME="gpt-4o"

# Start the server
python server.py
```

With your server running and exposing the AG-UI endpoint, the next step is building a client that can connect and consume the event stream.

## Streaming Results to Clients

With the server running, clients can connect and stream events as the agent processes requests. Here's a Python client that demonstrates the streaming capabilities:

```python
# client.py
import asyncio
import os
from dotenv import load_dotenv
from agent_framework import ChatAgent, FunctionCallContent, FunctionResultContent
from agent_framework_ag_ui import AGUIChatClient

# Load environment variables from .env file
load_dotenv()

async def interactive_chat():
    """Interactive chat session with streaming responses."""
    
    # Connect to the AG-UI server
    base_url = os.getenv("AGUI_SERVER_URL", "http://localhost:8000/chat")
    print(f"Connecting to: {base_url}\n")
    
    # Initialize the AG-UI client
    client = AGUIChatClient(endpoint=base_url)
    
    # Create a local agent representation
    agent = ChatAgent(chat_client=client)
    
    # Start a new conversation thread
    conversation_thread = agent.get_new_thread()
    
    print("Chat started! Type 'exit' or 'quit' to end the session.\n")
    
    try:
        while True:
            # Collect user input
            user_message = input("You: ")
            
            # Handle empty input
            if not user_message.strip():
                print("Please enter a message.\n")
                continue
            
            # Check for exit commands
            if user_message.lower() in ["exit", "quit", "bye"]:
                print("\nGoodbye!")
                break
            
            # Stream the agent's response
            print("Agent: ", end="", flush=True)
            
            # Track tool calls to avoid duplicate prints
            seen_tools = set()
            
            async for update in agent.run_stream(user_message, thread=conversation_thread):
                # Display text content
                if update.text:
                    print(update.text, end="", flush=True)
                
                # Display tool calls and results
                for content in update.contents:
                    if isinstance(content, FunctionCallContent):
                        # Only print each tool call once
                        if content.call_id not in seen_tools:
                            seen_tools.add(content.call_id)
                            print(f"\n[Calling tool: {content.name}]", flush=True)
                    elif isinstance(content, FunctionResultContent):
                        # Only print each result once
                        result_id = f"result_{content.call_id}"
                        if result_id not in seen_tools:
                            seen_tools.add(result_id)
                            result_text = content.result if isinstance(content.result, str) else str(content.result)
                            print(f"[Tool result: {result_text}]", flush=True)
            
            print("\n")  # New line after response completes
            
    except KeyboardInterrupt:
        print("\n\nChat interrupted by user.")
    except ConnectionError as e:
        print(f"\nConnection error: {e}")
        print("Make sure the server is running.")
    except Exception as e:
        print(f"\nUnexpected error: {e}")

def main():
    """Entry point for the AG-UI client."""
    asyncio.run(interactive_chat())

if __name__ == "__main__":
    main()
```

**Key features:**

*   The client connects to the AG-UI endpoint using AGUIChatClient with the endpoint parameter
*   run_stream()yields updates containing text and content as they arrive
*   Tool calls are detected using FunctionCallContent and displayed with[Calling tool: ...]
*   Tool results are detected using FunctionResultContent and displayed with[Tool result: ...]
*   Deduplication logic (seen_tools set) prevents printing the same tool call multiple times as it streams
*   Thread management maintains conversation context across messages
*   Graceful error handling for connection issues

To use the client:

```bash
# Optional: specify custom server URL
export AGUI_SERVER_URL="http://localhost:8000/chat"

# Start the interactive chat
python client.py
```

**Example Session:**

```none
Connecting to: http://localhost:8000/chat

Chat started! Type 'exit' or 'quit' to end the session.

You: What's the status of order ORD-001?
Agent: 
[Calling tool: get_order_status]
[Tool result: {"status": "shipped", "tracking": "1Z999AA1", "eta": "Jan 25, 2026"}]
Your order ORD-001 has been shipped! 

- Tracking Number: 1Z999AA1
- Estimated Delivery Date: January 25, 2026

You can use the tracking number to monitor the delivery progress.

You: Can you check ORD-002?
Agent: 
[Calling tool: get_order_status]
[Tool result: {"status": "processing", "tracking": null, "eta": "Jan 23, 2026"}]
Your order ORD-002 is currently being processed.

- Status: Processing
- Estimated Delivery: January 23, 2026

Your order should ship soon, and you'll receive a tracking number once it's on the way.

You: exit

Goodbye!
```

The client we just built handles events at a high level, abstracting away the details. But what's actually flowing through that SSE connection? Let's peek under the hood.

## Event Types You'll See

As the server streams back responses, clients receive a series of structured events. If you were to observe the raw SSE stream (e.g., using curl), you'd see events like:

```bash
curl -N http://localhost:8000/chat \
  -H "Content-Type: application/json" \
  -H "Accept: text/event-stream" \
  -d '{"messages": [{"role": "user", "content": "What'\''s the status of order ORD-001?"}]}'
```

**Sample event stream (with tool calling):**

```none
data: {"type":"RUN_STARTED","threadId":"eb4d9850-14ef-446c-af4b-23037acda9e8","runId":"chatcmpl-xyz"}

data: {"type":"TEXT_MESSAGE_START","messageId":"e8648880-a9ff-4178-a17d-4a6d3ec3d39c","role":"assistant"}

data: {"type":"TOOL_CALL_START","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","toolCallName":"get_order_status","parentMessageId":"e8648880-a9ff-4178-a17d-4a6d3ec3d39c"}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"{\""}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"order"}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"_id"}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"\":\""}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"ORD"}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"-"}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"001"}

data: {"type":"TOOL_CALL_ARGS","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","delta":"\"}"}

data: {"type":"TOOL_CALL_END","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y"}

data: {"type":"TOOL_CALL_RESULT","messageId":"f048cb0a-a049-4a51-9403-a05e4820438a","toolCallId":"call_GTWj2N3ZyYiiQIjg3fwmiQ8y","content":"{\"status\": \"shipped\", \"tracking\": \"1Z999AA1\", \"eta\": \"Jan 25, 2026\"}","role":"tool"}

data: {"type":"TEXT_MESSAGE_START","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","role":"assistant"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":"Your"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":" order"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":" ORD"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":"-"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":"001"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":" has"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":" been"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":" shipped"}

data: {"type":"TEXT_MESSAGE_CONTENT","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf","delta":"!"}

... (additional TEXT_MESSAGE_CONTENT events streaming the response) ...

data: {"type":"TEXT_MESSAGE_END","messageId":"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf"}

data: {"type":"RUN_FINISHED","threadId":"eb4d9850-14ef-446c-af4b-23037acda9e8","runId":"chatcmpl-xyz"}
```

**Understanding the flow:**

1.   RUN_STARTED- Agent begins processing the request
2.   TEXT_MESSAGE_START- First message starts (will contain tool calls)
3.   TOOL_CALL_START- Agent invokes the get_order_status tool
4.   Multiple TOOL_CALL_ARGS events - Arguments stream incrementally as JSON chunks ({"order_id":"ORD-001"})
5.   TOOL_CALL_END- Tool invocation structure complete
6.   TOOL_CALL_RESULT- Tool execution finished with result data
7.   TEXT_MESSAGE_START- Second message starts (the final response)
8.   Multiple TEXT_MESSAGE_CONTENT events - Response text streams word-by-word
9.   TEXT_MESSAGE_END- Response message complete
10.   RUN_FINISHED- Entire run completed successfully

This granular event model enables rich UI experiences - showing tool execution indicators ("Searching...", "Calculating..."), displaying intermediate results, and providing complete transparency into the agent's reasoning process.

Seeing the raw events helps, but truly working with AG-UI requires a shift in how you think about agent interactions. Let's explore this conceptual change.

## The Mental Model Shift

### Traditional API Thinking

```python
# Imperative: Call and wait
response = agent.run("What's 2+2?")
print(response)  # "The answer is 4"
```

**Mental model:**Function call with return value

### AG-UI Thinking

```python
# Reactive: Subscribe to events
async for event in agent.run_stream("What's 2+2?"):
    match event.type:
        case "RUN_STARTED":
            show_loading()
        case "TEXT_MESSAGE_CONTENT":
            display_chunk(event.delta)
        case "RUN_FINISHED":
            hide_loading()
```

**Mental model:**Observable stream of events

This shift feels similar to:

*   Moving from synchronous to async code
*   Moving from REST to event-driven architecture
*   Moving from polling to pub/sub

This mental shift isn't just philosophical - it unlocks concrete benefits that weren't possible with request/response patterns.

### What You Gain

#### Observability

```python
# You can SEE what the agent is doing
TOOL_CALL_START: "get_order_status"
TOOL_CALL_ARGS: {"order_id": "ORD-001"}
TOOL_CALL_RESULT: {"status": "shipped", "tracking": "1Z999AA1", "eta": "Jan 25, 2026"}
TEXT_MESSAGE_START: "Your order ORD-001 has been shipped..."
```

#### Interruptibility

```python
# Future: Cancel long-running operations
async for event in agent.run_stream(query):
    if user_clicked_cancel:
        await agent.cancel(thread_id, run_id)
        break
```

#### Transparency

```python
# Users see the reasoning process
"Looking up order ORD-001..."
"Order found: Status is 'shipped'"
"Retrieving tracking information..."
"Your order has been shipped with tracking number 1Z999AA1..."
```

To put these benefits in context, here's how AG-UI compares to traditional approaches across key dimensions:

### AG-UI vs. Traditional Approaches

| Aspect | Traditional REST | Custom Streaming | AG-UI |
| --- | --- | --- | --- |
| Connection Model | Request/Response | Varies | Server-Sent Events |
| State Management | Manual | Manual | Protocol-managed |
| Tool Calling | Invisible | Custom format | Standardized events |
| Framework | Varies | Framework-locked | Framework-agnostic |
| Browser Support | Universal | Varies | Universal |
| Implementation | Simple | Complex | Moderate |
| Ecosystem | N/A | Isolated | Growing |

You've now seen AG-UI's design principles, implementation details, and conceptual foundations. But the most important question remains: should you actually use it?

## Conclusion: Is AG-UI Right for Your Project?

AG-UI represents a shift toward standardized, observable agent interactions. Before adopting it, understand where the protocol stands and whether it fits your needs.

### Protocol Maturity

The protocol is stable enough for production use but still evolving:

**Ready now:**Core specification stable, Microsoft Agent Framework integration available, FastAPI/Python implementation mature, basic streaming and threading work reliably.

### Choose AG-UI If You

*   **Building new agent projects**- No legacy API to maintain, want future compatibility with emerging ecosystem
*   **Need streaming observability**- Multi-step workflows where users benefit from seeing each stage of execution
*   **Want framework flexibility**- Same client code works with any AG-UI-compliant backend
*   **Comfortable with evolving standards**- Can adapt to protocol changes as it matures

### Stick with Alternatives If You

*   **Have working solutions**- Custom streaming working well, migration cost not justified
*   **Need guaranteed stability**- Mission-critical systems where breaking changes are unacceptable
*   **Build simple agents**- Single-step request/response without tool calling or streaming needs
*   **Risk-averse environment**- Large existing implementations where proven approaches are required

Beyond individual project decisions, it's worth considering AG-UI's role in the broader ecosystem.

### The Bigger Picture

While this blog post focused on Microsoft Agent Framework, AG-UI's true power lies in its broader mission: creating a common language for agent-UI communication across the entire ecosystem. As more frameworks adopt it, the real value emerges:**write your UI once, work with any compliant agent framework**.

Think of it like GraphQL for APIs or OpenAPI for REST - a standardization layer that benefits the entire ecosystem.

**The protocol is young, but the problem it solves is real.**Whether you adopt it now or wait for broader adoption, understanding AG-UI helps you make informed architectural decisions for your agent applications.

Ready to dive deeper? Here are the official resources to continue your AG-UI journey.

## Resources

### AG-UI & Microsoft Agent Framework

*   [Getting Started with AG-UI (Microsoft Learn)](https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/getting-started?pivots=programming-language-python)- Official tutorial
*   [AG-UI Integration Overview](https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/?pivots=programming-language-python)- Architecture and concepts
*   [AG-UI Protocol Specification](https://docs.ag-ui.com/introduction)- Official protocol documentation
*   [Backend Tool Rendering](https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/backend-tool-rendering?pivots=programming-language-python)- Adding function tools
*   [Security Considerations](https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/security-considerations)- Production security guidance
*   [Microsoft Agent Framework Documentation](https://learn.microsoft.com/en-us/agent-framework/overview/agent-framework-overview)- Framework overview
*   [AG-UI Dojo Examples](https://dojo.ag-ui.com/microsoft-agent-framework-dotnet)- Live demonstrations

### UI Components & Integration

*   [CopilotKit for Microsoft Agent Framework](https://docs.copilotkit.ai/microsoft-agent-framework)- React component library

### Community & Support

*   [Microsoft Q&A](https://learn.microsoft.com/answers/)- Community support
*   [Agent Framework GitHub](https://github.com/microsoft/agent-framework)- Source code and issues

### Related Technologies

*   [Azure AI Foundry Documentation](https://learn.microsoft.com/azure/ai-foundry/)- Azure AI platform
*   [FastAPI Documentation](https://fastapi.tiangolo.com/)- Web framework
*   [Server-Sent Events (SSE) Specification](https://html.spec.whatwg.org/multipage/server-sent-events.html)- Protocol standard

_This blog post introduces AG-UI with Microsoft Agent Framework, focusing on fundamental concepts and building your first interactive agent application._

Updated Jan 21, 2026

Version 1.0

[agents](https://techcommunity.microsoft.com/tag/agents?nodeId=board%3AAzureDevCommunityBlog)

[ai](https://techcommunity.microsoft.com/tag/ai?nodeId=board%3AAzureDevCommunityBlog)

[ai foundry](https://techcommunity.microsoft.com/tag/ai%20foundry?nodeId=board%3AAzureDevCommunityBlog)

[azure](https://techcommunity.microsoft.com/tag/azure?nodeId=board%3AAzureDevCommunityBlog)

[azure ai foundry](https://techcommunity.microsoft.com/tag/azure%20ai%20foundry?nodeId=board%3AAzureDevCommunityBlog)

[best practices](https://techcommunity.microsoft.com/tag/best%20practices?nodeId=board%3AAzureDevCommunityBlog)

[developer](https://techcommunity.microsoft.com/tag/developer?nodeId=board%3AAzureDevCommunityBlog)

[get started](https://techcommunity.microsoft.com/tag/get%20started?nodeId=board%3AAzureDevCommunityBlog)

[learning](https://techcommunity.microsoft.com/tag/learning?nodeId=board%3AAzureDevCommunityBlog)

[llm](https://techcommunity.microsoft.com/tag/llm?nodeId=board%3AAzureDevCommunityBlog)

Like 2

Comment

[![Image 7: pratikpanda's avatar](https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/dS0xMjc0MjQzLTMzOTI1MGk4NTAyREFGNDFEQzkxQTE2?image-dimensions=80x80)](https://techcommunity.microsoft.com/users/pratikpanda/1274243)

[pratikpanda](https://techcommunity.microsoft.com/users/pratikpanda/1274243)

![Image 8: Icon for Microsoft rank](https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/cmstNC05WEo0blc?image-dimensions=100x16&constrain-image=true)Microsoft

Joined January 12, 2022

Send Message

[View Profile](https://techcommunity.microsoft.com/users/pratikpanda/1274243)

[](https://techcommunity.microsoft.com/category/azure/blog/azuredevcommunityblog)

[Microsoft Developer Community Blog](https://techcommunity.microsoft.com/category/azure/blog/azuredevcommunityblog)

Follow this blog board to get notified when there's new activity

Enjoying the article? Sign in to share your thoughts.

Sign in

### Share this page

*   [](https://www.linkedin.com/sharing/share-offsite/?url=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)
*   [](https://www.facebook.com/share.php?u=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249&t=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub)
*   [](https://twitter.com/share?text=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub&url=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)
*   [](https://www.reddit.com/submit?url=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249&title=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub)
*   [](https://bsky.app/intent/compose?text=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub%21%20%F0%9F%A6%8B%0Ahttps%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)
*   [](https://techcommunity.microsoft.com/t5/s/gxcuf89792/rss/Community)
*   [](mailto:?body=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)

What's new

*   [Surface Pro](https://www.microsoft.com/surface/devices/surface-pro)
*   [Surface Laptop](https://www.microsoft.com/surface/devices/surface-laptop)
*   [Surface Laptop Studio 2](https://www.microsoft.com/d/Surface-Laptop-Studio-2/8rqr54krf1dz)
*   [Copilot for organizations](https://www.microsoft.com/microsoft-copilot/organizations?icid=DSM_Footer_CopilotOrganizations)
*   [Copilot for personal use](https://www.microsoft.com/microsoft-copilot/for-individuals?form=MY02PT&OCID=GE_web_Copilot_Free_868g3t5nj)
*   [AI in Windows](https://www.microsoft.com/windows/ai-features?icid=DSM_Footer_WhatsNew_AIinWindows)
*   [Explore Microsoft products](https://www.microsoft.com/microsoft-products-and-apps)
*   [Windows 11 apps](https://www.microsoft.com/windows/apps-for-windows?icid=DSM_Footer_WhatsNew_Windows11apps)

Microsoft Store

*   [Account profile](https://account.microsoft.com/)
*   [Download Center](https://www.microsoft.com/download)
*   [Microsoft Store support](https://go.microsoft.com/fwlink/?linkid=2139749)
*   [Returns](https://go.microsoft.com/fwlink/p/?LinkID=824764&clcid=0x809)
*   [Order tracking](https://www.microsoft.com/store/b/order-tracking)
*   [Certified Refurbished](https://www.microsoft.com/store/b/certified-refurbished-products)
*   [Microsoft Store Promise](https://www.microsoft.com/store/b/why-microsoft-store?icid=footer_why-msft-store_7102020)
*   [Flexible Payments](https://www.microsoft.com/store/b/payment-financing-options?icid=footer_financing_vcc)

Education

*   [Microsoft in education](https://www.microsoft.com/education)
*   [Devices for education](https://www.microsoft.com/education/devices/overview)
*   [Microsoft Teams for Education](https://www.microsoft.com/education/products/teams)
*   [Microsoft 365 Education](https://www.microsoft.com/education/products/microsoft-365)
*   [How to buy for your school](https://www.microsoft.com/education/how-to-buy)
*   [Educator training and development](https://education.microsoft.com/)
*   [Deals for students and parents](https://www.microsoft.com/store/b/education)
*   [AI for education](https://www.microsoft.com/education/ai-in-education)

Business

*   [Microsoft AI](https://www.microsoft.com/ai?icid=DSM_Footer_AI)
*   [Microsoft Security](https://www.microsoft.com/security)
*   [Dynamics 365](https://www.microsoft.com/dynamics-365)
*   [Microsoft 365](https://www.microsoft.com/microsoft-365/business)
*   [Microsoft Power Platform](https://www.microsoft.com/power-platform)
*   [Microsoft Teams](https://www.microsoft.com/microsoft-teams/group-chat-software)
*   [Microsoft 365 Copilot](https://www.microsoft.com/microsoft-365-copilot?icid=DSM_Footer_Microsoft365Copilot)
*   [Small Business](https://www.microsoft.com/store/b/business?icid=CNavBusinessStore)

Developer & IT

*   [Azure](https://azure.microsoft.com/)
*   [Microsoft Developer](https://developer.microsoft.com/)
*   [Microsoft Learn](https://learn.microsoft.com/)
*   [Support for AI marketplace apps](https://www.microsoft.com/software-development-companies/offers-benefits/isv-success?icid=DSM_Footer_SupportAIMarketplace&ocid=cmm3atxvn98)
*   [Microsoft Tech Community](https://techcommunity.microsoft.com/)
*   [Microsoft Marketplace](https://marketplace.microsoft.com/?icid=DSM_Footer_Marketplace&ocid=cmm3atxvn98)
*   [Marketplace Rewards](https://www.microsoft.com/software-development-companies/offers-benefits/marketplace-rewards?icid=DSM_Footer_MarketplaceRewards&ocid=cmm3atxvn98)
*   [Visual Studio](https://visualstudio.microsoft.com/)

Company

*   [Careers](https://careers.microsoft.com/)
*   [About Microsoft](https://www.microsoft.com/about)
*   [Company news](https://news.microsoft.com/source/?icid=DSM_Footer_Company_CompanyNews)
*   [Privacy at Microsoft](https://www.microsoft.com/privacy?icid=DSM_Footer_Company_Privacy)
*   [Investors](https://www.microsoft.com/investor/default.aspx)
*   [Diversity and inclusion](https://www.microsoft.com/diversity/default?icid=DSM_Footer_Company_Diversity)
*   [Accessibility](https://www.microsoft.com/accessibility)
*   [Sustainability](https://www.microsoft.com/sustainability/)

[Your Privacy Choices](https://aka.ms/yourcaliforniaprivacychoices)[Consumer Health Privacy](https://go.microsoft.com/fwlink/?linkid=2259814)

*   [Sitemap](https://www.microsoft.com/en-us/sitemap1.aspx)
*   [Contact Microsoft](https://support.microsoft.com/contactus)
*   [Privacy](https://go.microsoft.com/fwlink/?LinkId=521839)
*   [Manage cookies](javascript:manageConsent();)
*   [Terms of use](https://go.microsoft.com/fwlink/?LinkID=206977)
*   [Trademarks](https://go.microsoft.com/fwlink/?linkid=2196228)
*   [Safety & eco](https://go.microsoft.com/fwlink/?linkid=2196227)
*   [Recycling](https://www.microsoft.com/legal/compliance/recycling)
*   [About our ads](https://choice.microsoft.com/)
*   © Microsoft 2026

*   [![Image 9: Share to LinkedIn](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-linkedin.svg?time=1743177821000)Share on LinkedIn](https://www.linkedin.com/sharing/share-offsite/?url=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)
*   [![Image 10: Share to Facebook](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-facebook.svg?time=1743177821000)Share on Facebook](https://www.facebook.com/share.php?u=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249&t=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub)
*   [![Image 11: Share to X](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-x.svg?time=1743177821000)Share on X](https://twitter.com/share?text=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub&url=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)
*   [![Image 12: Share to Reddit](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-reddit.svg?time=1743177821000)Share on Reddit](https://www.reddit.com/submit?url=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249&title=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub)
*   [![Image 13: Share to Blue Sky](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/bluesky-brands.svg?time=1743697028000)Share on Bluesky](https://bsky.app/intent/compose?text=Building%20Interactive%20Agent%20UIs%20with%20AG-UI%20and%20Microsoft%20Agent%20Framework%20%7C%20Microsoft%20Community%20Hub%21%20%F0%9F%A6%8B%0Ahttps%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)
*   [![Image 14: Subscribe to RSS](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/rss.svg?time=1743177821000)Share on RSS](https://techcommunity.microsoft.com/t5/s/gxcuf89792/rss/Community)
*   [![Image 15: Share to Email](https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-email.svg?time=1743177821000)Share on Email](mailto:?body=https%3A%2F%2Ftechcommunity.microsoft.com%2Fblog%2Fazuredevcommunityblog%2Fbuilding-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework%2F4488249)

"}},"componentScriptGroups({\"componentId\":\"custom.widget.SocialSharing\"})":{"__typename":"ComponentScriptGroups","scriptGroups":{"__typename":"ComponentScriptGroupsDefinition","afterInteractive":{"__typename":"PageScriptGroupDefinition","group":"AFTER_INTERACTIVE","scriptIds":[]},"lazyOnLoad":{"__typename":"PageScriptGroupDefinition","group":"LAZY_ON_LOAD","scriptIds":[]}},"componentScripts":[]},"component({\"componentId\":\"custom.widget.MicrosoftFooter\"})":{"__typename":"Component","render({\"context\":{\"component\":{\"entities\":[],\"props\":{}},\"page\":{\"entities\":[\"message:4488249\"],\"name\":\"BlogMessagePage\",\"props\":{},\"url\":\"https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249\"}}})":{"__typename":"ComponentRenderResult","html":"

*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/surface/devices/surface-pro\" data-m=\"{"cN":"Footer_WhatsNew_SurfacePro_nav","id":"n1c1c1c1m1r1a2","sN":1,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/surface/devices/surface-laptop\" data-m=\"{"cN":"Footer_WhatsNew_SurfaceLaptop_nav","id":"n2c1c1c1m1r1a2","sN":2,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/d/Surface-Laptop-Studio-2/8rqr54krf1dz\" data-m=\"{"cN":"Footer_WhatsNew_SurfaceLaptopStudio2_nav","id":"n3c1c1c1m1r1a2","sN":3,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/microsoft-copilot/organizations?icid=DSM_Footer_CopilotOrganizations\" data-m=\"{"cN":"Footer_WhatsNew_CopilotOrganizations_nav","id":"n4c1c1c1m1r1a2","sN":4,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/microsoft-copilot/for-individuals?form=MY02PT&OCID=GE_web_Copilot_Free_868g3t5nj\" data-m=\"{"cN":"Footer_WhatsNew_CopilotPersonal_nav","id":"n5c1c1c1m1r1a2","sN":5,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/windows/ai-features?icid=DSM_Footer_WhatsNew_AIinWindows\" data-m=\"{"cN":"Footer_WhatsNew_AIinWindows_nav","id":"n6c1c1c1m1r1a2","sN":6,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/microsoft-products-and-apps\" data-m=\"{"cN":"Footer_WhatsNew_ExploreMicrosoftProducts_nav","id":"n7c1c1c1m1r1a2","sN":7,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/windows/apps-for-windows?icid=DSM_Footer_WhatsNew_Windows11apps\" data-m=\"{"cN":"Footer_WhatsNew_Windows11Apps_nav","id":"n8c1c1c1m1r1a2","sN":8,"aN":"c1c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)

*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://account.microsoft.com/\" data-m=\"{"cN":"Footer_StoreandSupport_AccountProfile_nav","id":"n1c2c1c1m1r1a2","sN":1,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/download\" data-m=\"{"cN":"Footer_StoreandSupport_DownloadCenter_nav","id":"n2c2c1c1m1r1a2","sN":2,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://go.microsoft.com/fwlink/?linkid=2139749\" data-m=\"{"cN":"Footer_StoreandSupport_SalesAndSupport_nav","id":"n3c2c1c1m1r1a2","sN":3,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" id=\"footer-returns\" href=\"https://go.microsoft.com/fwlink/p/?LinkID=824764&clcid=0x809\" data-m=\"{"cN":"Footer_StoreandSupport_Returns_nav","id":"n4c2c1c1m1r1a2","sN":4,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/store/b/order-tracking\" data-m=\"{"cN":"Footer_StoreandSupport_OrderTracking_nav","id":"n5c2c1c1m1r1a2","sN":5,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/store/b/certified-refurbished-products\" data-m=\"{"cN":"Footer_StoreandSupport_CertifiedRefurbished_nav","id":"n6c2c1c1m1r1a2","sN":6,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/store/b/why-microsoft-store?icid=footer_why-msft-store_7102020\" data-m=\"{"cN":"Footer_StoreandSupport_MicrosoftPromise_nav","id":"n7c2c1c1m1r1a2","sN":7,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/store/b/payment-financing-options?icid=footer_financing_vcc\" data-m=\"{"cN":"Footer_StoreandSupport_Financing_nav","id":"n8c2c1c1m1r1a2","sN":8,"aN":"c2c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)

*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/education\" data-m=\"{"cN":"Footer_Education_MicrosoftInEducation_nav","id":"n1c3c1c1m1r1a2","sN":1,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/education/devices/overview\" data-m=\"{"cN":"Footer_Education_DevicesforEducation_nav","id":"n2c3c1c1m1r1a2","sN":2,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/education/products/teams\" data-m=\"{"cN":"Footer_Education_MicrosoftTeamsforEducation_nav","id":"n3c3c1c1m1r1a2","sN":3,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/education/products/microsoft-365\" data-m=\"{"cN":"Footer_Education_Microsoft365Education_nav","id":"n4c3c1c1m1r1a2","sN":4,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/education/how-to-buy\" data-m=\"{"cN":"Footer_Education_HowToBuy_nav","id":"n5c3c1c1m1r1a2","sN":5,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://education.microsoft.com/\" data-m=\"{"cN":"Footer_Education_EducatorTrainingDevelopment_nav","id":"n6c3c1c1m1r1a2","sN":6,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/store/b/education\" data-m=\"{"cN":"Footer_Education_DealsForStudentsandParents_nav","id":"n7c3c1c1m1r1a2","sN":7,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/education/ai-in-education\" data-m=\"{"cN":"Footer_Education_AIinEducation_nav","id":"n8c3c1c1m1r1a2","sN":8,"aN":"c3c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)

*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/ai?icid=DSM_Footer_AI\" data-m=\"{"cN":"Footer_Business_MicrosoftAI_nav","id":"n1c4c1c1m1r1a2","sN":1,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/security\" data-m=\"{"cN":"Footer_Business_MicrosoftSecurity_nav","id":"n2c4c1c1m1r1a2","sN":2,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/dynamics-365\" data-m=\"{"cN":"Footer_Business_MicrosoftDynamics365_nav","id":"n3c4c1c1m1r1a2","sN":3,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/microsoft-365/business\" data-m=\"{"cN":"Footer_Business_Microsoft365_nav","id":"n4c4c1c1m1r1a2","sN":4,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/power-platform\" data-m=\"{"cN":"Footer_Business_MicrosoftPowerPlatform_nav","id":"n5c4c1c1m1r1a2","sN":5,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/microsoft-teams/group-chat-software\" data-m=\"{"cN":"Footer_Business_MicrosoftTeams_nav","id":"n6c4c1c1m1r1a2","sN":6,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/microsoft-365-copilot?icid=DSM_Footer_Microsoft365Copilot\" data-m=\"{"cN":"Footer_Business_Microsoft365Copilot_nav","id":"n7c4c1c1m1r1a2","sN":7,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/store/b/business?icid=CNavBusinessStore\" data-m=\"{"cN":"Footer_Business_SmallBusiness_nav","id":"n8c4c1c1m1r1a2","sN":8,"aN":"c4c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)

*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://azure.microsoft.com/\" data-m=\"{"cN":"Footer_Enterprise_MicrosoftAzure_nav","id":"n1c5c1c1m1r1a2","sN":1,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://developer.microsoft.com/\" data-m=\"{"cN":"Footer_Developer_DeveloperCenter_nav","id":"n2c5c1c1m1r1a2","sN":2,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://learn.microsoft.com/\" data-m=\"{"cN":"Footer_DeveloperAndIT_MicrosoftLearn_nav","id":"n3c5c1c1m1r1a2","sN":3,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/software-development-companies/offers-benefits/isv-success?icid=DSM_Footer_SupportAIMarketplace&ocid=cmm3atxvn98\" data-m=\"{"cN":"Footer_DeveloperAndIT_SupportAIMarketplace_nav","id":"n4c5c1c1m1r1a2","sN":4,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://techcommunity.microsoft.com/\" data-m=\"{"cN":"Footer_DeveloperAndIT_MicrosoftTechCommunity_nav","id":"n5c5c1c1m1r1a2","sN":5,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://marketplace.microsoft.com/?icid=DSM_Footer_Marketplace&ocid=cmm3atxvn98\" data-m=\"{"cN":"Footer_DeveloperAndIT_Marketplace_nav","id":"n6c5c1c1m1r1a2","sN":6,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/software-development-companies/offers-benefits/marketplace-rewards?icid=DSM_Footer_MarketplaceRewards&ocid=cmm3atxvn98\" data-m=\"{"cN":"Footer_DeveloperAndIT_MarketplaceRewards_nav","id":"n7c5c1c1m1r1a2","sN":7,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://visualstudio.microsoft.com/\" data-m=\"{"cN":"Footer_Developer_MicrosoftVisualStudio_nav","id":"n8c5c1c1m1r1a2","sN":8,"aN":"c5c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)

*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://careers.microsoft.com/\" data-m=\"{"cN":"Footer_Company_Careers_nav","id":"n1c6c1c1m1r1a2","sN":1,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/about\" data-m=\"{"cN":"Footer_Company_AboutMicrosoft_nav","id":"n2c6c1c1m1r1a2","sN":2,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://news.microsoft.com/source/?icid=DSM_Footer_Company_CompanyNews\" data-m=\"{"cN":"Footer_Company_CompanyNews_nav","id":"n3c6c1c1m1r1a2","sN":3,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/privacy?icid=DSM_Footer_Company_Privacy\" data-m=\"{"cN":"Footer_Company_PrivacyAtMicrosoft_nav","id":"n4c6c1c1m1r1a2","sN":4,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/investor/default.aspx\" data-m=\"{"cN":"Footer_Company_Investors_nav","id":"n5c6c1c1m1r1a2","sN":5,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/diversity/default?icid=DSM_Footer_Company_Diversity\" data-m=\"{"cN":"Footer_Company_DiversityAndInclusion_nav","id":"n6c6c1c1m1r1a2","sN":6,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/accessibility\" data-m=\"{"cN":"Footer_Company_Accessibility_nav","id":"n7c6c1c1m1r1a2","sN":7,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)
*   [</li:i18n>\" class=\"c-uhff-link\" href=\"https://www.microsoft.com/sustainability/\" data-m=\"{"cN":"Footer_Company_Sustainability_nav","id":"n8c6c1c1m1r1a2","sN":8,"aN":"c6c1c1m1r1a2"}\">](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)

[](https://techcommunity.microsoft.com/%22https://aka.ms/yourcaliforniaprivacychoices/%22)[](https://techcommunity.microsoft.com/%22https://go.microsoft.com/fwlink/?linkid=2259814\%22)

*   [](https://techcommunity.microsoft.com/%22https://www.microsoft.com/en-us/sitemap1.aspx/%22)
*   [](https://techcommunity.microsoft.com/%22https://support.microsoft.com/contactus/%22)
*   [](https://techcommunity.microsoft.com/%22https://go.microsoft.com/fwlink/?LinkId=521839\%22)
*   [](https://techcommunity.microsoft.com/%22javascript:manageConsent();/%22)
*   [](https://techcommunity.microsoft.com/%22https://go.microsoft.com/fwlink/?LinkID=206977\%22)
*   [](https://techcommunity.microsoft.com/%22https://go.microsoft.com/fwlink/?linkid=2196228\%22)
*   [](https://techcommunity.microsoft.com/%22https://go.microsoft.com/fwlink/?linkid=2196227\%22)
*   [](https://techcommunity.microsoft.com/%22https://www.microsoft.com/legal/compliance/recycling/%22)
*   [](https://techcommunity.microsoft.com/%22https://choice.microsoft.com/%22)
*   © 

*   [![Image 16: \"<li:i18n](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)</li:i18n>\" src=\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-linkedin.svg?time=1743177821000\">](https://techcommunity.microsoft.com/%22https://www.linkedin.com/sharing/share-offsite/?url=page.url\%22)
*   [![Image 17: \"<li:i18n](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)</li:i18n>\" src=\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-facebook.svg?time=1743177821000\">](https://techcommunity.microsoft.com/%22https://www.facebook.com/share.php?u=page.url&t=page-name\%22)
*   [![Image 18: \"<li:i18n](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)</li:i18n>\" src=\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-x.svg?time=1743177821000\">](https://techcommunity.microsoft.com/%22https://twitter.com/share?text=page-name&url=page.url\%22)
*   [![Image 19: \"<li:i18n](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)</li:i18n>\" src=\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-reddit.svg?time=1743177821000\">](https://techcommunity.microsoft.com/%22https://www.reddit.com/submit?url=page.url&title=page-name\%22)
*   [![Image 20: \"<li:i18n](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)</li:i18n>\" src=\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/bluesky-brands.svg?time=1743697028000\">](https://techcommunity.microsoft.com/%22https://bsky.app/intent/compose?text=page-name%21%20%F0%9F%A6%8B%0Apage.url\%22)
*   [![Image 21: \"<li:i18n](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)</li:i18n>\" src=\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/rss.svg?time=1743177821000\">](https://techcommunity.microsoft.com/%22/t5/s/gxcuf89792/rss/Community/%22)
*   [![Image 22: \"<li:i18n](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)</li:i18n>\" src=\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/m_assets/components/MicrosoftFooter/assets/social-share-email.svg?time=1743177821000\">](https://techcommunity.microsoft.com/%22mailto:?body=page.url\%22)

"}},"componentScriptGroups({\"componentId\":\"custom.widget.MicrosoftFooter\"})":{"__typename":"ComponentScriptGroups","scriptGroups":{"__typename":"ComponentScriptGroupsDefinition","afterInteractive":{"__typename":"PageScriptGroupDefinition","group":"AFTER_INTERACTIVE","scriptIds":[]},"lazyOnLoad":{"__typename":"PageScriptGroupDefinition","group":"LAZY_ON_LOAD","scriptIds":[]}},"componentScripts":[]},"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/community/NavbarDropdownToggle\"]})":[{"__ref":"CachedAsset:text:en_US-components/community/NavbarDropdownToggle-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageCoverImage\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageCoverImage-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/nodes/NodeTitle\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/nodes/NodeTitle-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageTimeToRead\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageTimeToRead-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageSubject\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageSubject-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/users/UserLink\"]})":[{"__ref":"CachedAsset:text:en_US-components/users/UserLink-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/users/UserRank\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/users/UserRank-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageTime\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageTime-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageBody\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageBody-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageCustomFields\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageCustomFields-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageRevision\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageRevision-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/common/QueryHandler\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/common/QueryHandler-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/tags/TagList\"]})":[{"__ref":"CachedAsset:text:en_US-components/tags/TagList-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageReplyButton\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageReplyButton-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/messages/MessageAuthorBio\"]})":[{"__ref":"CachedAsset:text:en_US-components/messages/MessageAuthorBio-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/users/UserAvatar\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/users/UserAvatar-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/ranks/UserRankLabel\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/ranks/UserRankLabel-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/tags/TagView/TagViewChip\"]})":[{"__ref":"CachedAsset:text:en_US-components/tags/TagView/TagViewChip-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/common/Pager/PagerLoadMore\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/common/Pager/PagerLoadMore-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"components/users/UserRegistrationDate\"]})":[{"__ref":"CachedAsset:text:en_US-components/users/UserRegistrationDate-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/nodes/NodeAvatar\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/nodes/NodeAvatar-1776098500930"}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/nodes/NodeDescription\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/nodes/NodeDescription-1776098500930"}],"customFieldDefinitions({\"fieldNames\":[\"blogScoreGradeResponse\"]})":[{"__typename":"CustomFieldDefinitionResource","definition":{"__typename":"CustomFieldDefinition","name":"blogScoreGradeResponse","viewable":false}}],"cachedText({\"lastModified\":\"1776098500930\",\"locale\":\"en-US\",\"namespaces\":[\"shared/client/components/nodes/NodeIcon\"]})":[{"__ref":"CachedAsset:text:en_US-shared/client/components/nodes/NodeIcon-1776098500930"}]},"Theme:customTheme1":{"__typename":"Theme","id":"customTheme1"},"User:user:-1":{"__typename":"User","id":"user:-1","entityType":"USER","eventPath":"community:gxcuf89792/user:-1","uid":-1,"login":"Anonymous","email":"","avatar":null,"rank":null,"kudosWeight":1,"registrationData":{"__typename":"RegistrationData","status":"ANONYMOUS","registrationTime":null,"confirmEmailStatus":false,"registrationAccessLevel":"VIEW","ssoRegistrationFields":[]},"ssoId":null,"profileSettings":{"__typename":"ProfileSettings","dateDisplayStyle":{"__typename":"InheritableStringSettingWithPossibleValues","key":"layout.friendly_dates_enabled","value":"false","localValue":"true","possibleValues":["true","false"]},"dateDisplayFormat":{"__typename":"InheritableStringSetting","key":"layout.format_pattern_date","value":"MMM dd yyyy","localValue":"MM-dd-yyyy"},"language":{"__typename":"InheritableStringSettingWithPossibleValues","key":"profile.language","value":"en-US","localValue":null,"possibleValues":["en-US","es-ES"]},"repliesSortOrder":{"__typename":"InheritableStringSettingWithPossibleValues","key":"config.user_replies_sort_order","value":"DEFAULT","localValue":"DEFAULT","possibleValues":["DEFAULT","LIKES","PUBLISH_TIME","REVERSE_PUBLISH_TIME"]}},"deleted":false},"CachedAsset:pages-1776098495583":{"__typename":"CachedAsset","id":"pages-1776098495583","value":[{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"BlogViewAllPostsPage","type":"BLOG","urlPath":"/category/:categoryId/blog/:boardId/all-posts/(/:after|/:before)?","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CasePortalPage","type":"CASE_PORTAL","urlPath":"/caseportal","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CreateGroupHubPage","type":"GROUP_HUB","urlPath":"/groups/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CaseViewPage","type":"CASE_DETAILS","urlPath":"/case/:caseId/:caseNumber","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"InboxPage","type":"COMMUNITY","urlPath":"/inbox","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"HelpFAQPage","type":"COMMUNITY","urlPath":"/help","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"IdeaMessagePage","type":"IDEA_POST","urlPath":"/idea/:boardId/:messageSubject/:messageId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"IdeaViewAllIdeasPage","type":"IDEA","urlPath":"/category/:categoryId/ideas/:boardId/all-ideas/(/:after|/:before)?","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"LoginPage","type":"USER","urlPath":"/signin","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"WorkstreamsPage","type":"COMMUNITY","urlPath":"/workstreams","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"BlogPostPage","type":"BLOG","urlPath":"/category/:categoryId/blogs/:boardId/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"UserBlogPermissions.Page","type":"COMMUNITY","urlPath":"/c/user-blog-permissions/page","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ThemeEditorPage","type":"COMMUNITY","urlPath":"/designer/themes","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TkbViewAllArticlesPage","type":"TKB","urlPath":"/category/:categoryId/kb/:boardId/all-articles/(/:after|/:before)?","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1730819800000,"localOverride":null,"page":{"id":"AllEvents","type":"CUSTOM","urlPath":"/Events","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"OccasionEditPage","type":"EVENT","urlPath":"/event/:boardId/:messageSubject/:messageId/edit","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"OAuthAuthorizationAllowPage","type":"USER","urlPath":"/auth/authorize/allow","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"PageEditorPage","type":"COMMUNITY","urlPath":"/designer/pages","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"PostPage","type":"COMMUNITY","urlPath":"/category/:categoryId/:boardId/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CreateUserGroup.Page","type":"COMMUNITY","urlPath":"/c/create-user-group/page","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ForumBoardPage","type":"FORUM","urlPath":"/category/:categoryId/discussions/:boardId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TkbBoardPage","type":"TKB","urlPath":"/category/:categoryId/kb/:boardId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"EventPostPage","type":"EVENT","urlPath":"/category/:categoryId/events/:boardId/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"UserBadgesPage","type":"COMMUNITY","urlPath":"/users/:login/:userId/badges","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"GroupHubMembershipAction","type":"GROUP_HUB","urlPath":"/membership/join/:nodeId/:membershipType","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"MaintenancePage","type":"COMMUNITY","urlPath":"/maintenance","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"IdeaReplyPage","type":"IDEA_REPLY","urlPath":"/idea/:boardId/:messageSubject/:messageId/comments/:replyId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"UserSettingsPage","type":"USER","urlPath":"/mysettings/:userSettingsTab","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"GroupHubsPage","type":"GROUP_HUB","urlPath":"/groups","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ForumPostPage","type":"FORUM","urlPath":"/category/:categoryId/discussions/:boardId/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"OccasionRsvpActionPage","type":"OCCASION","urlPath":"/event/:boardId/:messageSubject/:messageId/rsvp/:responseType","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"VerifyUserEmailPage","type":"USER","urlPath":"/verifyemail/:userId/:verifyEmailToken","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"AllOccasionsPage","type":"OCCASION","urlPath":"/category/:categoryId/events/:boardId/all-events/(/:after|/:before)?","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"EventBoardPage","type":"EVENT","urlPath":"/category/:categoryId/events/:boardId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TkbReplyPage","type":"TKB_REPLY","urlPath":"/kb/:boardId/:messageSubject/:messageId/comments/:replyId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"IdeaBoardPage","type":"IDEA","urlPath":"/category/:categoryId/ideas/:boardId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CommunityGuideLinesPage","type":"COMMUNITY","urlPath":"/communityguidelines","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CaseCreatePage","type":"SALESFORCE_CASE_CREATION","urlPath":"/caseportal/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TkbEditPage","type":"TKB","urlPath":"/kb/:boardId/:messageSubject/:messageId/edit","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ForgotPasswordPage","type":"USER","urlPath":"/forgotpassword","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"IdeaEditPage","type":"IDEA","urlPath":"/idea/:boardId/:messageSubject/:messageId/edit","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TagPage","type":"COMMUNITY","urlPath":"/tag/:tagName","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"BlogBoardPage","type":"BLOG","urlPath":"/category/:categoryId/blog/:boardId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"OccasionMessagePage","type":"OCCASION_TOPIC","urlPath":"/event/:boardId/:messageSubject/:messageId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ManageContentPage","type":"COMMUNITY","urlPath":"/managecontent","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ClosedMembershipNodeNonMembersPage","type":"GROUP_HUB","urlPath":"/closedgroup/:groupHubId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CommunityPage","type":"COMMUNITY","urlPath":"/","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ForumMessagePage","type":"FORUM_TOPIC","urlPath":"/discussions/:boardId/:messageSubject/:messageId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"IdeaPostPage","type":"IDEA","urlPath":"/category/:categoryId/ideas/:boardId/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1730819800000,"localOverride":null,"page":{"id":"CommunityHub.Page","type":"CUSTOM","urlPath":"/Directory","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"BlogMessagePage","type":"BLOG_ARTICLE","urlPath":"/blog/:boardId/:messageSubject/:messageId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"RegistrationPage","type":"USER","urlPath":"/register","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"EditGroupHubPage","type":"GROUP_HUB","urlPath":"/group/:groupHubId/edit","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ForumEditPage","type":"FORUM","urlPath":"/discussions/:boardId/:messageSubject/:messageId/edit","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ResetPasswordPage","type":"USER","urlPath":"/resetpassword/:userId/:resetPasswordToken","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1730819800000,"localOverride":null,"page":{"id":"AllBlogs.Page","type":"CUSTOM","urlPath":"/blogs","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TkbMessagePage","type":"TKB_ARTICLE","urlPath":"/kb/:boardId/:messageSubject/:messageId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"BlogEditPage","type":"BLOG","urlPath":"/blog/:boardId/:messageSubject/:messageId/edit","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ManageUsersPage","type":"USER","urlPath":"/users/manage/:tab?/:manageUsersTab?","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ForumReplyPage","type":"FORUM_REPLY","urlPath":"/discussions/:boardId/:messageSubject/:messageId/replies/:replyId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"PrivacyPolicyPage","type":"COMMUNITY","urlPath":"/privacypolicy","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"NotificationPage","type":"COMMUNITY","urlPath":"/notifications","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"UserPage","type":"USER","urlPath":"/users/:login/:userId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"HealthCheckPage","type":"COMMUNITY","urlPath":"/health","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"OccasionReplyPage","type":"OCCASION_REPLY","urlPath":"/event/:boardId/:messageSubject/:messageId/comments/:replyId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ManageMembersPage","type":"GROUP_HUB","urlPath":"/group/:groupHubId/manage/:tab?","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"SearchResultsPage","type":"COMMUNITY","urlPath":"/search","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"BlogReplyPage","type":"BLOG_REPLY","urlPath":"/blog/:boardId/:messageSubject/:messageId/replies/:replyId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"GroupHubPage","type":"GROUP_HUB","urlPath":"/group/:groupHubId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TermsOfServicePage","type":"COMMUNITY","urlPath":"/termsofservice","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"CategoryPage","type":"CATEGORY","urlPath":"/category/:categoryId","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"ForumViewAllTopicsPage","type":"FORUM","urlPath":"/category/:categoryId/discussions/:boardId/all-topics/(/:after|/:before)?","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"TkbPostPage","type":"TKB","urlPath":"/category/:categoryId/kbs/:boardId/create","__typename":"PageDescriptor"},"__typename":"PageResource"},{"lastUpdatedTime":1776098495583,"localOverride":null,"page":{"id":"GroupHubPostPage","type":"GROUP_HUB","urlPath":"/group/:groupHubId/:boardId/create","__typename":"PageDescriptor"},"__typename":"PageResource"}],"localOverride":false},"CachedAsset:text:en_US-components/context/AppContext/AppContextProvider-0":{"__typename":"CachedAsset","id":"text:en_US-components/context/AppContext/AppContextProvider-0","value":{"noCommunity":"Cannot find community","noUser":"Cannot find current user","noNode":"Cannot find node with id {nodeId}","noMessage":"Cannot find message with id {messageId}","userBanned":"We're sorry, but you have been banned from using this site.","userBannedReason":"You have been banned for the following reason: {reason}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/common/Loading/LoadingDot-0":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/common/Loading/LoadingDot-0","value":{"title":"Loading..."},"localOverride":false},"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/cmstNC05WEo0blc\"}":{"__typename":"AssociatedImage","url":"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/cmstNC05WEo0blc","height":512,"width":512,"mimeType":"image/png"},"Rank:rank:4":{"__typename":"Rank","id":"rank:4","position":2,"name":"Microsoft","color":"333333","icon":{"__ref":"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/cmstNC05WEo0blc\"}"},"rankStyle":"OUTLINE"},"User:user:1274243":{"__typename":"User","id":"user:1274243","uid":1274243,"login":"pratikpanda","deleted":false,"avatar":{"__typename":"UserAvatar","url":"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/dS0xMjc0MjQzLTMzOTI1MGk4NTAyREFGNDFEQzkxQTE2"},"rank":{"__ref":"Rank:rank:4"},"email":"","messagesCount":9,"biography":null,"topicsCount":6,"kudosReceivedCount":5,"kudosGivenCount":0,"kudosWeight":1,"registrationData":{"__typename":"RegistrationData","status":null,"registrationTime":"2022-01-12T22:29:31.502-08:00","confirmEmailStatus":null},"followersCount":null,"solutionsCount":0},"Category:category:Azure":{"__typename":"Category","id":"category:Azure","entityType":"CATEGORY","displayId":"Azure","nodeType":"category","depth":3,"title":"Azure","shortTitle":"Azure","parent":{"__ref":"Category:category:products-services"},"categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:top":{"__typename":"Category","id":"category:top","entityType":"CATEGORY","displayId":"top","nodeType":"category","depth":0,"title":"Top","shortTitle":"Top"},"Category:category:communities":{"__typename":"Category","id":"category:communities","entityType":"CATEGORY","displayId":"communities","nodeType":"category","depth":1,"parent":{"__ref":"Category:category:top"},"title":"Communities","shortTitle":"Communities"},"Category:category:products-services":{"__typename":"Category","id":"category:products-services","entityType":"CATEGORY","displayId":"products-services","nodeType":"category","depth":2,"parent":{"__ref":"Category:category:communities"},"title":"Products","shortTitle":"Products"},"Blog:board:AzureDevCommunityBlog":{"__typename":"Blog","id":"board:AzureDevCommunityBlog","entityType":"BLOG","displayId":"AzureDevCommunityBlog","nodeType":"board","depth":4,"conversationStyle":"BLOG","repliesProperties":{"__typename":"RepliesProperties","sortOrder":"REVERSE_PUBLISH_TIME","repliesFormat":"threaded"},"tagProperties":{"__typename":"TagNodeProperties","tagsEnabled":{"__typename":"PolicyResult","failureReason":null}},"requireTags":true,"tagType":"PRESET_ONLY","description":"","title":"Microsoft Developer Community Blog","shortTitle":"Microsoft Developer Community Blog","parent":{"__ref":"Category:category:Azure"},"ancestors":{"__typename":"CoreNodeConnection","edges":[{"__typename":"CoreNodeEdge","node":{"__ref":"Community:community:gxcuf89792"}},{"__typename":"CoreNodeEdge","node":{"__ref":"Category:category:communities"}},{"__typename":"CoreNodeEdge","node":{"__ref":"Category:category:products-services"}},{"__typename":"CoreNodeEdge","node":{"__ref":"Category:category:Azure"}}]},"userContext":{"__typename":"NodeUserContext","canAddAttachments":false,"canUpdateNode":false,"canPostMessages":false,"isSubscribed":false},"theme":{"__ref":"Theme:customTheme1"},"boardPolicies":{"__typename":"BoardPolicies","canViewSpamDashBoard":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.feature.moderation_spam.action.access_spam_quarantine.allowed.accessDenied","key":"error.lithium.policies.feature.moderation_spam.action.access_spam_quarantine.allowed.accessDenied","args":[]}},"canArchiveMessage":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.content_archivals.enable_content_archival_settings.accessDenied","key":"error.lithium.policies.content_archivals.enable_content_archival_settings.accessDenied","args":[]}},"canPublishArticleOnCreate":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.forums.policy_can_publish_on_create_workflow_action.accessDenied","key":"error.lithium.policies.forums.policy_can_publish_on_create_workflow_action.accessDenied","args":[]}}},"linkProperties":{"__typename":"LinkProperties","isExternalLinkWarningEnabled":false}},"BlogTopicMessage:message:4488249":{"__typename":"BlogTopicMessage","uid":4488249,"subject":"Building Interactive Agent UIs with AG-UI and Microsoft Agent Framework","id":"message:4488249","entityType":"BLOG_ARTICLE","eventPath":"category:Azure/category:products-services/category:communities/community:gxcuf89792board:AzureDevCommunityBlog/message:4488249","revisionNum":3,"repliesCount":8,"author":{"__ref":"User:user:1274243"},"depth":0,"hasGivenKudo":false,"board":{"__ref":"Blog:board:AzureDevCommunityBlog"},"conversation":{"__ref":"Conversation:conversation:4488249"},"messagePolicies":{"__typename":"MessagePolicies","canPublishArticleOnEdit":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.forums.policy_can_publish_on_edit_workflow_action.accessDenied","key":"error.lithium.policies.forums.policy_can_publish_on_edit_workflow_action.accessDenied","args":[]}},"canModerateSpamMessage":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.feature.moderation_spam.action.moderate_entity.allowed.accessDenied","key":"error.lithium.policies.feature.moderation_spam.action.moderate_entity.allowed.accessDenied","args":[]}},"canReply":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.forums.action.message.reply_to_entity.allow.accessDenied","key":"error.lithium.policies.forums.action.message.reply_to_entity.allow.accessDenied","args":[]}},"canAcceptSolution":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.accepted_solutions.action_allow.message.mark_as_accepted_solution.accessDenied","key":"error.lithium.policies.accepted_solutions.action_allow.message.mark_as_accepted_solution.accessDenied","args":[]}},"canRejectSolution":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.accepted_solutions.action_allow.message.unmark_as_accepted_solution.accessDenied","key":"error.lithium.policies.accepted_solutions.action_allow.message.unmark_as_accepted_solution.accessDenied","args":[]}},"canTag":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.labels.action.labelableentity.set_labels.allow.accessDenied","key":"error.lithium.policies.labels.action.labelableentity.set_labels.allow.accessDenied","args":[]}},"canEdit":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.forums.action_allow.edit_message.accessDenied","key":"error.lithium.policies.forums.action_allow.edit_message.accessDenied","args":[]}},"canKudo":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.kudos.action.entity.give_kudos.allow.accessDenied","key":"error.lithium.policies.kudos.action.entity.give_kudos.allow.accessDenied","args":[]}}},"contentWorkflow":{"__typename":"ContentWorkflow","state":"PUBLISH","scheduledPublishTime":null,"scheduledTimezone":null,"userContext":{"__typename":"MessageWorkflowContext","canSubmitForReview":null,"canEdit":false,"canRecall":null,"canSubmitForPublication":null,"canReturnToAuthor":null,"canPublish":null,"canReturnToReview":null,"canSchedule":false},"shortScheduledTimezone":null},"readOnly":false,"editFrozen":false,"showMoveIndicator":false,"moderationData":{"__ref":"ModerationData:moderation_data:4488249"},"teaser":"","body":"
## Introduction

\n
Picture this: You've built an AI agent that analyzes financial data. A user uploads a quarterly report and asks: _\"What are the top three expense categories?\"_ Behind the scenes, your agent parses the spreadsheet, aggregates thousands of rows, and generates visualizations. All in 20 seconds. But the user? They see a loading spinner. Nothing else. No \"reading file\" message, no \"analyzing data\" indicator, no hint that progress is being made. They start wondering: Is it frozen? Should I refresh?

\n
The problem isn't the agent's capabilities - it's the communication gap between the agent running on the backend and the user interface. When agents perform multi-step reasoning, call external APIs, or execute complex tool chains, users deserve to see what's happening. They need streaming updates, intermediate results, and transparent progress indicators. Yet most agent frameworks force developers to choose between simple request/response patterns or building custom solutions to stream updates to their UIs.

\n
This is where**AG-UI**comes in. AG-UI is a fairly new event-based protocol that standardizes how agents communicate with user interfaces. Instead of every framework and development team inventing their own streaming solution, AG-UI provides a shared vocabulary of structured events that work consistently across different agent implementations. When an agent starts processing, calls a tool, generates text, or encounters an error, the UI receives explicit, typed events in real time.

\n
The beauty of AG-UI is its framework-agnostic design. While this blog post demonstrates integration with**Microsoft Agent Framework (MAF)**, the same AG-UI protocol works with LangGraph, CrewAI, or any other compliant framework. Write your UI code once, and it works with any AG-UI-compliant backend. (Note: MAF supports both Python and .NET - this blog post focuses on the Python implementation.)

\n
## TL;DR

\n
**The Problem:** Users don't get real-time updates while AI agents work behind the scenes - no progress indicators, no transparency into tool calls, and no insight into what's happening.

\n
**The Solution:** AG-UI is an open, event-based protocol that standardizes real-time communication between AI agents and user interfaces. Instead of each development team and framework inventing custom streaming solutions, AG-UI provides a shared vocabulary of structured events (like TOOL_CALL_START, TEXT_MESSAGE_CONTENT, RUN_FINISHED) that work across any compliant framework.

\n
**Key Benefits:**

\n
\n*   **Framework-agnostic**- Write UI code once, works with LangGraph, Microsoft Agent Framework, CrewAI, and more
\n*   **Real-time observability**- See exactly what your agent is doing as it happens
\n*   **Server-Sent Events**- Built on standard HTTP for universal compatibility
\n*   **Protocol-managed state**- No manual conversation history tracking
\n
\n
**In This Post:** You'll learn why AG-UI exists, how it works, and build a complete working application using Microsoft Agent Framework with Python - from server setup to client implementation.

\n
## What You'll Learn

\n
This blog post walks through:

\n
\n*   **Why AG-UI exists** - how agent-UI communication has evolved and what problems current approaches couldn't solve
\n*   **How the protocol works**- the key design choices that make AG-UI simple, reliable, and framework-agnostic
\n*   **Protocol architecture**- the generic components and how AG-UI integrates with agent frameworks
\n*   **Building an AG-UI application**- a complete working example using Microsoft Agent Framework with server, client, and step-by-step setup
\n*   **Understanding events**- what happens under the hood when your agent runs and how to observe it
\n*   **Thinking in events**- how building with AG-UI differs from traditional APIs, and what benefits this brings
\n*   **Making the right choice**- when AG-UI is the right fit for your project and when alternatives might be better
\n
\n
**Estimated reading time:**15 minutes

\n
**Who this is for:**Developers building AI agents who want to provide real-time feedback to users, and teams evaluating standardized approaches to agent-UI communication

\n
To appreciate why AG-UI matters, we need to understand the journey that led to its creation. Let's trace how agent-UI communication has evolved through three distinct phases.

\n
## The Evolution of Agent-UI Communication

\n
AI agents have become more capable over time. As they evolved, the way they communicated with user interfaces had to evolve as well. Here's how this evolution unfolded.

\n
### Phase 1: Simple Request/Response

\n
In the early days of AI agent development, the interaction model was straightforward: send a question, wait for an answer, display the result. This synchronous approach mirrored traditional API calls and worked fine for simple scenarios.

\n`# Simple, but limiting\nresponse = agent.run(\"What's the weather in Paris?\")\ndisplay(response)  # User waits... and waits...`\n
**Works for:**Quick queries that complete in seconds, simple Q&A interactions where immediate feedback and interactivity aren't critical.

\n
**Breaks down:**When agents need to call multiple tools, perform multi-step reasoning, or process complex queries that take 30+ seconds. Users see nothing but a loading spinner, with no insight into what's happening or whether the agent is making progress. This creates a poor user experience and makes it impossible to show intermediate results or allow user intervention.

\n
Recognizing these limitations, development teams began experimenting with more sophisticated approaches.

\n
### Phase 2: Custom Streaming Solutions

\n
As agents became more sophisticated, teams recognized the need for incremental feedback and interactivity. Rather than waiting for the complete response, they implemented custom streaming solutions to show partial results as they became available.

\n`# Every team invents their own format\nfor chunk in agent.stream(\"What's the weather?\"):\n    display(chunk)  # But what about tool calls? Errors? Progress?`\n
This was a step forward for building interactive agent UIs, but each team solved the problem differently. Also, different frameworks had incompatible approaches - some streamed only text tokens, others sent structured JSON, and most provided no visibility into critical events like tool calls or errors.

\n
**The problem:**

\n
\n*   No standardization across frameworks - client code that works with LangGraph won't work with Crew AI, requiring separate implementations for each agent backend
\n*   Each implementation handles tool calls differently - some send nothing during tool execution, others send unstructured messages
\n*   Complex state management - clients must track conversation history, manage reconnections, and handle edge cases manually
\n
\n
The industry needed a better solution - a common protocol that could work across all frameworks while maintaining the benefits of streaming.

\n
### Phase 3: Standardized Protocol (AG-UI)

\n
AG-UI emerged as a response to the fragmentation problem. Instead of each framework and development team inventing their own streaming solution, AG-UI provides a shared vocabulary of events that work consistently across different agent implementations.

\n`# Standardized events everyone understands\nasync for event in agent.run_stream(\"What's the weather?\"):\n    if event.type == \"TEXT_MESSAGE_CONTENT\":\n        display_text(event.delta)\n    elif event.type == \"TOOL_CALL_START\":\n        show_tool_indicator(event.tool_name)\n    elif event.type == \"TOOL_CALL_RESULT\":\n        show_tool_result(event.result)`\n
The key difference is**structured observability**. Rather than guessing what the agent is doing from unstructured text, clients receive explicit events for every stage of execution: when the agent starts, when it generates text, when it calls a tool, when that tool completes, and when the entire run finishes.

\n
**What's different:**A standardized vocabulary of event types, complete observability into agent execution, and framework-agnostic clients that work with any AG-UI-compliant backend. You write your UI code once, and it works whether the backend uses Microsoft Agent Framework, LangGraph, or any other framework that speaks AG-UI.

\n
Now that we've seen why AG-UI emerged and what problems it solves, let's examine the specific design decisions that make the protocol work. These choices weren't arbitrary - each one addresses concrete challenges in building reliable, observable agent-UI communication.

\n
## The Design Decisions Behind AG-UI

\n
### Why Server-Sent Events (SSE)?

\n

| Aspect | WebSockets | SSE (AG-UI) |
| --- | --- | --- |
| Complexity | Bidirectional | Unidirectional (simpler) |
| Firewall/Proxy | Sometimes blocked | Standard HTTP |
| Reconnection | Manual implementation | Built-in browser support |
| Use case | Real-time games, chat | Agent responses (one-way) |

\n
For agent interactions, you typically only need server→client communication, making SSE a simpler choice.

\n
SSE solves the _transport_ problem - how events travel from server to client. But once connected, how does the protocol handle conversation state across multiple interactions?

\n
### Why Protocol-Managed Threads?

\n`# Without protocol threads (client manages):\nconversation_history = []\nconversation_history.append({\"role\": \"user\", \"content\": message})\nresponse = agent.complete(conversation_history)\nconversation_history.append({\"role\": \"assistant\", \"content\": response})\n# Complex, error-prone, doesn't work with multiple clients\n\n# With AG-UI (protocol manages):\nthread = agent.get_new_thread()  # Server creates and manages thread\nagent.run_stream(message, thread=thread)  # Server maintains context\n# Simple, reliable, shareable across clients`\n
With transport and state management handled, the final piece is the actual messages flowing through the connection. What information should the protocol communicate, and how should it be structured?

\n
### Why Standardized Event Types?

\n
Instead of parsing unstructured text, clients get typed events:

\n
\n*   RUN_STARTED- Agent begins (start loading UI)
\n*   TEXT_MESSAGE_CONTENT- Text chunk (stream to user)
\n*   TOOL_CALL_START- Tool invoked (show \"searching...\", \"calculating...\")
\n*   TOOL_CALL_RESULT- Tool finished (show result, update UI)
\n*   RUN_FINISHED- Complete (hide loading)
\n
\n
This lets UIs react intelligently without custom parsing logic.

\n
Now that we understand the protocol's design choices, let's see how these pieces fit together in a complete system.

\n
## Architecture Overview

\n
Here's how the components interact:

\n![Image 23: \"\"](https://techcommunity.microsoft.com/%22https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWQ0WU5kZA?image-dimensions=277x765&revision=3\%22)\n
The communication between these layers relies on a well-defined set of event types. Here are the core events that flow through the SSE connection:

\n
### Core Event Types

\n
AG-UI provides a standardized set of event types to describe what's happening during an agent's execution:

\n
\n*   RUN_STARTED- agent begins execution
\n*   TEXT_MESSAGE_START,TEXT_MESSAGE_CONTENT,TEXT_MESSAGE_END- streaming segments of text
\n*   TOOL_CALL_START,TOOL_CALL_ARGS,TOOL_CALL_END,TOOL_CALL_RESULT- tool execution events
\n*   RUN_FINISHED- agent has finished execution
\n*   RUN_ERROR- error information
\n
\n
This model lets the UI update as the agent runs, rather than waiting for the final response.

\n
The generic architecture above applies to any AG-UI implementation. Now let's see how this translates to Microsoft Agent Framework.

\n
## AG-UI with Microsoft Agent Framework

\n
While AG-UI is framework-agnostic, this blog post demonstrates integration with Microsoft Agent Framework (MAF) using Python. MAF is available in both**Python**and**.NET**, giving you flexibility to build AG-UI applications in your preferred language. Understanding how MAF implements the protocol will help you build your own applications or work with other compliant frameworks.

\n
### Integration Architecture

\n
The Microsoft Agent Framework integration involves several specialized layers that handle protocol translation and execution orchestration:

\n![Image 24: \"\"](https://techcommunity.microsoft.com/%22https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWpEdm5abA?image-dimensions=378x999&revision=3\%22)\n
**Understanding each layer:**

\n
\n*   **FastAPI Endpoint**- Handles HTTP requests and establishes SSE connections for streaming
\n*   **AgentFrameworkAgent**- Protocol wrapper that translates between AG-UI events and Agent Framework operations
\n*   **Orchestrators**- Manage execution flow, coordinate tool calling sequences, and handle state transitions
\n*   **ChatAgent**- Your agent implementation with instructions, tools, and business logic
\n*   **ChatClient**- Interface to the underlying language model (Azure OpenAI, OpenAI, or other providers)
\n
\n
The good news? When you call add_agent_framework_fastapi_endpoint, all the middleware layers are configured automatically. You simply provide your ChatAgent, and the integration handles protocol translation, event streaming, and state management behind the scenes.

\n
Now that we understand both the protocol architecture and the Microsoft Agent Framework integration, let's build a working application.

\n
## Hands-On: Building Your First AG-UI Application

\n
This section demonstrates how to build an AG-UI server and client using Microsoft Agent Framework and FastAPI.

\n
### Prerequisites

\n
Before building your first AG-UI application, ensure you have:

\n
\n*   **Python 3.10 or later**installed
\n*   **Basic understanding**of async/await patterns in Python
\n*   **Azure CLI**installed and authenticated (az login)
\n*   **Azure OpenAI service**endpoint and deployment configured ([setup guide](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/azure/ai-foundry/openai/how-to/create-resource/%22))
\n*   **Cognitive Services OpenAI Contributor**role for your Azure OpenAI resource
\n
\n
You'll also need to install the AG-UI integration package:

\n`pip install agent-framework-ag-ui --pre`\n
This automatically installs agent-framework-core,fastapi, and uvicorn as dependencies.

\n
With your environment configured, let's create the server that will host your agent and expose it via the AG-UI protocol.

\n
### Building the Server

\n
Let's create a FastAPI server that hosts an AI agent and exposes it via AG-UI:

\n`# server.py\nimport os\nfrom typing import Annotated\nfrom dotenv import load_dotenv\nfrom fastapi import FastAPI\nfrom pydantic import Field\nfrom agent_framework import ChatAgent, ai_function\nfrom agent_framework.azure import AzureOpenAIChatClient\nfrom agent_framework_ag_ui import add_agent_framework_fastapi_endpoint\nfrom azure.identity import DefaultAzureCredential\n\n# Load environment variables from .env file\nload_dotenv()\n\n# Validate environment configuration\nopenai_endpoint = os.getenv(\"AZURE_OPENAI_ENDPOINT\")\nmodel_deployment = os.getenv(\"AZURE_OPENAI_DEPLOYMENT_NAME\")\n\nif not openai_endpoint:\n    raise RuntimeError(\"Missing required environment variable: AZURE_OPENAI_ENDPOINT\")\nif not model_deployment:\n    raise RuntimeError(\"Missing required environment variable: AZURE_OPENAI_DEPLOYMENT_NAME\")\n\n# Define tools the agent can use\n@ai_function\ndef get_order_status(\n    order_id: Annotated[str, Field(description=\"The order ID to look up (e.g., ORD-001)\")]\n) -> dict:\n    \"\"\"Look up the status of a customer order.\n    \n    Returns order status, tracking number, and estimated delivery date.\n    \"\"\"\n    # Simulated order lookup\n    orders = {\n        \"ORD-001\": {\"status\": \"shipped\", \"tracking\": \"1Z999AA1\", \"eta\": \"Jan 25, 2026\"},\n        \"ORD-002\": {\"status\": \"processing\", \"tracking\": None, \"eta\": \"Jan 23, 2026\"},\n        \"ORD-003\": {\"status\": \"delivered\", \"tracking\": \"1Z999AA3\", \"eta\": \"Delivered Jan 20\"},\n    }\n    return orders.get(order_id, {\"status\": \"not_found\", \"message\": \"Order not found\"})\n\n# Initialize Azure OpenAI client\nchat_client = AzureOpenAIChatClient(\n    credential=DefaultAzureCredential(),\n    endpoint=openai_endpoint,\n    deployment_name=model_deployment,\n)\n\n# Configure the agent with custom instructions and tools\nagent = ChatAgent(\n    name=\"CustomerSupportAgent\",\n    instructions=\"\"\"You are a helpful customer support assistant. \n    \n    You have access to a get_order_status tool that can look up order information.\n    \n    IMPORTANT: When a user mentions an order ID (like ORD-001, ORD-002, etc.), \n    you MUST call the get_order_status tool to retrieve the actual order details.\n    Do NOT make up or guess order information.\n    \n    After calling get_order_status, provide the actual results to the user in a friendly format.\"\"\",\n    chat_client=chat_client,\n    tools=[get_order_status],\n)\n\n# Initialize FastAPI application\napp = FastAPI(\n    title=\"AG-UI Customer Support Server\",\n    description=\"Interactive AI agent server using AG-UI protocol with tool calling\"\n)\n\n# Mount the AG-UI endpoint\nadd_agent_framework_fastapi_endpoint(app, agent, path=\"/chat\")\n\ndef main():\n    \"\"\"Entry point for the AG-UI server.\"\"\"\n    import uvicorn\n    print(\"Starting AG-UI server on http://localhost:8000\")\n    uvicorn.run(app, host=\"0.0.0.0\", port=8000, log_level=\"info\")\n\n# Run the application\nif __name__ == \"__main__\":\n    main()`\n
**What's happening here:**

\n
\n*   We define a tool:get_order_status with the[AI​](https://techcommunity.microsoft.com/%22javascript:void(0)/%22)_function decorator
\n*   Use Annotated and Field for parameter descriptions to help the agent understand when and how to use the tool
\n*   We create an Azure OpenAI chat client with credential authentication
\n*   The ChatAgent is configured with domain-specific instructions and the tools parameter
\n*   add_agent_framework_fastapi_endpoint automatically handles SSE streaming and tool execution
\n*   The server exposes the agent at the/chat endpoint
\n
\n
> \n
> **Note:**This example uses Azure OpenAI, but AG-UI works with any chat model. You can also integrate with Azure AI Foundry's model catalog or use other LLM providers. Tool calling is supported by most modern LLMs including GPT-4, GPT-4o, and Claude models.
> 
> \n

\n
To run this server:

\n`# Set your Azure OpenAI credentials\nexport AZURE_OPENAI_ENDPOINT=\"https://your-resource.openai.azure.com/\"\nexport AZURE_OPENAI_DEPLOYMENT_NAME=\"gpt-4o\"\n\n# Start the server\npython server.py`\n
With your server running and exposing the AG-UI endpoint, the next step is building a client that can connect and consume the event stream.

\n
## Streaming Results to Clients

\n
With the server running, clients can connect and stream events as the agent processes requests. Here's a Python client that demonstrates the streaming capabilities:

\n`# client.py\nimport asyncio\nimport os\nfrom dotenv import load_dotenv\nfrom agent_framework import ChatAgent, FunctionCallContent, FunctionResultContent\nfrom agent_framework_ag_ui import AGUIChatClient\n\n# Load environment variables from .env file\nload_dotenv()\n\nasync def interactive_chat():\n    \"\"\"Interactive chat session with streaming responses.\"\"\"\n    \n    # Connect to the AG-UI server\n    base_url = os.getenv(\"AGUI_SERVER_URL\", \"http://localhost:8000/chat\")\n    print(f\"Connecting to: {base_url}\\n\")\n    \n    # Initialize the AG-UI client\n    client = AGUIChatClient(endpoint=base_url)\n    \n    # Create a local agent representation\n    agent = ChatAgent(chat_client=client)\n    \n    # Start a new conversation thread\n    conversation_thread = agent.get_new_thread()\n    \n    print(\"Chat started! Type 'exit' or 'quit' to end the session.\\n\")\n    \n    try:\n        while True:\n            # Collect user input\n            user_message = input(\"You: \")\n            \n            # Handle empty input\n            if not user_message.strip():\n                print(\"Please enter a message.\\n\")\n                continue\n            \n            # Check for exit commands\n            if user_message.lower() in [\"exit\", \"quit\", \"bye\"]:\n                print(\"\\nGoodbye!\")\n                break\n            \n            # Stream the agent's response\n            print(\"Agent: \", end=\"\", flush=True)\n            \n            # Track tool calls to avoid duplicate prints\n            seen_tools = set()\n            \n            async for update in agent.run_stream(user_message, thread=conversation_thread):\n                # Display text content\n                if update.text:\n                    print(update.text, end=\"\", flush=True)\n                \n                # Display tool calls and results\n                for content in update.contents:\n                    if isinstance(content, FunctionCallContent):\n                        # Only print each tool call once\n                        if content.call_id not in seen_tools:\n                            seen_tools.add(content.call_id)\n                            print(f\"\\n[Calling tool: {content.name}]\", flush=True)\n                    elif isinstance(content, FunctionResultContent):\n                        # Only print each result once\n                        result_id = f\"result_{content.call_id}\"\n                        if result_id not in seen_tools:\n                            seen_tools.add(result_id)\n                            result_text = content.result if isinstance(content.result, str) else str(content.result)\n                            print(f\"[Tool result: {result_text}]\", flush=True)\n            \n            print(\"\\n\")  # New line after response completes\n            \n    except KeyboardInterrupt:\n        print(\"\\n\\nChat interrupted by user.\")\n    except ConnectionError as e:\n        print(f\"\\nConnection error: {e}\")\n        print(\"Make sure the server is running.\")\n    except Exception as e:\n        print(f\"\\nUnexpected error: {e}\")\n\ndef main():\n    \"\"\"Entry point for the AG-UI client.\"\"\"\n    asyncio.run(interactive_chat())\n\nif __name__ == \"__main__\":\n    main()`\n
**Key features:**

\n
\n*   The client connects to the AG-UI endpoint using AGUIChatClient with the endpoint parameter
\n*   run_stream()yields updates containing text and content as they arrive
\n*   Tool calls are detected using FunctionCallContent and displayed with[Calling tool: ...]
\n*   Tool results are detected using FunctionResultContent and displayed with[Tool result: ...]
\n*   Deduplication logic (seen_tools set) prevents printing the same tool call multiple times as it streams
\n*   Thread management maintains conversation context across messages
\n*   Graceful error handling for connection issues
\n
\n
To use the client:

\n`# Optional: specify custom server URL\nexport AGUI_SERVER_URL=\"http://localhost:8000/chat\"\n\n# Start the interactive chat\npython client.py`\n
**Example Session:**

\n`Connecting to: http://localhost:8000/chat\n\nChat started! Type 'exit' or 'quit' to end the session.\n\nYou: What's the status of order ORD-001?\nAgent: \n[Calling tool: get_order_status]\n[Tool result: {\"status\": \"shipped\", \"tracking\": \"1Z999AA1\", \"eta\": \"Jan 25, 2026\"}]\nYour order ORD-001 has been shipped! \n\n- Tracking Number: 1Z999AA1\n- Estimated Delivery Date: January 25, 2026\n\nYou can use the tracking number to monitor the delivery progress.\n\nYou: Can you check ORD-002?\nAgent: \n[Calling tool: get_order_status]\n[Tool result: {\"status\": \"processing\", \"tracking\": null, \"eta\": \"Jan 23, 2026\"}]\nYour order ORD-002 is currently being processed.\n\n- Status: Processing\n- Estimated Delivery: January 23, 2026\n\nYour order should ship soon, and you'll receive a tracking number once it's on the way.\n\nYou: exit\n\nGoodbye!`\n
The client we just built handles events at a high level, abstracting away the details. But what's actually flowing through that SSE connection? Let's peek under the hood.

\n
## Event Types You'll See

\n
As the server streams back responses, clients receive a series of structured events. If you were to observe the raw SSE stream (e.g., using curl), you'd see events like:

\n`curl -N http://localhost:8000/chat \\\n  -H \"Content-Type: application/json\" \\\n  -H \"Accept: text/event-stream\" \\\n  -d '{\"messages\": [{\"role\": \"user\", \"content\": \"What'\\''s the status of order ORD-001?\"}]}'`\n
**Sample event stream (with tool calling):**

\n`data: {\"type\":\"RUN_STARTED\",\"threadId\":\"eb4d9850-14ef-446c-af4b-23037acda9e8\",\"runId\":\"chatcmpl-xyz\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_START\",\"messageId\":\"e8648880-a9ff-4178-a17d-4a6d3ec3d39c\",\"role\":\"assistant\"}\n\ndata: {\"type\":\"TOOL_CALL_START\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"toolCallName\":\"get_order_status\",\"parentMessageId\":\"e8648880-a9ff-4178-a17d-4a6d3ec3d39c\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"{\\\"\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"order\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"_id\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"\\\":\\\"\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"ORD\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"-\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"001\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"\\\"}\"}\n\ndata: {\"type\":\"TOOL_CALL_END\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\"}\n\ndata: {\"type\":\"TOOL_CALL_RESULT\",\"messageId\":\"f048cb0a-a049-4a51-9403-a05e4820438a\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"content\":\"{\\\"status\\\": \\\"shipped\\\", \\\"tracking\\\": \\\"1Z999AA1\\\", \\\"eta\\\": \\\"Jan 25, 2026\\\"}\",\"role\":\"tool\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_START\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"role\":\"assistant\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"Your\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" order\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" ORD\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"-\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"001\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" has\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" been\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" shipped\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"!\"}\n\n... (additional TEXT_MESSAGE_CONTENT events streaming the response) ...\n\ndata: {\"type\":\"TEXT_MESSAGE_END\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\"}\n\ndata: {\"type\":\"RUN_FINISHED\",\"threadId\":\"eb4d9850-14ef-446c-af4b-23037acda9e8\",\"runId\":\"chatcmpl-xyz\"}`\n
**Understanding the flow:**

\n
\n 1.   RUN_STARTED- Agent begins processing the request
\n 2.   TEXT_MESSAGE_START- First message starts (will contain tool calls)
\n 3.   TOOL_CALL_START- Agent invokes the get_order_status tool
\n 4.   Multiple TOOL_CALL_ARGS events - Arguments stream incrementally as JSON chunks ({\"order_id\":\"ORD-001\"})
\n 5.   TOOL_CALL_END- Tool invocation structure complete
\n 6.   TOOL_CALL_RESULT- Tool execution finished with result data
\n 7.   TEXT_MESSAGE_START- Second message starts (the final response)
\n 8.   Multiple TEXT_MESSAGE_CONTENT events - Response text streams word-by-word
\n 9.   TEXT_MESSAGE_END- Response message complete
\n 10.   RUN_FINISHED- Entire run completed successfully
\n
\n
This granular event model enables rich UI experiences - showing tool execution indicators (\"Searching...\", \"Calculating...\"), displaying intermediate results, and providing complete transparency into the agent's reasoning process.

\n
Seeing the raw events helps, but truly working with AG-UI requires a shift in how you think about agent interactions. Let's explore this conceptual change.

\n
## The Mental Model Shift

\n
### Traditional API Thinking

\n`# Imperative: Call and wait\nresponse = agent.run(\"What's 2+2?\")\nprint(response)  # \"The answer is 4\"`\n
**Mental model:**Function call with return value

\n
### AG-UI Thinking

\n`# Reactive: Subscribe to events\nasync for event in agent.run_stream(\"What's 2+2?\"):\n    match event.type:\n        case \"RUN_STARTED\":\n            show_loading()\n        case \"TEXT_MESSAGE_CONTENT\":\n            display_chunk(event.delta)\n        case \"RUN_FINISHED\":\n            hide_loading()`\n
**Mental model:**Observable stream of events

\n
This shift feels similar to:

\n
\n*   Moving from synchronous to async code
\n*   Moving from REST to event-driven architecture
\n*   Moving from polling to pub/sub
\n
\n
This mental shift isn't just philosophical - it unlocks concrete benefits that weren't possible with request/response patterns.

\n
### What You Gain

\n
#### Observability

\n`# You can SEE what the agent is doing\nTOOL_CALL_START: \"get_order_status\"\nTOOL_CALL_ARGS: {\"order_id\": \"ORD-001\"}\nTOOL_CALL_RESULT: {\"status\": \"shipped\", \"tracking\": \"1Z999AA1\", \"eta\": \"Jan 25, 2026\"}\nTEXT_MESSAGE_START: \"Your order ORD-001 has been shipped...\"`\n
#### Interruptibility

\n`# Future: Cancel long-running operations\nasync for event in agent.run_stream(query):\n    if user_clicked_cancel:\n        await agent.cancel(thread_id, run_id)\n        break`\n
#### Transparency

\n`# Users see the reasoning process\n\"Looking up order ORD-001...\"\n\"Order found: Status is 'shipped'\"\n\"Retrieving tracking information...\"\n\"Your order has been shipped with tracking number 1Z999AA1...\"`\n
To put these benefits in context, here's how AG-UI compares to traditional approaches across key dimensions:

\n
### AG-UI vs. Traditional Approaches

\n

| Aspect | Traditional REST | Custom Streaming | AG-UI |
| --- | --- | --- | --- |
| Connection Model | Request/Response | Varies | Server-Sent Events |
| State Management | Manual | Manual | Protocol-managed |
| Tool Calling | Invisible | Custom format | Standardized events |
| Framework | Varies | Framework-locked | Framework-agnostic |
| Browser Support | Universal | Varies | Universal |
| Implementation | Simple | Complex | Moderate |
| Ecosystem | N/A | Isolated | Growing |

\n
You've now seen AG-UI's design principles, implementation details, and conceptual foundations. But the most important question remains: should you actually use it?

\n
## Conclusion: Is AG-UI Right for Your Project?

\n
AG-UI represents a shift toward standardized, observable agent interactions. Before adopting it, understand where the protocol stands and whether it fits your needs.

\n
### Protocol Maturity

\n
The protocol is stable enough for production use but still evolving:

\n
**Ready now:**Core specification stable, Microsoft Agent Framework integration available, FastAPI/Python implementation mature, basic streaming and threading work reliably.

\n
### Choose AG-UI If You

\n
\n*   **Building new agent projects**- No legacy API to maintain, want future compatibility with emerging ecosystem
\n*   **Need streaming observability**- Multi-step workflows where users benefit from seeing each stage of execution
\n*   **Want framework flexibility**- Same client code works with any AG-UI-compliant backend
\n*   **Comfortable with evolving standards**- Can adapt to protocol changes as it matures
\n
\n
### Stick with Alternatives If You

\n
\n*   **Have working solutions**- Custom streaming working well, migration cost not justified
\n*   **Need guaranteed stability**- Mission-critical systems where breaking changes are unacceptable
\n*   **Build simple agents**- Single-step request/response without tool calling or streaming needs
\n*   **Risk-averse environment**- Large existing implementations where proven approaches are required
\n
\n
Beyond individual project decisions, it's worth considering AG-UI's role in the broader ecosystem.

\n
### The Bigger Picture

\n
While this blog post focused on Microsoft Agent Framework, AG-UI's true power lies in its broader mission: creating a common language for agent-UI communication across the entire ecosystem. As more frameworks adopt it, the real value emerges:**write your UI once, work with any compliant agent framework**.

\n
Think of it like GraphQL for APIs or OpenAPI for REST - a standardization layer that benefits the entire ecosystem.

\n
**The protocol is young, but the problem it solves is real.**Whether you adopt it now or wait for broader adoption, understanding AG-UI helps you make informed architectural decisions for your agent applications.

\n
Ready to dive deeper? Here are the official resources to continue your AG-UI journey.

\n
## Resources

\n
### AG-UI & Microsoft Agent Framework

\n
\n*   [Getting Started with AG-UI (Microsoft Learn)](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/getting-started?pivots=programming-language-python\%22)- Official tutorial
\n*   [AG-UI Integration Overview](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/?pivots=programming-language-python\%22)- Architecture and concepts
\n*   [AG-UI Protocol Specification](https://techcommunity.microsoft.com/%22https://docs.ag-ui.com/introduction/%22)- Official protocol documentation
\n*   [Backend Tool Rendering](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/backend-tool-rendering?pivots=programming-language-python\%22)- Adding function tools
\n*   [Security Considerations](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/security-considerations/%22)- Production security guidance
\n*   [Microsoft Agent Framework Documentation](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/overview/agent-framework-overview/%22)- Framework overview
\n*   [AG-UI Dojo Examples](https://techcommunity.microsoft.com/%22https://dojo.ag-ui.com/microsoft-agent-framework-dotnet/%22)- Live demonstrations
\n
\n
### UI Components & Integration

\n
\n*   [CopilotKit for Microsoft Agent Framework](https://techcommunity.microsoft.com/%22https://docs.copilotkit.ai/microsoft-agent-framework/%22)- React component library
\n
\n
### Community & Support

\n
\n*   [Microsoft Q&A](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/answers//%22)- Community support
\n*   [Agent Framework GitHub](https://techcommunity.microsoft.com/%22https://github.com/microsoft/agent-framework/%22)- Source code and issues
\n
\n
### Related Technologies

\n
\n*   [Azure AI Foundry Documentation](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/azure/ai-foundry//%22)- Azure AI platform
\n*   [FastAPI Documentation](https://techcommunity.microsoft.com/%22https://fastapi.tiangolo.com//%22)- Web framework
\n*   [Server-Sent Events (SSE) Specification](https://techcommunity.microsoft.com/%22https://html.spec.whatwg.org/multipage/server-sent-events.html/%22)- Protocol standard
\n
\n
_This blog post introduces AG-UI with Microsoft Agent Framework, focusing on fundamental concepts and building your first interactive agent application._

","body@stringLength":"55750","rawBody":"
## Introduction

\n
Picture this: You've built an AI agent that analyzes financial data. A user uploads a quarterly report and asks: _\"What are the top three expense categories?\"_ Behind the scenes, your agent parses the spreadsheet, aggregates thousands of rows, and generates visualizations. All in 20 seconds. But the user? They see a loading spinner. Nothing else. No \"reading file\" message, no \"analyzing data\" indicator, no hint that progress is being made. They start wondering: Is it frozen? Should I refresh?

\n
The problem isn't the agent's capabilities - it's the communication gap between the agent running on the backend and the user interface. When agents perform multi-step reasoning, call external APIs, or execute complex tool chains, users deserve to see what's happening. They need streaming updates, intermediate results, and transparent progress indicators. Yet most agent frameworks force developers to choose between simple request/response patterns or building custom solutions to stream updates to their UIs.

\n
This is where**AG-UI**comes in. AG-UI is a fairly new event-based protocol that standardizes how agents communicate with user interfaces. Instead of every framework and development team inventing their own streaming solution, AG-UI provides a shared vocabulary of structured events that work consistently across different agent implementations. When an agent starts processing, calls a tool, generates text, or encounters an error, the UI receives explicit, typed events in real time.

\n
The beauty of AG-UI is its framework-agnostic design. While this blog post demonstrates integration with**Microsoft Agent Framework (MAF)**, the same AG-UI protocol works with LangGraph, CrewAI, or any other compliant framework. Write your UI code once, and it works with any AG-UI-compliant backend. (Note: MAF supports both Python and .NET - this blog post focuses on the Python implementation.)

\n
## TL;DR

\n
**The Problem:** Users don't get real-time updates while AI agents work behind the scenes - no progress indicators, no transparency into tool calls, and no insight into what's happening.

\n
**The Solution:** AG-UI is an open, event-based protocol that standardizes real-time communication between AI agents and user interfaces. Instead of each development team and framework inventing custom streaming solutions, AG-UI provides a shared vocabulary of structured events (like TOOL_CALL_START, TEXT_MESSAGE_CONTENT, RUN_FINISHED) that work across any compliant framework.

\n
**Key Benefits:**

\n
\n*   **Framework-agnostic**- Write UI code once, works with LangGraph, Microsoft Agent Framework, CrewAI, and more
\n*   **Real-time observability**- See exactly what your agent is doing as it happens
\n*   **Server-Sent Events**- Built on standard HTTP for universal compatibility
\n*   **Protocol-managed state**- No manual conversation history tracking
\n
\n
**In This Post:** You'll learn why AG-UI exists, how it works, and build a complete working application using Microsoft Agent Framework with Python - from server setup to client implementation.

\n
## What You'll Learn

\n
This blog post walks through:

\n
\n*   **Why AG-UI exists** - how agent-UI communication has evolved and what problems current approaches couldn't solve
\n*   **How the protocol works**- the key design choices that make AG-UI simple, reliable, and framework-agnostic
\n*   **Protocol architecture**- the generic components and how AG-UI integrates with agent frameworks
\n*   **Building an AG-UI application**- a complete working example using Microsoft Agent Framework with server, client, and step-by-step setup
\n*   **Understanding events**- what happens under the hood when your agent runs and how to observe it
\n*   **Thinking in events**- how building with AG-UI differs from traditional APIs, and what benefits this brings
\n*   **Making the right choice**- when AG-UI is the right fit for your project and when alternatives might be better
\n
\n
**Estimated reading time:**15 minutes

\n
**Who this is for:**Developers building AI agents who want to provide real-time feedback to users, and teams evaluating standardized approaches to agent-UI communication

\n
To appreciate why AG-UI matters, we need to understand the journey that led to its creation. Let's trace how agent-UI communication has evolved through three distinct phases.

\n
## The Evolution of Agent-UI Communication

\n
AI agents have become more capable over time. As they evolved, the way they communicated with user interfaces had to evolve as well. Here's how this evolution unfolded.

\n
### Phase 1: Simple Request/Response

\n
In the early days of AI agent development, the interaction model was straightforward: send a question, wait for an answer, display the result. This synchronous approach mirrored traditional API calls and worked fine for simple scenarios.

\n# Simple, but limiting\nresponse = agent.run(\"What's the weather in Paris?\")\ndisplay(response) # User waits... and waits...\n
**Works for:**Quick queries that complete in seconds, simple Q&A interactions where immediate feedback and interactivity aren't critical.

\n
**Breaks down:**When agents need to call multiple tools, perform multi-step reasoning, or process complex queries that take 30+ seconds. Users see nothing but a loading spinner, with no insight into what's happening or whether the agent is making progress. This creates a poor user experience and makes it impossible to show intermediate results or allow user intervention.

\n
Recognizing these limitations, development teams began experimenting with more sophisticated approaches.

\n
### Phase 2: Custom Streaming Solutions

\n
As agents became more sophisticated, teams recognized the need for incremental feedback and interactivity. Rather than waiting for the complete response, they implemented custom streaming solutions to show partial results as they became available.

\n# Every team invents their own format\nfor chunk in agent.stream(\"What's the weather?\"):\n display(chunk) # But what about tool calls? Errors? Progress?\n
This was a step forward for building interactive agent UIs, but each team solved the problem differently. Also, different frameworks had incompatible approaches - some streamed only text tokens, others sent structured JSON, and most provided no visibility into critical events like tool calls or errors.

\n
**The problem:**

\n
\n*   No standardization across frameworks - client code that works with LangGraph won't work with Crew AI, requiring separate implementations for each agent backend
\n*   Each implementation handles tool calls differently - some send nothing during tool execution, others send unstructured messages
\n*   Complex state management - clients must track conversation history, manage reconnections, and handle edge cases manually
\n
\n
The industry needed a better solution - a common protocol that could work across all frameworks while maintaining the benefits of streaming.

\n
### Phase 3: Standardized Protocol (AG-UI)

\n
AG-UI emerged as a response to the fragmentation problem. Instead of each framework and development team inventing their own streaming solution, AG-UI provides a shared vocabulary of events that work consistently across different agent implementations.

\n# Standardized events everyone understands\nasync for event in agent.run_stream(\"What's the weather?\"):\n if event.type == \"TEXT_MESSAGE_CONTENT\":\n display_text(event.delta)\n elif event.type == \"TOOL_CALL_START\":\n show_tool_indicator(event.tool_name)\n elif event.type == \"TOOL_CALL_RESULT\":\n show_tool_result(event.result)\n
The key difference is**structured observability**. Rather than guessing what the agent is doing from unstructured text, clients receive explicit events for every stage of execution: when the agent starts, when it generates text, when it calls a tool, when that tool completes, and when the entire run finishes.

\n
**What's different:**A standardized vocabulary of event types, complete observability into agent execution, and framework-agnostic clients that work with any AG-UI-compliant backend. You write your UI code once, and it works whether the backend uses Microsoft Agent Framework, LangGraph, or any other framework that speaks AG-UI.

\n
Now that we've seen why AG-UI emerged and what problems it solves, let's examine the specific design decisions that make the protocol work. These choices weren't arbitrary - each one addresses concrete challenges in building reliable, observable agent-UI communication.

\n
## The Design Decisions Behind AG-UI

\n
### Why Server-Sent Events (SSE)?

\n

Aspect WebSockets SSE (AG-UI)Complexity Bidirectional Unidirectional (simpler)Firewall/Proxy Sometimes blocked Standard HTTP Reconnection Manual implementation Built-in browser support Use case Real-time games, chat Agent responses (one-way)

\n
For agent interactions, you typically only need server→client communication, making SSE a simpler choice.

\n
SSE solves the _transport_ problem - how events travel from server to client. But once connected, how does the protocol handle conversation state across multiple interactions?

\n
### Why Protocol-Managed Threads?

\n# Without protocol threads (client manages):\nconversation_history = []\nconversation_history.append({\"role\": \"user\", \"content\": message})\nresponse = agent.complete(conversation_history)\nconversation_history.append({\"role\": \"assistant\", \"content\": response})\n# Complex, error-prone, doesn't work with multiple clients\n\n# With AG-UI (protocol manages):\nthread = agent.get_new_thread() # Server creates and manages thread\nagent.run_stream(message, thread=thread) # Server maintains context\n# Simple, reliable, shareable across clients\n
With transport and state management handled, the final piece is the actual messages flowing through the connection. What information should the protocol communicate, and how should it be structured?

\n
### Why Standardized Event Types?

\n
Instead of parsing unstructured text, clients get typed events:

\n
\n*   RUN_STARTED- Agent begins (start loading UI)
\n*   TEXT_MESSAGE_CONTENT- Text chunk (stream to user)
\n*   TOOL_CALL_START- Tool invoked (show \"searching...\", \"calculating...\")
\n*   TOOL_CALL_RESULT- Tool finished (show result, update UI)
\n*   RUN_FINISHED- Complete (hide loading)
\n
\n
This lets UIs react intelligently without custom parsing logic.

\n
Now that we understand the protocol's design choices, let's see how these pieces fit together in a complete system.

\n
## Architecture Overview

\n
Here's how the components interact:

\n\n
The communication between these layers relies on a well-defined set of event types. Here are the core events that flow through the SSE connection:

\n
### Core Event Types

\n
AG-UI provides a standardized set of event types to describe what's happening during an agent's execution:

\n
\n*   RUN_STARTED- agent begins execution
\n*   TEXT_MESSAGE_START,TEXT_MESSAGE_CONTENT,TEXT_MESSAGE_END- streaming segments of text
\n*   TOOL_CALL_START,TOOL_CALL_ARGS,TOOL_CALL_END,TOOL_CALL_RESULT- tool execution events
\n*   RUN_FINISHED- agent has finished execution
\n*   RUN_ERROR- error information
\n
\n
This model lets the UI update as the agent runs, rather than waiting for the final response.

\n
The generic architecture above applies to any AG-UI implementation. Now let's see how this translates to Microsoft Agent Framework.

\n
## AG-UI with Microsoft Agent Framework

\n
While AG-UI is framework-agnostic, this blog post demonstrates integration with Microsoft Agent Framework (MAF) using Python. MAF is available in both**Python**and**.NET**, giving you flexibility to build AG-UI applications in your preferred language. Understanding how MAF implements the protocol will help you build your own applications or work with other compliant frameworks.

\n
### Integration Architecture

\n
The Microsoft Agent Framework integration involves several specialized layers that handle protocol translation and execution orchestration:

\n\n
**Understanding each layer:**

\n
\n*   **FastAPI Endpoint**- Handles HTTP requests and establishes SSE connections for streaming
\n*   **AgentFrameworkAgent**- Protocol wrapper that translates between AG-UI events and Agent Framework operations
\n*   **Orchestrators**- Manage execution flow, coordinate tool calling sequences, and handle state transitions
\n*   **ChatAgent**- Your agent implementation with instructions, tools, and business logic
\n*   **ChatClient**- Interface to the underlying language model (Azure OpenAI, OpenAI, or other providers)
\n
\n
The good news? When you call add_agent_framework_fastapi_endpoint, all the middleware layers are configured automatically. You simply provide your ChatAgent, and the integration handles protocol translation, event streaming, and state management behind the scenes.

\n
Now that we understand both the protocol architecture and the Microsoft Agent Framework integration, let's build a working application.

\n
## Hands-On: Building Your First AG-UI Application

\n
This section demonstrates how to build an AG-UI server and client using Microsoft Agent Framework and FastAPI.

\n
### Prerequisites

\n
Before building your first AG-UI application, ensure you have:

\n
\n*   **Python 3.10 or later**installed
\n*   **Basic understanding**of async/await patterns in Python
\n*   **Azure CLI**installed and authenticated (az login)
\n*   **Azure OpenAI service**endpoint and deployment configured ([setup guide](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/azure/ai-foundry/openai/how-to/create-resource/%22))
\n*   **Cognitive Services OpenAI Contributor**role for your Azure OpenAI resource
\n
\n
You'll also need to install the AG-UI integration package:

\n pip install agent-framework-ag-ui --pre\n
This automatically installs agent-framework-core,fastapi, and uvicorn as dependencies.

\n
With your environment configured, let's create the server that will host your agent and expose it via the AG-UI protocol.

\n
### Building the Server

\n
Let's create a FastAPI server that hosts an AI agent and exposes it via AG-UI:

\n# server.py\nimport os\nfrom typing import Annotated\nfrom dotenv import load_dotenv\nfrom fastapi import FastAPI\nfrom pydantic import Field\nfrom agent_framework import ChatAgent, ai_function\nfrom agent_framework.azure import AzureOpenAIChatClient\nfrom agent_framework_ag_ui import add_agent_framework_fastapi_endpoint\nfrom azure.identity import DefaultAzureCredential\n\n# Load environment variables from .env file\nload_dotenv()\n\n# Validate environment configuration\nopenai_endpoint = os.getenv(\"AZURE_OPENAI_ENDPOINT\")\nmodel_deployment = os.getenv(\"AZURE_OPENAI_DEPLOYMENT_NAME\")\n\nif not openai_endpoint:\n raise RuntimeError(\"Missing required environment variable: AZURE_OPENAI_ENDPOINT\")\nif not model_deployment:\n raise RuntimeError(\"Missing required environment variable: AZURE_OPENAI_DEPLOYMENT_NAME\")\n\n# Define tools the agent can use\n@ai_function\ndef get_order_status(\n order_id: Annotated[str, Field(description=\"The order ID to look up (e.g., ORD-001)\")]\n) -> dict:\n \"\"\"Look up the status of a customer order.\n \n Returns order status, tracking number, and estimated delivery date.\n \"\"\"\n # Simulated order lookup\n orders = {\n \"ORD-001\": {\"status\": \"shipped\", \"tracking\": \"1Z999AA1\", \"eta\": \"Jan 25, 2026\"},\n \"ORD-002\": {\"status\": \"processing\", \"tracking\": None, \"eta\": \"Jan 23, 2026\"},\n \"ORD-003\": {\"status\": \"delivered\", \"tracking\": \"1Z999AA3\", \"eta\": \"Delivered Jan 20\"},\n }\n return orders.get(order_id, {\"status\": \"not_found\", \"message\": \"Order not found\"})\n\n# Initialize Azure OpenAI client\nchat_client = AzureOpenAIChatClient(\n credential=DefaultAzureCredential(),\n endpoint=openai_endpoint,\n deployment_name=model_deployment,\n)\n\n# Configure the agent with custom instructions and tools\nagent = ChatAgent(\n name=\"CustomerSupportAgent\",\n instructions=\"\"\"You are a helpful customer support assistant. \n \n You have access to a get_order_status tool that can look up order information.\n \n IMPORTANT: When a user mentions an order ID (like ORD-001, ORD-002, etc.), \n you MUST call the get_order_status tool to retrieve the actual order details.\n Do NOT make up or guess order information.\n \n After calling get_order_status, provide the actual results to the user in a friendly format.\"\"\",\n chat_client=chat_client,\n tools=[get_order_status],\n)\n\n# Initialize FastAPI application\napp = FastAPI(\n title=\"AG-UI Customer Support Server\",\n description=\"Interactive AI agent server using AG-UI protocol with tool calling\"\n)\n\n# Mount the AG-UI endpoint\nadd_agent_framework_fastapi_endpoint(app, agent, path=\"/chat\")\n\ndef main():\n \"\"\"Entry point for the AG-UI server.\"\"\"\n import uvicorn\n print(\"Starting AG-UI server on http://localhost:8000\")\n uvicorn.run(app, host=\"0.0.0.0\", port=8000, log_level=\"info\")\n\n# Run the application\nif __name__ == \"__main__\":\n main()\n
**What's happening here:**

\n
\n*   We define a tool:get_order_status with the​_function decorator
\n*   Use Annotated and Field for parameter descriptions to help the agent understand when and how to use the tool
\n*   We create an Azure OpenAI chat client with credential authentication
\n*   The ChatAgent is configured with domain-specific instructions and the tools parameter
\n*   add_agent_framework_fastapi_endpoint automatically handles SSE streaming and tool execution
\n*   The server exposes the agent at the/chat endpoint
\n
\n
> \n
> **Note:**This example uses Azure OpenAI, but AG-UI works with any chat model. You can also integrate with Azure AI Foundry's model catalog or use other LLM providers. Tool calling is supported by most modern LLMs including GPT-4, GPT-4o, and Claude models.
> 
> \n

\n
To run this server:

\n# Set your Azure OpenAI credentials\nexport AZURE_OPENAI_ENDPOINT=\"https://your-resource.openai.azure.com/\"\nexport AZURE_OPENAI_DEPLOYMENT_NAME=\"gpt-4o\"\n\n# Start the server\npython server.py\n
With your server running and exposing the AG-UI endpoint, the next step is building a client that can connect and consume the event stream.

\n
## Streaming Results to Clients

\n
With the server running, clients can connect and stream events as the agent processes requests. Here's a Python client that demonstrates the streaming capabilities:

\n# client.py\nimport asyncio\nimport os\nfrom dotenv import load_dotenv\nfrom agent_framework import ChatAgent, FunctionCallContent, FunctionResultContent\nfrom agent_framework_ag_ui import AGUIChatClient\n\n# Load environment variables from .env file\nload_dotenv()\n\nasync def interactive_chat():\n \"\"\"Interactive chat session with streaming responses.\"\"\"\n \n # Connect to the AG-UI server\n base_url = os.getenv(\"AGUI_SERVER_URL\", \"http://localhost:8000/chat\")\n print(f\"Connecting to: {base_url}\\n\")\n \n # Initialize the AG-UI client\n client = AGUIChatClient(endpoint=base_url)\n \n # Create a local agent representation\n agent = ChatAgent(chat_client=client)\n \n # Start a new conversation thread\n conversation_thread = agent.get_new_thread()\n \n print(\"Chat started! Type 'exit' or 'quit' to end the session.\\n\")\n \n try:\n while True:\n # Collect user input\n user_message = input(\"You: \")\n \n # Handle empty input\n if not user_message.strip():\n print(\"Please enter a message.\\n\")\n continue\n \n # Check for exit commands\n if user_message.lower() in [\"exit\", \"quit\", \"bye\"]:\n print(\"\\nGoodbye!\")\n break\n \n # Stream the agent's response\n print(\"Agent: \", end=\"\", flush=True)\n \n # Track tool calls to avoid duplicate prints\n seen_tools = set()\n \n async for update in agent.run_stream(user_message, thread=conversation_thread):\n # Display text content\n if update.text:\n print(update.text, end=\"\", flush=True)\n \n # Display tool calls and results\n for content in update.contents:\n if isinstance(content, FunctionCallContent):\n # Only print each tool call once\n if content.call_id not in seen_tools:\n seen_tools.add(content.call_id)\n print(f\"\\n[Calling tool: {content.name}]\", flush=True)\n elif isinstance(content, FunctionResultContent):\n # Only print each result once\n result_id = f\"result_{content.call_id}\"\n if result_id not in seen_tools:\n seen_tools.add(result_id)\n result_text = content.result if isinstance(content.result, str) else str(content.result)\n print(f\"[Tool result: {result_text}]\", flush=True)\n \n print(\"\\n\") # New line after response completes\n \n except KeyboardInterrupt:\n print(\"\\n\\nChat interrupted by user.\")\n except ConnectionError as e:\n print(f\"\\nConnection error: {e}\")\n print(\"Make sure the server is running.\")\n except Exception as e:\n print(f\"\\nUnexpected error: {e}\")\n\ndef main():\n \"\"\"Entry point for the AG-UI client.\"\"\"\n asyncio.run(interactive_chat())\n\nif __name__ == \"__main__\":\n main()\n
**Key features:**

\n
\n*   The client connects to the AG-UI endpoint using AGUIChatClient with the endpoint parameter
\n*   run_stream()yields updates containing text and content as they arrive
\n*   Tool calls are detected using FunctionCallContent and displayed with[Calling tool: ...]
\n*   Tool results are detected using FunctionResultContent and displayed with[Tool result: ...]
\n*   Deduplication logic (seen_tools set) prevents printing the same tool call multiple times as it streams
\n*   Thread management maintains conversation context across messages
\n*   Graceful error handling for connection issues
\n
\n
To use the client:

\n# Optional: specify custom server URL\nexport AGUI_SERVER_URL=\"http://localhost:8000/chat\"\n\n# Start the interactive chat\npython client.py\n
**Example Session:**

\n Connecting to: http://localhost:8000/chat\n\nChat started! Type 'exit' or 'quit' to end the session.\n\nYou: What's the status of order ORD-001?\nAgent: \n[Calling tool: get_order_status]\n[Tool result: {\"status\": \"shipped\", \"tracking\": \"1Z999AA1\", \"eta\": \"Jan 25, 2026\"}]\nYour order ORD-001 has been shipped! \n\n- Tracking Number: 1Z999AA1\n- Estimated Delivery Date: January 25, 2026\n\nYou can use the tracking number to monitor the delivery progress.\n\nYou: Can you check ORD-002?\nAgent: \n[Calling tool: get_order_status]\n[Tool result: {\"status\": \"processing\", \"tracking\": null, \"eta\": \"Jan 23, 2026\"}]\nYour order ORD-002 is currently being processed.\n\n- Status: Processing\n- Estimated Delivery: January 23, 2026\n\nYour order should ship soon, and you'll receive a tracking number once it's on the way.\n\nYou: exit\n\nGoodbye!\n
The client we just built handles events at a high level, abstracting away the details. But what's actually flowing through that SSE connection? Let's peek under the hood.

\n
## Event Types You'll See

\n
As the server streams back responses, clients receive a series of structured events. If you were to observe the raw SSE stream (e.g., using curl), you'd see events like:

\n curl -N http://localhost:8000/chat \\\n -H \"Content-Type: application/json\" \\\n -H \"Accept: text/event-stream\" \\\n -d '{\"messages\": [{\"role\": \"user\", \"content\": \"What'\\''s the status of order ORD-001?\"}]}'\n
**Sample event stream (with tool calling):**

\n data: {\"type\":\"RUN_STARTED\",\"threadId\":\"eb4d9850-14ef-446c-af4b-23037acda9e8\",\"runId\":\"chatcmpl-xyz\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_START\",\"messageId\":\"e8648880-a9ff-4178-a17d-4a6d3ec3d39c\",\"role\":\"assistant\"}\n\ndata: {\"type\":\"TOOL_CALL_START\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"toolCallName\":\"get_order_status\",\"parentMessageId\":\"e8648880-a9ff-4178-a17d-4a6d3ec3d39c\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"{\\\"\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"order\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"_id\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"\\\":\\\"\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"ORD\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"-\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"001\"}\n\ndata: {\"type\":\"TOOL_CALL_ARGS\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"delta\":\"\\\"}\"}\n\ndata: {\"type\":\"TOOL_CALL_END\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\"}\n\ndata: {\"type\":\"TOOL_CALL_RESULT\",\"messageId\":\"f048cb0a-a049-4a51-9403-a05e4820438a\",\"toolCallId\":\"call_GTWj2N3ZyYiiQIjg3fwmiQ8y\",\"content\":\"{\\\"status\\\": \\\"shipped\\\", \\\"tracking\\\": \\\"1Z999AA1\\\", \\\"eta\\\": \\\"Jan 25, 2026\\\"}\",\"role\":\"tool\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_START\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"role\":\"assistant\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"Your\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" order\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" ORD\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"-\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"001\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" has\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" been\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\" shipped\"}\n\ndata: {\"type\":\"TEXT_MESSAGE_CONTENT\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\",\"delta\":\"!\"}\n\n... (additional TEXT_MESSAGE_CONTENT events streaming the response) ...\n\ndata: {\"type\":\"TEXT_MESSAGE_END\",\"messageId\":\"8215fc88-8cb6-4ce4-8bdb-a8715dcd26cf\"}\n\ndata: {\"type\":\"RUN_FINISHED\",\"threadId\":\"eb4d9850-14ef-446c-af4b-23037acda9e8\",\"runId\":\"chatcmpl-xyz\"}\n
**Understanding the flow:**

\n
\n 1.   RUN_STARTED- Agent begins processing the request
\n 2.   TEXT_MESSAGE_START- First message starts (will contain tool calls)
\n 3.   TOOL_CALL_START- Agent invokes the get_order_status tool
\n 4.   Multiple TOOL_CALL_ARGS events - Arguments stream incrementally as JSON chunks ({\"order_id\":\"ORD-001\"})
\n 5.   TOOL_CALL_END- Tool invocation structure complete
\n 6.   TOOL_CALL_RESULT- Tool execution finished with result data
\n 7.   TEXT_MESSAGE_START- Second message starts (the final response)
\n 8.   Multiple TEXT_MESSAGE_CONTENT events - Response text streams word-by-word
\n 9.   TEXT_MESSAGE_END- Response message complete
\n 10.   RUN_FINISHED- Entire run completed successfully
\n
\n
This granular event model enables rich UI experiences - showing tool execution indicators (\"Searching...\", \"Calculating...\"), displaying intermediate results, and providing complete transparency into the agent's reasoning process.

\n
Seeing the raw events helps, but truly working with AG-UI requires a shift in how you think about agent interactions. Let's explore this conceptual change.

\n
## The Mental Model Shift

\n
### Traditional API Thinking

\n# Imperative: Call and wait\nresponse = agent.run(\"What's 2+2?\")\nprint(response) # \"The answer is 4\"\n
**Mental model:**Function call with return value

\n
### AG-UI Thinking

\n# Reactive: Subscribe to events\nasync for event in agent.run_stream(\"What's 2+2?\"):\n match event.type:\n case \"RUN_STARTED\":\n show_loading()\n case \"TEXT_MESSAGE_CONTENT\":\n display_chunk(event.delta)\n case \"RUN_FINISHED\":\n hide_loading()\n
**Mental model:**Observable stream of events

\n
This shift feels similar to:

\n
\n*   Moving from synchronous to async code
\n*   Moving from REST to event-driven architecture
\n*   Moving from polling to pub/sub
\n
\n
This mental shift isn't just philosophical - it unlocks concrete benefits that weren't possible with request/response patterns.

\n
### What You Gain

\n
#### Observability

\n# You can SEE what the agent is doing\nTOOL_CALL_START: \"get_order_status\"\nTOOL_CALL_ARGS: {\"order_id\": \"ORD-001\"}\nTOOL_CALL_RESULT: {\"status\": \"shipped\", \"tracking\": \"1Z999AA1\", \"eta\": \"Jan 25, 2026\"}\nTEXT_MESSAGE_START: \"Your order ORD-001 has been shipped...\"\n
#### Interruptibility

\n# Future: Cancel long-running operations\nasync for event in agent.run_stream(query):\n if user_clicked_cancel:\n await agent.cancel(thread_id, run_id)\n break\n
#### Transparency

\n# Users see the reasoning process\n\"Looking up order ORD-001...\"\n\"Order found: Status is 'shipped'\"\n\"Retrieving tracking information...\"\n\"Your order has been shipped with tracking number 1Z999AA1...\"\n
To put these benefits in context, here's how AG-UI compares to traditional approaches across key dimensions:

\n
### AG-UI vs. Traditional Approaches

\n

Aspect Traditional REST Custom Streaming AG-UI Connection Model Request/Response Varies Server-Sent Events State Management Manual Manual Protocol-managed Tool Calling Invisible Custom format Standardized events Framework Varies Framework-locked Framework-agnostic Browser Support Universal Varies Universal Implementation Simple Complex Moderate Ecosystem N/A Isolated Growing

\n
You've now seen AG-UI's design principles, implementation details, and conceptual foundations. But the most important question remains: should you actually use it?

\n
## Conclusion: Is AG-UI Right for Your Project?

\n
AG-UI represents a shift toward standardized, observable agent interactions. Before adopting it, understand where the protocol stands and whether it fits your needs.

\n
### Protocol Maturity

\n
The protocol is stable enough for production use but still evolving:

\n
**Ready now:**Core specification stable, Microsoft Agent Framework integration available, FastAPI/Python implementation mature, basic streaming and threading work reliably.

\n
### Choose AG-UI If You

\n
\n*   **Building new agent projects**- No legacy API to maintain, want future compatibility with emerging ecosystem
\n*   **Need streaming observability**- Multi-step workflows where users benefit from seeing each stage of execution
\n*   **Want framework flexibility**- Same client code works with any AG-UI-compliant backend
\n*   **Comfortable with evolving standards**- Can adapt to protocol changes as it matures
\n
\n
### Stick with Alternatives If You

\n
\n*   **Have working solutions**- Custom streaming working well, migration cost not justified
\n*   **Need guaranteed stability**- Mission-critical systems where breaking changes are unacceptable
\n*   **Build simple agents**- Single-step request/response without tool calling or streaming needs
\n*   **Risk-averse environment**- Large existing implementations where proven approaches are required
\n
\n
Beyond individual project decisions, it's worth considering AG-UI's role in the broader ecosystem.

\n
### The Bigger Picture

\n
While this blog post focused on Microsoft Agent Framework, AG-UI's true power lies in its broader mission: creating a common language for agent-UI communication across the entire ecosystem. As more frameworks adopt it, the real value emerges:**write your UI once, work with any compliant agent framework**.

\n
Think of it like GraphQL for APIs or OpenAPI for REST - a standardization layer that benefits the entire ecosystem.

\n
**The protocol is young, but the problem it solves is real.**Whether you adopt it now or wait for broader adoption, understanding AG-UI helps you make informed architectural decisions for your agent applications.

\n
Ready to dive deeper? Here are the official resources to continue your AG-UI journey.

\n
## Resources

\n
### AG-UI & Microsoft Agent Framework

\n
\n*   [Getting Started with AG-UI (Microsoft Learn)](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/getting-started?pivots=programming-language-python\%22)- Official tutorial
\n*   [AG-UI Integration Overview](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/?pivots=programming-language-python\%22)- Architecture and concepts
\n*   [AG-UI Protocol Specification](https://techcommunity.microsoft.com/%22https://docs.ag-ui.com/introduction/%22)- Official protocol documentation
\n*   [Backend Tool Rendering](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/backend-tool-rendering?pivots=programming-language-python\%22)- Adding function tools
\n*   [Security Considerations](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/integrations/ag-ui/security-considerations/%22)- Production security guidance
\n*   [Microsoft Agent Framework Documentation](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/en-us/agent-framework/overview/agent-framework-overview/%22)- Framework overview
\n*   [AG-UI Dojo Examples](https://techcommunity.microsoft.com/%22https://dojo.ag-ui.com/microsoft-agent-framework-dotnet/%22)- Live demonstrations
\n
\n
### UI Components & Integration

\n
\n*   [CopilotKit for Microsoft Agent Framework](https://techcommunity.microsoft.com/%22https://docs.copilotkit.ai/microsoft-agent-framework/%22)- React component library
\n
\n
### Community & Support

\n
\n*   [Microsoft Q&A](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/answers//%22)- Community support
\n*   [Agent Framework GitHub](https://techcommunity.microsoft.com/%22https://github.com/microsoft/agent-framework/%22)- Source code and issues
\n
\n
### Related Technologies

\n
\n*   [Azure AI Foundry Documentation](https://techcommunity.microsoft.com/%22https://learn.microsoft.com/azure/ai-foundry//%22)- Azure AI platform
\n*   [FastAPI Documentation](https://techcommunity.microsoft.com/%22https://fastapi.tiangolo.com//%22)- Web framework
\n*   [Server-Sent Events (SSE) Specification](https://techcommunity.microsoft.com/%22https://html.spec.whatwg.org/multipage/server-sent-events.html/%22)- Protocol standard
\n
\n
_This blog post introduces AG-UI with Microsoft Agent Framework, focusing on fundamental concepts and building your first interactive agent application._

","kudosSumWeight":2,"postTime":"2026-01-29T00:00:00.030-08:00","images":{"__typename":"AssociatedImageConnection","edges":[{"__typename":"AssociatedImageEdge","cursor":"MjYuMXwyLjF8b3wyNXxfTlZffDE","node":{"__ref":"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LVYxckRmVQ?revision=3\"}"}},{"__typename":"AssociatedImageEdge","cursor":"MjYuMXwyLjF8b3wyNXxfTlZffDI","node":{"__ref":"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWQ0WU5kZA?revision=3\"}"}},{"__typename":"AssociatedImageEdge","cursor":"MjYuMXwyLjF8b3wyNXxfTlZffDM","node":{"__ref":"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWpEdm5abA?revision=3\"}"}}],"totalCount":3,"pageInfo":{"__typename":"PageInfo","hasNextPage":false,"endCursor":null,"hasPreviousPage":false,"startCursor":null}},"attachments":{"__typename":"AttachmentConnection","pageInfo":{"__typename":"PageInfo","hasNextPage":false,"endCursor":null,"hasPreviousPage":false,"startCursor":null},"edges":[]},"tags":{"__typename":"TagConnection","pageInfo":{"__typename":"PageInfo","hasNextPage":true,"endCursor":"MjYuMXwyLjF8b3wxMHxfTlZffDEw","hasPreviousPage":false,"startCursor":null},"edges":[{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDE","node":{"__typename":"Tag","id":"tag:agents","text":"agents","time":"2017-10-12T10:08:20.499-07:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDI","node":{"__typename":"Tag","id":"tag:ai","text":"ai","time":"2018-01-27T01:25:04.985-08:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDM","node":{"__typename":"Tag","id":"tag:ai foundry","text":"ai foundry","time":"2024-12-05T05:29:02.794-08:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDQ","node":{"__typename":"Tag","id":"tag:azure","text":"azure","time":"2016-09-06T09:34:09.130-07:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDU","node":{"__typename":"Tag","id":"tag:azure ai foundry","text":"azure ai foundry","time":"2024-11-14T15:44:19.352-08:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDY","node":{"__typename":"Tag","id":"tag:best practices","text":"best practices","time":"2016-08-17T10:05:07.769-07:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDc","node":{"__typename":"Tag","id":"tag:developer","text":"developer","time":"2016-07-01T11:00:22.155-07:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDg","node":{"__typename":"Tag","id":"tag:get started","text":"get started","time":"2021-03-25T09:09:57.638-07:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDk","node":{"__typename":"Tag","id":"tag:learning","text":"learning","time":"2019-03-21T04:21:17.532-07:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}},{"__typename":"TagEdge","cursor":"MjYuMXwyLjF8b3wxMHxfTlZffDEw","node":{"__typename":"Tag","id":"tag:llm","text":"llm","time":"2023-03-15T00:00:00.039-07:00","lastActivityTime":null,"messagesCount":null,"followersCount":null}}]},"timeToRead":18,"rawTeaser":"","introduction":"","coverImage":{"__typename":"UploadedImage","url":"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LVYxckRmVQ?revision=3","width":1792,"height":1015},"coverImageProperties":{"__typename":"CoverImageProperties","style":"STANDARD","titlePosition":"BOTTOM","altText":""},"currentRevision":{"__ref":"Revision:revision:4488249_3"},"latestVersion":{"__typename":"FriendlyVersion","major":"1","minor":"0"},"metrics":{"__typename":"MessageMetrics","views":1860},"read":false,"visibilityScope":"PUBLIC","canonicalUrl":null,"seoTitle":null,"seoDescription":null,"placeholder":false,"originalMessageForPlaceholder":null,"contributors":{"__typename":"UserConnection","edges":[]},"nonCoAuthorContributors":{"__typename":"UserConnection","edges":[]},"coAuthors":{"__typename":"UserConnection","edges":[]},"blogMessagePolicies":{"__typename":"BlogMessagePolicies","canDoAuthoringActionsOnBlog":{"__typename":"PolicyResult","failureReason":{"__typename":"FailureReason","message":"error.lithium.policies.blog.action_can_do_authoring_action.accessDenied","key":"error.lithium.policies.blog.action_can_do_authoring_action.accessDenied","args":[]}}},"archivalData":null,"customFields":[{"__typename":"CustomStringField","name":"blogScoreGradeResponse","stringValue":"{}"}],"revisions({\"constraints\":{\"isPublished\":{\"eq\":true}}})":{"__typename":"RevisionConnection","totalCount":3}},"Conversation:conversation:4488249":{"__typename":"Conversation","id":"conversation:4488249","solved":false,"topic":{"__ref":"BlogTopicMessage:message:4488249"},"lastPostingActivityTime":"2026-02-12T23:26:32.727-08:00","lastPostTime":"2026-02-12T23:26:32.727-08:00","unreadReplyCount":8,"isSubscribed":false},"ModerationData:moderation_data:4488249":{"__typename":"ModerationData","id":"moderation_data:4488249","status":"APPROVED","rejectReason":null,"isReportedAbuse":false,"rejectUser":null,"rejectTime":null,"rejectActorType":null},"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LVYxckRmVQ?revision=3\"}":{"__typename":"AssociatedImage","url":"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LVYxckRmVQ?revision=3","title":"Screenshot 2026-01-21 232607.png","associationType":"COVER","width":1792,"height":1015,"altText":""},"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWQ0WU5kZA?revision=3\"}":{"__typename":"AssociatedImage","url":"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWQ0WU5kZA?revision=3","title":"image.png","associationType":"BODY","width":277,"height":765,"altText":""},"AssociatedImage:{\"url\":\"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWpEdm5abA?revision=3\"}":{"__typename":"AssociatedImage","url":"https://techcommunity.microsoft.com/t5/s/gxcuf89792/images/bS00NDg4MjQ5LWpEdm5abA?revision=3","title":"image.png","associationType":"BODY","width":399,"height":1054,"altText":""},"Revision:revision:4488249_3":{"__typename":"Revision","id":"revision:4488249_3","lastEditTime":"2026-01-21T10:06:08.057-08:00"},"CachedAsset:theme:customTheme1-1776077659306":{"__typename":"CachedAsset","id":"theme:customTheme1-1776077659306","value":{"id":"customTheme1","animation":{"fast":"150ms","normal":"250ms","slow":"500ms","slowest":"750ms","function":"cubic-bezier(0.07, 0.91, 0.51, 1)","__typename":"AnimationThemeSettings"},"avatar":{"borderRadius":"50%","collections":["default"],"__typename":"AvatarThemeSettings"},"basics":{"browserIcon":{"imageAssetName":"favicon-1730836283320.png","imageLastModified":"1730836286415","__typename":"ThemeAsset"},"customerLogo":{"imageAssetName":"favicon-1730836271365.png","imageLastModified":"1730836274203","__typename":"ThemeAsset"},"maximumWidthOfPageContent":"1300px","oneColumnNarrowWidth":"1200px","gridGutterWidthMd":"30px","gridGutterWidthXs":"10px","pageWidthStyle":"WIDTH_OF_BROWSER","__typename":"BasicsThemeSettings"},"buttons":{"borderRadiusSm":"3px","borderRadius":"3px","borderRadiusLg":"5px","paddingY":"5px","paddingYLg":"7px","paddingYHero":"var(--lia-bs-btn-padding-y-lg)","paddingX":"12px","paddingXLg":"16px","paddingXHero":"60px","fontStyle":"NORMAL","fontWeight":"700","textTransform":"NONE","disabledOpacity":0.5,"primaryTextColor":"var(--lia-bs-white)","primaryTextHoverColor":"var(--lia-bs-white)","primaryTextActiveColor":"var(--lia-bs-white)","primaryBgColor":"var(--lia-bs-primary)","primaryBgHoverColor":"hsl(var(--lia-bs-primary-h), var(--lia-bs-primary-s), calc(var(--lia-bs-primary-l) * 0.85))","primaryBgActiveColor":"hsl(var(--lia-bs-primary-h), var(--lia-bs-primary-s), calc(var(--lia-bs-primary-l) * 0.7))","primaryBorder":"1px solid transparent","primaryBorderHover":"1px solid transparent","primaryBorderActive":"1px solid transparent","primaryBorderFocus":"1px solid var(--lia-bs-white)","primaryBoxShadowFocus":"0 0 0 1px var(--lia-bs-primary), 0 0 0 4px hsla(var(--lia-bs-primary-h), var(--lia-bs-primary-s), var(--lia-bs-primary-l), 0.2)","secondaryTextColor":"var(--lia-bs-gray-900)","secondaryTextHoverColor":"hsl(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), calc(var(--lia-bs-gray-900-l) * 0.95))","secondaryTextActiveColor":"hsl(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), calc(var(--lia-bs-gray-900-l) * 0.9))","secondaryBgColor":"var(--lia-bs-gray-200)","secondaryBgHoverColor":"hsl(var(--lia-bs-gray-200-h), var(--lia-bs-gray-200-s), calc(var(--lia-bs-gray-200-l) * 0.96))","secondaryBgActiveColor":"hsl(var(--lia-bs-gray-200-h), var(--lia-bs-gray-200-s), calc(var(--lia-bs-gray-200-l) * 0.92))","secondaryBorder":"1px solid transparent","secondaryBorderHover":"1px solid transparent","secondaryBorderActive":"1px solid transparent","secondaryBorderFocus":"1px solid transparent","secondaryBoxShadowFocus":"0 0 0 1px var(--lia-bs-primary), 0 0 0 4px hsla(var(--lia-bs-primary-h), var(--lia-bs-primary-s), var(--lia-bs-primary-l), 0.2)","tertiaryTextColor":"var(--lia-bs-gray-900)","tertiaryTextHoverColor":"hsl(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), calc(var(--lia-bs-gray-900-l) * 0.95))","tertiaryTextActiveColor":"hsl(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), calc(var(--lia-bs-gray-900-l) * 0.9))","tertiaryBgColor":"transparent","tertiaryBgHoverColor":"transparent","tertiaryBgActiveColor":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.04)","tertiaryBorder":"1px solid transparent","tertiaryBorderHover":"1px solid hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.08)","tertiaryBorderActive":"1px solid transparent","tertiaryBorderFocus":"1px solid transparent","tertiaryBoxShadowFocus":"0 0 0 1px var(--lia-bs-primary), 0 0 0 4px hsla(var(--lia-bs-primary-h), var(--lia-bs-primary-s), var(--lia-bs-primary-l), 0.2)","destructiveTextColor":"var(--lia-bs-danger)","destructiveTextHoverColor":"hsl(var(--lia-bs-danger-h), var(--lia-bs-danger-s), calc(var(--lia-bs-danger-l) * 0.95))","destructiveTextActiveColor":"hsl(var(--lia-bs-danger-h), var(--lia-bs-danger-s), calc(var(--lia-bs-danger-l) * 0.9))","destructiveBgColor":"var(--lia-bs-gray-200)","destructiveBgHoverColor":"hsl(var(--lia-bs-gray-200-h), var(--lia-bs-gray-200-s), calc(var(--lia-bs-gray-200-l) * 0.96))","destructiveBgActiveColor":"hsl(var(--lia-bs-gray-200-h), var(--lia-bs-gray-200-s), calc(var(--lia-bs-gray-200-l) * 0.92))","destructiveBorder":"1px solid transparent","destructiveBorderHover":"1px solid transparent","destructiveBorderActive":"1px solid transparent","destructiveBorderFocus":"1px solid transparent","destructiveBoxShadowFocus":"0 0 0 1px var(--lia-bs-primary), 0 0 0 4px hsla(var(--lia-bs-primary-h), var(--lia-bs-primary-s), var(--lia-bs-primary-l), 0.2)","__typename":"ButtonsThemeSettings"},"border":{"color":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.08)","mainContent":"NONE","sideContent":"LIGHT","radiusSm":"3px","radius":"5px","radiusLg":"9px","radius50":"100vw","__typename":"BorderThemeSettings"},"boxShadow":{"xs":"0 0 0 1px hsla(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), var(--lia-bs-gray-900-l), 0.08), 0 3px 0 -1px hsla(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), var(--lia-bs-gray-900-l), 0.16)","sm":"0 2px 4px hsla(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), var(--lia-bs-gray-900-l), 0.12)","md":"0 5px 15px hsla(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), var(--lia-bs-gray-900-l), 0.3)","lg":"0 10px 30px hsla(var(--lia-bs-gray-900-h), var(--lia-bs-gray-900-s), var(--lia-bs-gray-900-l), 0.3)","__typename":"BoxShadowThemeSettings"},"cards":{"bgColor":"var(--lia-panel-bg-color)","borderRadius":"var(--lia-panel-border-radius)","boxShadow":"var(--lia-box-shadow-xs)","__typename":"CardsThemeSettings"},"chip":{"maxWidth":"300px","height":"30px","__typename":"ChipThemeSettings"},"coreTypes":{"defaultMessageLinkColor":"var(--lia-bs-link-color)","defaultMessageLinkDecoration":"none","defaultMessageLinkFontStyle":"NORMAL","defaultMessageLinkFontWeight":"400","defaultMessageFontStyle":"NORMAL","defaultMessageFontWeight":"400","defaultMessageFontFamily":"var(--lia-bs-font-family-base)","forumColor":"#4099E2","forumFontFamily":"var(--lia-bs-font-family-base)","forumFontWeight":"var(--lia-default-message-font-weight)","forumLineHeight":"var(--lia-bs-line-height-base)","forumFontStyle":"var(--lia-default-message-font-style)","forumMessageLinkColor":"var(--lia-default-message-link-color)","forumMessageLinkDecoration":"var(--lia-default-message-link-decoration)","forumMessageLinkFontStyle":"var(--lia-default-message-link-font-style)","forumMessageLinkFontWeight":"var(--lia-default-message-link-font-weight)","forumSolvedColor":"#148563","blogColor":"#1CBAA0","blogFontFamily":"var(--lia-bs-font-family-base)","blogFontWeight":"var(--lia-default-message-font-weight)","blogLineHeight":"1.75","blogFontStyle":"var(--lia-default-message-font-style)","blogMessageLinkColor":"var(--lia-default-message-link-color)","blogMessageLinkDecoration":"var(--lia-default-message-link-decoration)","blogMessageLinkFontStyle":"var(--lia-default-message-link-font-style)","blogMessageLinkFontWeight":"var(--lia-default-message-link-font-weight)","tkbColor":"#4C6B90","tkbFontFamily":"var(--lia-bs-font-family-base)","tkbFontWeight":"var(--lia-default-message-font-weight)","tkbLineHeight":"1.75","tkbFontStyle":"var(--lia-default-message-font-style)","tkbMessageLinkColor":"var(--lia-default-message-link-color)","tkbMessageLinkDecoration":"var(--lia-default-message-link-decoration)","tkbMessageLinkFontStyle":"var(--lia-default-message-link-font-style)","tkbMessageLinkFontWeight":"var(--lia-default-message-link-font-weight)","qandaColor":"#4099E2","qandaFontFamily":"var(--lia-bs-font-family-base)","qandaFontWeight":"var(--lia-default-message-font-weight)","qandaLineHeight":"var(--lia-bs-line-height-base)","qandaFontStyle":"var(--lia-default-message-link-font-style)","qandaMessageLinkColor":"var(--lia-default-message-link-color)","qandaMessageLinkDecoration":"var(--lia-default-message-link-decoration)","qandaMessageLinkFontStyle":"var(--lia-default-message-link-font-style)","qandaMessageLinkFontWeight":"var(--lia-default-message-link-font-weight)","qandaSolvedColor":"#3FA023","ideaColor":"#FF8000","ideaFontFamily":"var(--lia-bs-font-family-base)","ideaFontWeight":"var(--lia-default-message-font-weight)","ideaLineHeight":"var(--lia-bs-line-height-base)","ideaFontStyle":"var(--lia-default-message-font-style)","ideaMessageLinkColor":"var(--lia-default-message-link-color)","ideaMessageLinkDecoration":"var(--lia-default-message-link-decoration)","ideaMessageLinkFontStyle":"var(--lia-default-message-link-font-style)","ideaMessageLinkFontWeight":"var(--lia-default-message-link-font-weight)","contestColor":"#FCC845","contestFontFamily":"var(--lia-bs-font-family-base)","contestFontWeight":"var(--lia-default-message-font-weight)","contestLineHeight":"var(--lia-bs-line-height-base)","contestFontStyle":"var(--lia-default-message-link-font-style)","contestMessageLinkColor":"var(--lia-default-message-link-color)","contestMessageLinkDecoration":"var(--lia-default-message-link-decoration)","contestMessageLinkFontStyle":"ITALIC","contestMessageLinkFontWeight":"var(--lia-default-message-link-font-weight)","occasionColor":"#bc341b","occasionFontFamily":"var(--lia-bs-font-family-base)","occasionFontWeight":"var(--lia-default-message-font-weight)","occasionLineHeight":"var(--lia-bs-line-height-base)","occasionFontStyle":"var(--lia-default-message-font-style)","occasionMessageLinkColor":"var(--lia-default-message-link-color)","occasionMessageLinkDecoration":"var(--lia-default-message-link-decoration)","occasionMessageLinkFontStyle":"var(--lia-default-message-link-font-style)","occasionMessageLinkFontWeight":"var(--lia-default-message-link-font-weight)","grouphubColor":"#333333","categoryColor":"#949494","communityColor":"#FFFFFF","productColor":"#949494","__typename":"CoreTypesThemeSettings"},"colors":{"black":"#000000","white":"#FFFFFF","gray100":"#F7F7F7","gray200":"#F7F7F7","gray300":"#E8E8E8","gray400":"#D9D9D9","gray500":"#CCCCCC","gray600":"#717171","gray700":"#707070","gray800":"#545454","gray900":"#333333","dark":"#545454","light":"#F7F7F7","primary":"#0069D4","secondary":"#333333","bodyText":"#1E1E1E","bodyBg":"#FFFFFF","info":"#409AE2","success":"#41C5AE","warning":"#FCC844","danger":"#BC341B","alertSystem":"#FF6600","textMuted":"#707070","highlight":"#FFFCAD","outline":"var(--lia-bs-primary)","custom":["#D3F5A4","#243A5E"],"__typename":"ColorsThemeSettings"},"divider":{"size":"3px","marginLeft":"4px","marginRight":"4px","borderRadius":"50%","bgColor":"var(--lia-bs-gray-600)","bgColorActive":"var(--lia-bs-gray-600)","__typename":"DividerThemeSettings"},"dropdown":{"fontSize":"var(--lia-bs-font-size-sm)","borderColor":"var(--lia-bs-border-color)","borderRadius":"var(--lia-bs-border-radius-sm)","dividerBg":"var(--lia-bs-gray-300)","itemPaddingY":"5px","itemPaddingX":"20px","headerColor":"var(--lia-bs-gray-700)","__typename":"DropdownThemeSettings"},"email":{"link":{"color":"#0069D4","hoverColor":"#0061c2","decoration":"none","hoverDecoration":"underline","__typename":"EmailLinkSettings"},"border":{"color":"#e4e4e4","__typename":"EmailBorderSettings"},"buttons":{"borderRadiusLg":"5px","paddingXLg":"16px","paddingYLg":"7px","fontWeight":"700","primaryTextColor":"#ffffff","primaryTextHoverColor":"#ffffff","primaryBgColor":"#0069D4","primaryBgHoverColor":"#005cb8","primaryBorder":"1px solid transparent","primaryBorderHover":"1px solid transparent","__typename":"EmailButtonsSettings"},"panel":{"borderRadius":"5px","borderColor":"#e4e4e4","__typename":"EmailPanelSettings"},"__typename":"EmailThemeSettings"},"emoji":{"skinToneDefault":"#ffcd43","skinToneLight":"#fae3c5","skinToneMediumLight":"#e2cfa5","skinToneMedium":"#daa478","skinToneMediumDark":"#a78058","skinToneDark":"#5e4d43","__typename":"EmojiThemeSettings"},"heading":{"color":"var(--lia-bs-body-color)","fontFamily":"Segoe UI","fontStyle":"NORMAL","fontWeight":"400","h1FontSize":"34px","h2FontSize":"32px","h3FontSize":"28px","h4FontSize":"24px","h5FontSize":"20px","h6FontSize":"16px","lineHeight":"1.3","subHeaderFontSize":"11px","subHeaderFontWeight":"500","h1LetterSpacing":"normal","h2LetterSpacing":"normal","h3LetterSpacing":"normal","h4LetterSpacing":"normal","h5LetterSpacing":"normal","h6LetterSpacing":"normal","subHeaderLetterSpacing":"2px","h1FontWeight":"var(--lia-bs-headings-font-weight)","h2FontWeight":"var(--lia-bs-headings-font-weight)","h3FontWeight":"var(--lia-bs-headings-font-weight)","h4FontWeight":"var(--lia-bs-headings-font-weight)","h5FontWeight":"var(--lia-bs-headings-font-weight)","h6FontWeight":"var(--lia-bs-headings-font-weight)","__typename":"HeadingThemeSettings"},"icons":{"size10":"10px","size12":"12px","size14":"14px","size16":"16px","size20":"20px","size24":"24px","size30":"30px","size40":"40px","size50":"50px","size60":"60px","size80":"80px","size120":"120px","size160":"160px","__typename":"IconsThemeSettings"},"imagePreview":{"bgColor":"var(--lia-bs-gray-900)","titleColor":"var(--lia-bs-white)","controlColor":"var(--lia-bs-white)","controlBgColor":"var(--lia-bs-gray-800)","__typename":"ImagePreviewThemeSettings"},"input":{"borderColor":"var(--lia-bs-gray-600)","disabledColor":"var(--lia-bs-gray-600)","focusBorderColor":"var(--lia-bs-primary)","labelMarginBottom":"10px","btnFontSize":"var(--lia-bs-font-size-sm)","focusBoxShadow":"0 0 0 3px hsla(var(--lia-bs-primary-h), var(--lia-bs-primary-s), var(--lia-bs-primary-l), 0.2)","checkLabelMarginBottom":"2px","checkboxBorderRadius":"3px","borderRadiusSm":"var(--lia-bs-border-radius-sm)","borderRadius":"var(--lia-bs-border-radius)","borderRadiusLg":"var(--lia-bs-border-radius-lg)","formTextMarginTop":"4px","textAreaBorderRadius":"var(--lia-bs-border-radius)","activeFillColor":"var(--lia-bs-primary)","__typename":"InputThemeSettings"},"loading":{"dotDarkColor":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.2)","dotLightColor":"hsla(var(--lia-bs-white-h), var(--lia-bs-white-s), var(--lia-bs-white-l), 0.5)","barDarkColor":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.06)","barLightColor":"hsla(var(--lia-bs-white-h), var(--lia-bs-white-s), var(--lia-bs-white-l), 0.4)","__typename":"LoadingThemeSettings"},"link":{"color":"var(--lia-bs-primary)","hoverColor":"hsl(var(--lia-bs-primary-h), var(--lia-bs-primary-s), calc(var(--lia-bs-primary-l) - 10%))","decoration":"none","hoverDecoration":"underline","__typename":"LinkThemeSettings"},"listGroup":{"itemPaddingY":"15px","itemPaddingX":"15px","borderColor":"var(--lia-bs-gray-300)","__typename":"ListGroupThemeSettings"},"modal":{"contentTextColor":"var(--lia-bs-body-color)","contentBg":"var(--lia-bs-white)","backgroundBg":"var(--lia-bs-black)","smSize":"440px","mdSize":"760px","lgSize":"1080px","backdropOpacity":0.3,"contentBoxShadowXs":"var(--lia-bs-box-shadow-sm)","contentBoxShadow":"var(--lia-bs-box-shadow)","headerFontWeight":"700","__typename":"ModalThemeSettings"},"navbar":{"position":"FIXED","background":{"attachment":null,"clip":null,"color":"var(--lia-bs-white)","imageAssetName":"","imageLastModified":"0","origin":null,"position":"CENTER_CENTER","repeat":"NO_REPEAT","size":"COVER","__typename":"BackgroundProps"},"backgroundOpacity":0.8,"paddingTop":"15px","paddingBottom":"15px","borderBottom":"1px solid var(--lia-bs-border-color)","boxShadow":"var(--lia-bs-box-shadow-sm)","brandMarginRight":"30px","brandMarginRightSm":"10px","brandLogoHeight":"30px","linkGap":"10px","linkJustifyContent":"flex-start","linkPaddingY":"5px","linkPaddingX":"10px","linkDropdownPaddingY":"9px","linkDropdownPaddingX":"var(--lia-nav-link-px)","linkColor":"var(--lia-bs-body-color)","linkHoverColor":"var(--lia-bs-primary)","linkFontSize":"var(--lia-bs-font-size-sm)","linkFontStyle":"NORMAL","linkFontWeight":"400","linkTextTransform":"NONE","linkLetterSpacing":"normal","linkBorderRadius":"var(--lia-bs-border-radius-sm)","linkBgColor":"transparent","linkBgHoverColor":"transparent","linkBorder":"none","linkBorderHover":"none","linkBoxShadow":"none","linkBoxShadowHover":"none","linkTextBorderBottom":"none","linkTextBorderBottomHover":"none","dropdownPaddingTop":"10px","dropdownPaddingBottom":"15px","dropdownPaddingX":"10px","dropdownMenuOffset":"2px","dropdownDividerMarginTop":"10px","dropdownDividerMarginBottom":"10px","dropdownBorderColor":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.08)","controllerBgHoverColor":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.1)","controllerIconColor":"var(--lia-bs-body-color)","controllerIconHoverColor":"var(--lia-bs-body-color)","controllerTextColor":"var(--lia-nav-controller-icon-color)","controllerTextHoverColor":"var(--lia-nav-controller-icon-hover-color)","controllerHighlightColor":"hsla(30, 100%, 50%)","controllerHighlightTextColor":"var(--lia-yiq-light)","controllerBorderRadius":"var(--lia-border-radius-50)","hamburgerColor":"var(--lia-nav-controller-icon-color)","hamburgerHoverColor":"var(--lia-nav-controller-icon-color)","hamburgerBgColor":"transparent","hamburgerBgHoverColor":"transparent","hamburgerBorder":"none","hamburgerBorderHover":"none","collapseMenuMarginLeft":"20px","collapseMenuDividerBg":"var(--lia-nav-link-color)","collapseMenuDividerOpacity":0.16,"__typename":"NavbarThemeSettings"},"pager":{"textColor":"var(--lia-bs-link-color)","textFontWeight":"var(--lia-font-weight-md)","textFontSize":"var(--lia-bs-font-size-sm)","__typename":"PagerThemeSettings"},"panel":{"bgColor":"var(--lia-bs-white)","borderRadius":"var(--lia-bs-border-radius)","borderColor":"var(--lia-bs-border-color)","boxShadow":"none","__typename":"PanelThemeSettings"},"popover":{"arrowHeight":"8px","arrowWidth":"16px","maxWidth":"300px","minWidth":"100px","headerBg":"var(--lia-bs-white)","borderColor":"var(--lia-bs-border-color)","borderRadius":"var(--lia-bs-border-radius)","boxShadow":"0 0.5rem 1rem hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.15)","__typename":"PopoverThemeSettings"},"prism":{"color":"#000000","bgColor":"#f5f2f0","fontFamily":"var(--font-family-monospace)","fontSize":"var(--lia-bs-font-size-base)","fontWeightBold":"var(--lia-bs-font-weight-bold)","fontStyleItalic":"italic","tabSize":2,"highlightColor":"#b3d4fc","commentColor":"#62707e","punctuationColor":"#6f6f6f","namespaceOpacity":"0.7","propColor":"#990055","selectorColor":"#517a00","operatorColor":"#906736","operatorBgColor":"hsla(0, 0%, 100%, 0.5)","keywordColor":"#0076a9","functionColor":"#d3284b","variableColor":"#c14700","__typename":"PrismThemeSettings"},"rte":{"bgColor":"var(--lia-bs-white)","borderRadius":"var(--lia-panel-border-radius)","boxShadow":" var(--lia-panel-box-shadow)","customColor1":"#bfedd2","customColor2":"#fbeeb8","customColor3":"#f8cac6","customColor4":"#eccafa","customColor5":"#c2e0f4","customColor6":"#2dc26b","customColor7":"#f1c40f","customColor8":"#e03e2d","customColor9":"#b96ad9","customColor10":"#3598db","customColor11":"#169179","customColor12":"#e67e23","customColor13":"#ba372a","customColor14":"#843fa1","customColor15":"#236fa1","customColor16":"#ecf0f1","customColor17":"#ced4d9","customColor18":"#95a5a6","customColor19":"#7e8c8d","customColor20":"#34495e","customColor21":"#000000","customColor22":"#ffffff","defaultMessageHeaderMarginTop":"40px","defaultMessageHeaderMarginBottom":"20px","defaultMessageItemMarginTop":"0","defaultMessageItemMarginBottom":"10px","diffAddedColor":"hsla(170, 53%, 51%, 0.4)","diffChangedColor":"hsla(43, 97%, 63%, 0.4)","diffNoneColor":"hsla(0, 0%, 80%, 0.4)","diffRemovedColor":"hsla(9, 74%, 47%, 0.4)","specialMessageHeaderMarginTop":"40px","specialMessageHeaderMarginBottom":"20px","specialMessageItemMarginTop":"0","specialMessageItemMarginBottom":"10px","tableBgColor":"transparent","tableBorderColor":"var(--lia-bs-gray-700)","tableBorderStyle":"solid","tableCellPaddingX":"5px","tableCellPaddingY":"5px","tableTextColor":"var(--lia-bs-body-color)","tableVerticalAlign":"middle","__typename":"RteThemeSettings"},"tags":{"bgColor":"var(--lia-bs-gray-200)","bgHoverColor":"var(--lia-bs-gray-400)","borderRadius":"var(--lia-bs-border-radius-sm)","color":"var(--lia-bs-body-color)","hoverColor":"var(--lia-bs-body-color)","fontWeight":"var(--lia-font-weight-md)","fontSize":"var(--lia-font-size-xxs)","textTransform":"UPPERCASE","letterSpacing":"0.5px","__typename":"TagsThemeSettings"},"toasts":{"borderRadius":"var(--lia-bs-border-radius)","paddingX":"12px","__typename":"ToastsThemeSettings"},"typography":{"fontFamilyBase":"Segoe UI","fontStyleBase":"NORMAL","fontWeightBase":"400","fontWeightLight":"300","fontWeightNormal":"400","fontWeightMd":"500","fontWeightBold":"700","letterSpacingSm":"normal","letterSpacingXs":"normal","lineHeightBase":"1.5","fontSizeBase":"16px","fontSizeXxs":"11px","fontSizeXs":"12px","fontSizeSm":"14px","fontSizeLg":"20px","fontSizeXl":"24px","smallFontSize":"14px","customFonts":[{"source":"SERVER","name":"Segoe UI","styles":[{"style":"NORMAL","weight":"400","__typename":"FontStyleData"},{"style":"NORMAL","weight":"300","__typename":"FontStyleData"},{"style":"NORMAL","weight":"600","__typename":"FontStyleData"},{"style":"NORMAL","weight":"700","__typename":"FontStyleData"},{"style":"ITALIC","weight":"400","__typename":"FontStyleData"}],"assetNames":["SegoeUI-normal-400.woff2","SegoeUI-normal-300.woff2","SegoeUI-normal-600.woff2","SegoeUI-normal-700.woff2","SegoeUI-italic-400.woff2"],"__typename":"CustomFont"},{"source":"SERVER","name":"MWF Fluent Icons","styles":[{"style":"NORMAL","weight":"400","__typename":"FontStyleData"}],"assetNames":["MWFFluentIcons-normal-400.woff2"],"__typename":"CustomFont"}],"__typename":"TypographyThemeSettings"},"unstyledListItem":{"marginBottomSm":"5px","marginBottomMd":"10px","marginBottomLg":"15px","marginBottomXl":"20px","marginBottomXxl":"25px","__typename":"UnstyledListItemThemeSettings"},"yiq":{"light":"#ffffff","dark":"#000000","__typename":"YiqThemeSettings"},"colorLightness":{"primaryDark":0.36,"primaryLight":0.74,"primaryLighter":0.89,"primaryLightest":0.95,"infoDark":0.39,"infoLight":0.72,"infoLighter":0.85,"infoLightest":0.93,"successDark":0.24,"successLight":0.62,"successLighter":0.8,"successLightest":0.91,"warningDark":0.39,"warningLight":0.68,"warningLighter":0.84,"warningLightest":0.93,"dangerDark":0.41,"dangerLight":0.72,"dangerLighter":0.89,"dangerLightest":0.95,"__typename":"ColorLightnessThemeSettings"},"localOverride":false,"__typename":"Theme"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/common/Loading/LoadingDot-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/common/Loading/LoadingDot-1776098500930","value":{"title":"Loading..."},"localOverride":false},"CachedAsset:quilt:o365.prod:pages/blogs/BlogMessagePage:board:AzureDevCommunityBlog-1776098508212":{"__typename":"CachedAsset","id":"quilt:o365.prod:pages/blogs/BlogMessagePage:board:AzureDevCommunityBlog-1776098508212","value":{"id":"BlogMessagePage","container":{"id":"Common","headerProps":{"backgroundImageProps":null,"backgroundColor":null,"addComponents":null,"removeComponents":["community.widget.bannerWidget"],"componentOrder":null,"__typename":"QuiltContainerSectionProps"},"headerComponentProps":{"community.widget.breadcrumbWidget":{"disableLastCrumbForDesktop":false}},"footerProps":null,"footerComponentProps":null,"items":[{"id":"blog-article","layout":"ONE_COLUMN","bgColor":null,"showTitle":null,"showDescription":null,"textPosition":null,"textColor":null,"sectionEditLevel":"LOCKED","bgImage":null,"disableSpacing":null,"edgeToEdgeDisplay":null,"fullHeight":null,"showBorder":null,"__typename":"OneColumnQuiltSection","columnMap":{"main":[{"id":"blogs.widget.blogArticleWidget","className":"lia-blog-container","props":null,"__typename":"QuiltComponent"}],"__typename":"OneSectionColumns"}},{"id":"section-1729184836777","layout":"MAIN_SIDE","bgColor":"transparent","showTitle":false,"showDescription":false,"textPosition":"CENTER","textColor":"var(--lia-bs-body-color)","sectionEditLevel":null,"bgImage":null,"disableSpacing":null,"edgeToEdgeDisplay":null,"fullHeight":null,"showBorder":null,"__typename":"MainSideQuiltSection","columnMap":{"main":[],"side":[{"id":"custom.widget.UnregisteredCTAWidget","className":null,"props":{"widgetVisibility":"anonymousOnly","useTitle":true,"useBackground":false,"title":"","lazyLoad":false,"widgetChooser":"custom.widget.UnregisteredCTAWidget"},"__typename":"QuiltComponent"}],"__typename":"MainSideSectionColumns"}}],"__typename":"QuiltContainer"},"__typename":"Quilt","localOverride":false},"localOverride":false},"CachedAsset:text:en_US-components/common/EmailVerification-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/common/EmailVerification-1776098500930","value":{"email.verification.title":"Email Verification Required","email.verification.message.update.email":"To participate in the community, you must first verify your email address. The verification email was sent to {email}. To change your email, visit My Settings.","email.verification.message.resend.email":"To participate in the community, you must first verify your email address. The verification email was sent to {email}. Resend email."},"localOverride":false},"CachedAsset:text:en_US-pages/blogs/BlogMessagePage-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-pages/blogs/BlogMessagePage-1776098500930","value":{"title":"{contextMessageSubject} | {communityTitle}","errorMissing":"This blog post cannot be found","name":"Blog Message Page","section.blog-article.title":"Blog Post","archivedMessageTitle":"This Content Has Been Archived","section.section-1729184836777.title":"","section.section-1729184836777.description":"","section.CncIde.title":"Blog Post","section.tifEmD.description":"","section.tifEmD.title":""},"localOverride":false},"CachedAsset:quiltWrapper:o365.prod:Common:1776098492536":{"__typename":"CachedAsset","id":"quiltWrapper:o365.prod:Common:1776098492536","value":{"id":"Common","header":{"backgroundImageProps":{"assetName":null,"backgroundSize":"COVER","backgroundRepeat":"NO_REPEAT","backgroundPosition":"CENTER_CENTER","lastModified":null,"__typename":"BackgroundImageProps"},"backgroundColor":"transparent","items":[{"id":"community.widget.navbarWidget","props":{"showUserName":true,"showRegisterLink":true,"useIconLanguagePicker":true,"useLabelLanguagePicker":true,"style":{"boxShadow":"var(--lia-bs-box-shadow-sm)","linkFontWeight":"400","controllerHighlightColor":"hsla(30, 100%, 50%)","dropdownDividerMarginBottom":"10px","hamburgerBorderHover":"none","linkFontSize":"14px","linkBoxShadowHover":"none","backgroundOpacity":0.8,"controllerBorderRadius":"var(--lia-border-radius-50)","hamburgerBgColor":"transparent","linkTextBorderBottom":"none","hamburgerColor":"var(--lia-nav-controller-icon-color)","brandLogoHeight":"30px","linkLetterSpacing":"normal","linkBgHoverColor":"transparent","collapseMenuDividerOpacity":0.16,"paddingBottom":"15px","dropdownPaddingBottom":"15px","dropdownMenuOffset":"2px","hamburgerBgHoverColor":"transparent","borderBottom":"1px solid var(--lia-bs-border-color)","hamburgerBorder":"none","dropdownPaddingX":"10px","brandMarginRightSm":"10px","linkBoxShadow":"none","linkJustifyContent":"flex-start","linkColor":"var(--lia-bs-body-color)","collapseMenuDividerBg":"var(--lia-nav-link-color)","dropdownPaddingTop":"10px","controllerTextColor":"var(--lia-nav-controller-icon-color)","controllerHighlightTextColor":"var(--lia-yiq-dark)","background":{"imageAssetName":"","color":"var(--lia-bs-white)","size":"COVER","repeat":"NO_REPEAT","position":"CENTER_CENTER","imageLastModified":""},"linkBorderRadius":"var(--lia-bs-border-radius-sm)","linkHoverColor":"var(--lia-bs-body-color)","position":"FIXED","linkBorder":"none","linkTextBorderBottomHover":"2px solid var(--lia-bs-primary)","brandMarginRight":"30px","hamburgerHoverColor":"var(--lia-nav-controller-icon-color)","linkBorderHover":"none","collapseMenuMarginLeft":"20px","linkFontStyle":"NORMAL","linkPaddingX":"10px","controllerTextHoverColor":"var(--lia-nav-controller-icon-hover-color)","paddingTop":"15px","linkPaddingY":"5px","linkTextTransform":"NONE","dropdownBorderColor":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.08)","controllerBgHoverColor":"hsla(var(--lia-bs-black-h), var(--lia-bs-black-s), var(--lia-bs-black-l), 0.1)","linkDropdownPaddingX":"var(--lia-nav-link-px)","linkBgColor":"transparent","linkDropdownPaddingY":"9px","controllerIconColor":"var(--lia-bs-body-color)","dropdownDividerMarginTop":"10px","linkGap":"10px","controllerIconHoverColor":"var(--lia-bs-body-color)"},"links":{"sideLinks":[],"logoLinks":[],"mainLinks":[{"children":[],"linkType":"INTERNAL","id":"gxcuf89792","params":{},"routeName":"CommunityPage"},{"children":[],"linkType":"EXTERNAL","id":"community-hub-link","url":"/Directory","target":"SELF"},{"children":[{"linkType":"INTERNAL","id":"Common-microsoft365-link","params":{"categoryId":"microsoft365"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-windows-link","params":{"categoryId":"Windows"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoft-security-link","params":{"categoryId":"microsoft-security"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoft-teams-link","params":{"categoryId":"MicrosoftTeams"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-azure-link","params":{"categoryId":"Azure"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-content_management-link","params":{"categoryId":"Content_Management"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoftintune-link","params":{"categoryId":"microsoftintune"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-exchange-link","params":{"categoryId":"Exchange"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-windows-server-link","params":{"categoryId":"Windows-Server"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-outlook-link","params":{"categoryId":"Outlook"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoft365-copilot-link","params":{"categoryId":"Microsoft365Copilot"},"routeName":"CategoryPage"},{"linkType":"EXTERNAL","id":"Common_Enntvz-view-all-products-link","url":"/Directory","target":"SELF"}],"linkType":"EXTERNAL","id":"products-link","url":"/","target":"SELF"},{"children":[{"linkType":"INTERNAL","id":"Common-education-sector-link","params":{"categoryId":"EducationSector"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-partner-community-link","params":{"categoryId":"PartnerCommunity"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-healthcare-and-life-sciences-link","params":{"categoryId":"HealthcareAndLifeSciences"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-i-t-ops-talk-link","params":{"categoryId":"ITOpsTalk"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-public-sector-link","params":{"categoryId":"PublicSector"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoftfor-nonprofits-link","params":{"categoryId":"MicrosoftforNonprofits"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-io-t-link","params":{"categoryId":"IoT"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-mvp-link","params":{"categoryId":"mvp"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoft-mechanics-link","params":{"categoryId":"MicrosoftMechanics"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-driving-adoption-link","params":{"categoryId":"DrivingAdoption"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoft-learn-for-educators-link","params":{"categoryId":"microsoft-learn-for-educators"},"routeName":"CategoryPage"}],"linkType":"EXTERNAL","id":"topics-link","url":"/","target":"SELF"},{"children":[],"linkType":"EXTERNAL","id":"all-blogs-link","url":"/Blogs","target":"SELF"},{"children":[],"linkType":"EXTERNAL","id":"all-events-link","url":"/Events","target":"SELF"},{"children":[{"linkType":"INTERNAL","id":"Skills-Hub-link","params":{"categoryId":"skills-hub"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Skills-Hub-Blog","params":{"boardId":"skills-hub-blog","categoryId":"skills-hub"},"routeName":"BlogBoardPage"},{"linkType":"EXTERNAL","id":"ms-learn-ext-LD","url":"/category/skills-hub?tab=grouphub","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-dynamics","url":"https://docs.microsoft.com/learn/dynamics365/?WT.mc_id=techcom_header-webpage-m365","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-m365","url":"https://docs.microsoft.com/learn/m365/?wt.mc_id=techcom_header-webpage-m365","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-security","url":"https://docs.microsoft.com/learn/topics/sci/?wt.mc_id=techcom_header-webpage-m365","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-pp","url":"https://docs.microsoft.com/learn/powerplatform/?wt.mc_id=techcom_header-webpage-powerplatform","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-github","url":"https://docs.microsoft.com/learn/github/?wt.mc_id=techcom_header-webpage-github","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-teams","url":"https://docs.microsoft.com/learn/teams/?wt.mc_id=techcom_header-webpage-teams","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-net","url":"https://docs.microsoft.com/learn/dotnet/?wt.mc_id=techcom_header-webpage-dotnet","target":"BLANK"},{"linkType":"EXTERNAL","id":"ms-learn-ext-azure","url":"https://docs.microsoft.com/learn/azure/?WT.mc_id=techcom_header-webpage-m365","target":"BLANK"}],"linkType":"INTERNAL","id":"Skills-Hub","params":{"categoryId":"skills-hub"},"routeName":"CategoryPage"},{"children":[{"linkType":"INTERNAL","id":"Common-community-info-center-link","params":{"categoryId":"Community-Info-Center"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-usergroups-link","params":{"categoryId":"usergroups"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-community-news-desk-link","params":{"categoryId":"CommunityNewsDesk"},"routeName":"CategoryPage"},{"linkType":"INTERNAL","id":"Common-microsoft-global-community-initiative-link","params":{"categoryId":"microsoft-global-community-initiative"},"routeName":"CategoryPage"}],"linkType":"INTERNAL","id":"Common-gxcuf89792-community","params":{},"routeName":"CommunityPage"}]},"showSearchIcon":true,"languagePickerStyle":"iconAndLabel"},"__typename":"QuiltComponent"},{"id":"community.widget.breadcrumbWidget","props":{"backgroundColor":"transparent","linkHighlightColor":"var(--lia-bs-primary)","visualEffects":{"showBottomBorder":true},"linkTextColor":"var(--lia-bs-gray-700)"},"__typename":"QuiltComponent"},{"id":"custom.widget.CommunityBanner","props":{"widgetVisibility":"signedInOrAnonymous","useTitle":true,"usePageWidth":false,"useBackground":false,"title":"","lazyLoad":false},"__typename":"QuiltComponent"},{"id":"custom.widget.ChatbotWidget","props":{"customComponentId":"custom.widget.ChatbotWidget","cDisplay_form":true,"useBackground":false},"__typename":"QuiltComponent"},{"id":"custom.widget.HeroBanner","props":{"widgetVisibility":"signedInOrAnonymous","usePageWidth":false,"useTitle":true,"cMax_items":3,"useBackground":false,"title":"","lazyLoad":false,"widgetChooser":"custom.widget.HeroBanner"},"__typename":"QuiltComponent"}],"__typename":"QuiltWrapperSection"},"footer":{"backgroundImageProps":{"assetName":null,"backgroundSize":"COVER","backgroundRepeat":"NO_REPEAT","backgroundPosition":"CENTER_CENTER","lastModified":null,"__typename":"BackgroundImageProps"},"backgroundColor":"transparent","items":[{"id":"custom.widget.SocialSharing","props":{"widgetVisibility":"signedInOrAnonymous","useTitle":true,"useBackground":false,"title":"","lazyLoad":false},"__typename":"QuiltComponent"},{"id":"custom.widget.MicrosoftFooter","props":{"widgetVisibility":"signedInOrAnonymous","useTitle":true,"useBackground":false,"title":"","lazyLoad":false},"__typename":"QuiltComponent"}],"__typename":"QuiltWrapperSection"},"__typename":"QuiltWrapper","localOverride":false},"localOverride":false},"CachedAsset:text:en_US-components/common/ActionFeedback-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/common/ActionFeedback-1776098500930","value":{"joinedGroupHub.title":"Welcome","joinedGroupHub.message":"You are now a member of this group and are subscribed to updates.","groupHubInviteNotFound.title":"Invitation Not Found","groupHubInviteNotFound.message":"Sorry, we could not find your invitation to the group. The owner may have canceled the invite.","groupHubNotFound.title":"Group Not Found","groupHubNotFound.message":"The grouphub you tried to join does not exist. It may have been deleted.","existingGroupHubMember.title":"Already Joined","existingGroupHubMember.message":"You are already a member of this group.","accountLocked.title":"Account Locked","accountLocked.message":"Your account has been locked due to multiple failed attempts. Try again in {lockoutTime} minutes.","editedGroupHub.title":"Changes Saved","editedGroupHub.message":"Your group has been updated.","leftGroupHub.title":"Goodbye","leftGroupHub.message":"You are no longer a member of this group and will not receive future updates.","deletedGroupHub.title":"Deleted","deletedGroupHub.message":"The group has been deleted.","groupHubCreated.title":"Group Created","groupHubCreated.message":"{groupHubName} is ready to use","accountClosed.title":"Account Closed","accountClosed.message":"The account has been closed and you will now be redirected to the homepage","resetTokenExpired.title":"Reset Password Link has Expired","resetTokenExpired.message":"Try resetting your password again","invalidUrl.title":"Invalid URL","invalidUrl.message":"The URL you're using is not recognized. Verify your URL and try again.","accountClosedForUser.title":"Account Closed","accountClosedForUser.message":"{userName}'s account is closed","inviteTokenInvalid.title":"Invitation Invalid","inviteTokenInvalid.message":"Your invitation to the community has been canceled or expired.","inviteTokenError.title":"Invitation Verification Failed","inviteTokenError.message":"The url you are utilizing is not recognized. Verify your URL and try again","pageNotFound.title":"Access Denied","pageNotFound.message":"You do not have access to this area of the community or it doesn't exist","eventAttending.title":"Responded as Attending","eventAttending.message":"You'll be notified when there's new activity and reminded as the event approaches","eventInterested.title":"Responded as Interested","eventInterested.message":"You'll be notified when there's new activity and reminded as the event approaches","eventNotFound.title":"Event Not Found","eventNotFound.message":"The event you tried to respond to does not exist.","redirectToRelatedPage.title":"Showing Related Content","redirectToRelatedPageForBaseUsers.title":"Showing Related Content","redirectToRelatedPageForBaseUsers.message":"The content you are trying to access is archived","redirectToRelatedPage.message":"The content you are trying to access is archived","relatedUrl.archivalLink.flyoutMessage":"The content you are trying to access is archived View Archived Content"},"localOverride":false},"CachedAsset:component:custom.widget.CommunityBanner-en-us-1776078299744":{"__typename":"CachedAsset","id":"component:custom.widget.CommunityBanner-en-us-1776078299744","value":{"component":{"id":"custom.widget.CommunityBanner","template":{"id":"CommunityBanner","markupLanguage":"REACT","style":null,"texts":null,"defaults":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"components":[{"id":"custom.widget.CommunityBanner","form":null,"config":null,"props":[],"__typename":"Component"}],"grouping":"CUSTOM","__typename":"ComponentTemplate"},"properties":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"form":null,"__typename":"Component","localOverride":false},"globalCss":null,"form":null},"localOverride":false},"CachedAsset:component:custom.widget.ChatbotWidget-en-us-1776078299744":{"__typename":"CachedAsset","id":"component:custom.widget.ChatbotWidget-en-us-1776078299744","value":{"component":{"id":"custom.widget.ChatbotWidget","template":{"id":"ChatbotWidget","markupLanguage":"REACT","style":null,"texts":{"chatbot.references.title":"Related Articles","chatbot.welcome.title":"Welcome!","chatbot.welcome.description":"I'm here to help you explore and discover great content.","chatbot.welcome.prompt":"Ask me a question or choose a suggestion below to get started:","chatbot.welcome.cta":"Let's dive in—what would you like to discover today?","chatbot.status.typing":"Assistant is typing…","chatbot.status.error":"error","chatbot.error.response":"Failed to get response. Please try again.","chatbot.error.processing":"There was an error processing your message.","chatbot.error.configuration":"API URL not configured","chatbot.error.network":"Network error occurred. Please check your connection and try again.","chatbot.error.timeout":"Request timed out. Please try again.","chatbot.error.emptyResponse":"I couldn't generate a response. Please try rephrasing your question.","chatbot.buttons.send":"Send","chatbot.buttons.close":"Close chat","chatbot.buttons.newChat":"Start new chat","chatbot.buttons.collapse":"Collapse chat panel","chatbot.buttons.expand":"Expand chat panel","chatbot.buttons.fullscreen":"Enter fullscreen","chatbot.buttons.exitFullscreen":"Exit fullscreen","chatbot.buttons.like":"Like this response","chatbot.buttons.dislike":"Dislike this response","chatbot.buttons.removeLike":"Remove like","chatbot.buttons.removeDislike":"Remove dislike","chatbot.aria.chatInput":"Chat input","chatbot.aria.sendMessage":"Send message","chatbot.aria.openChat":"Open chat assistant","chatbot.aria.closeChat":"Close chat assistant","chatbot.defaults.title":"Ask Tech Community","chatbot.defaults.subtitle":"Ask questions – get answers","chatbot.defaults.entryHeading":"Find answers","chatbot.defaults.entrySubtext":"Ask the agent","chatbot.defaults.placeholder":"Type your message…","chatbot.defaults.initialMessage":"Hi! I'm your assistant. Ask me something or pick a suggestion above to begin.","chatbot.suggestions.findBlogs":"Find insightful blogs","chatbot.suggestions.exploreEvents":"Explore upcoming events","chatbot.suggestions.startJourney":"Start your journey with something new","chatbot.dialog.endConversation":"End conversation","chatbot.dialog.confirmEndConversation":"Do you want to end this conversation and start over?","chatbot.dialog.endConversationButton":"End conversation","chatbot.dialog.cancel":"Cancel","chatbot.error.genericServiceUnavailable":"The service is currently unavailable. Please try again later.","chatbot.error.noResults":"We could not find any information related to your query. Try rephrasing your query."},"defaults":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"components":[{"id":"custom.widget.ChatbotWidget","form":null,"config":null,"props":[],"__typename":"Component"}],"grouping":"CUSTOM","__typename":"ComponentTemplate"},"properties":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"form":null,"__typename":"Component","localOverride":false},"globalCss":null,"form":null},"localOverride":false},"CachedAsset:component:custom.widget.HeroBanner-en-us-1776078299744":{"__typename":"CachedAsset","id":"component:custom.widget.HeroBanner-en-us-1776078299744","value":{"component":{"id":"custom.widget.HeroBanner","template":{"id":"HeroBanner","markupLanguage":"REACT","style":null,"texts":{"searchPlaceholderText":"Search this community","followActionText":"Follow","unfollowActionText":"Following","searchOnHoverText":"Please enter your search term(s) and then press return key to complete a search.","blogs.sidebar.pagetitle":"Latest Blogs | Microsoft Tech Community","followThisNode":"Follow this node","unfollowThisNode":"Unfollow this node","customField.teamsLink.title":"Microsoft teams link","customField.teamsLink.label":"Teams meeting url"},"defaults":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[{"id":"max_items","dataType":"NUMBER","list":false,"defaultValue":"3","label":"Max Items","description":"The maximum number of items to display in the carousel","possibleValues":null,"control":"INPUT","__typename":"PropDefinition"}],"__typename":"ComponentProperties"},"components":[{"id":"custom.widget.HeroBanner","form":{"fields":[{"id":"widgetChooser","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"title","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"useTitle","validation":null,"noValidation":null,"dataType":"BOOLEAN","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"useBackground","validation":null,"noValidation":null,"dataType":"BOOLEAN","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"widgetVisibility","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"moreOptions","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"cMax_items","validation":null,"noValidation":null,"dataType":"NUMBER","list":false,"control":"INPUT","defaultValue":"3","label":"Max Items","description":"The maximum number of items to display in the carousel","possibleValues":null,"__typename":"FormField"}],"layout":{"rows":[{"id":"widgetChooserGroup","type":"fieldset","as":null,"items":[{"id":"widgetChooser","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"titleGroup","type":"fieldset","as":null,"items":[{"id":"title","className":null,"__typename":"FormFieldRef"},{"id":"useTitle","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"useBackground","type":"fieldset","as":null,"items":[{"id":"useBackground","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"widgetVisibility","type":"fieldset","as":null,"items":[{"id":"widgetVisibility","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"moreOptionsGroup","type":"fieldset","as":null,"items":[{"id":"moreOptions","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"componentPropsGroup","type":"fieldset","as":null,"items":[{"id":"cMax_items","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"}],"actionButtons":null,"className":"custom_widget_HeroBanner_form","formGroupFieldSeparator":"divider","__typename":"FormLayout"},"__typename":"Form"},"config":null,"props":[],"__typename":"Component"}],"grouping":"CUSTOM","__typename":"ComponentTemplate"},"properties":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[{"id":"max_items","dataType":"NUMBER","list":false,"defaultValue":"3","label":"Max Items","description":"The maximum number of items to display in the carousel","possibleValues":null,"control":"INPUT","__typename":"PropDefinition"}],"__typename":"ComponentProperties"},"form":{"fields":[{"id":"widgetChooser","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"title","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"useTitle","validation":null,"noValidation":null,"dataType":"BOOLEAN","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"useBackground","validation":null,"noValidation":null,"dataType":"BOOLEAN","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"widgetVisibility","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"moreOptions","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"cMax_items","validation":null,"noValidation":null,"dataType":"NUMBER","list":false,"control":"INPUT","defaultValue":"3","label":"Max Items","description":"The maximum number of items to display in the carousel","possibleValues":null,"__typename":"FormField"}],"layout":{"rows":[{"id":"widgetChooserGroup","type":"fieldset","as":null,"items":[{"id":"widgetChooser","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"titleGroup","type":"fieldset","as":null,"items":[{"id":"title","className":null,"__typename":"FormFieldRef"},{"id":"useTitle","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"useBackground","type":"fieldset","as":null,"items":[{"id":"useBackground","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"widgetVisibility","type":"fieldset","as":null,"items":[{"id":"widgetVisibility","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"moreOptionsGroup","type":"fieldset","as":null,"items":[{"id":"moreOptions","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"componentPropsGroup","type":"fieldset","as":null,"items":[{"id":"cMax_items","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"}],"actionButtons":null,"className":"custom_widget_HeroBanner_form","formGroupFieldSeparator":"divider","__typename":"FormLayout"},"__typename":"Form"},"__typename":"Component","localOverride":false},"globalCss":null,"form":{"fields":[{"id":"widgetChooser","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"title","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"useTitle","validation":null,"noValidation":null,"dataType":"BOOLEAN","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"useBackground","validation":null,"noValidation":null,"dataType":"BOOLEAN","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"widgetVisibility","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"moreOptions","validation":null,"noValidation":null,"dataType":"STRING","list":null,"control":null,"defaultValue":null,"label":null,"description":null,"possibleValues":null,"__typename":"FormField"},{"id":"cMax_items","validation":null,"noValidation":null,"dataType":"NUMBER","list":false,"control":"INPUT","defaultValue":"3","label":"Max Items","description":"The maximum number of items to display in the carousel","possibleValues":null,"__typename":"FormField"}],"layout":{"rows":[{"id":"widgetChooserGroup","type":"fieldset","as":null,"items":[{"id":"widgetChooser","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"titleGroup","type":"fieldset","as":null,"items":[{"id":"title","className":null,"__typename":"FormFieldRef"},{"id":"useTitle","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"useBackground","type":"fieldset","as":null,"items":[{"id":"useBackground","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"widgetVisibility","type":"fieldset","as":null,"items":[{"id":"widgetVisibility","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"moreOptionsGroup","type":"fieldset","as":null,"items":[{"id":"moreOptions","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"},{"id":"componentPropsGroup","type":"fieldset","as":null,"items":[{"id":"cMax_items","className":null,"__typename":"FormFieldRef"}],"props":null,"legend":null,"description":null,"className":null,"viewVariant":null,"toggleState":null,"__typename":"FormFieldset"}],"actionButtons":null,"className":"custom_widget_HeroBanner_form","formGroupFieldSeparator":"divider","__typename":"FormLayout"},"__typename":"Form"}},"localOverride":false},"CachedAsset:component:custom.widget.UnregisteredCTAWidget-en-us-1776078299744":{"__typename":"CachedAsset","id":"component:custom.widget.UnregisteredCTAWidget-en-us-1776078299744","value":{"component":{"id":"custom.widget.UnregisteredCTAWidget","template":{"id":"UnregisteredCTAWidget","markupLanguage":"REACT","style":null,"texts":{"register.communityHub":"Welcome to the {name} Community Hub. Sign in to like, participate, or start a conversation.","register.category":"Welcome to the {name} Community Hub. Sign in to like, participate, or start a conversation.","register.discussionBoard":"Welcome to the {name} space. Sign in to like, reply, or start a discussion.","register.blogSpace":"Welcome to the {name} space. Sign in to like or comment on articles in this space.","register.eventSpace":"Welcome to the {name} space. Sign in to RSVP, add events to your calendar, and join the conversation.","register.ideaSpace":"Welcome to the {name} space. Sign in to vote, comment, or submit your own feedback.","buttonRegister":"Sign in","register.discussionBoardArticle":"Have a question or insight to share? Sign in to join the discussion.","register.blogSpaceArticle":"Enjoying the article? Sign in to share your thoughts.","register.eventSpaceArticle":"Don’t just watch - take part. Sign in to RSVP, ask questions, and join the discussion.","register.ideaSpaceArticle":"Sign in to submit ideas, upvote ideas, and join the conversation."},"defaults":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"components":[{"id":"custom.widget.UnregisteredCTAWidget","form":null,"config":null,"props":[],"__typename":"Component"}],"grouping":"CUSTOM","__typename":"ComponentTemplate"},"properties":{"config":{"applicablePages":[],"description":null,"fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"form":null,"__typename":"Component","localOverride":false},"globalCss":null,"form":null},"localOverride":false},"CachedAsset:component:custom.widget.SocialSharing-en-us-1776078299744":{"__typename":"CachedAsset","id":"component:custom.widget.SocialSharing-en-us-1776078299744","value":{"component":{"id":"custom.widget.SocialSharing","template":{"id":"SocialSharing","markupLanguage":"HANDLEBARS","style":".sharePage {\n display: flex;\n justify-content: center;\n background: #d7d7d7;\n padding: 0px;\n height: 60px;\n}\n.singleSocialIcons {\n display: flex;\n gap: 12px;\n list-style-type: none;\n padding: 0px;\n margin: 0;\n}\n.containers {\n display: flex;\n gap: 30px;\n}\n\n.listIcon {\n align-content: center;\n}\n.headingShare {\n display: inline;\n margin-right: 25px;\n margin-bottom: 0px;\n font-size: 20px;\n font-weight: 550;\n align-content: center;\n}\n\n@media (max-width: 990px) {\n .sharePage {\n display: flex;\n justify-content: center;\n }\n\n .containers {\n display: inline-block;\n justify-content: center;\n align-content: center;\n align-items: center;\n }\n .headingShare {\n display: flex;\n justify-content: center;\n }\n .singleSocialIcons {\n }\n}\n","texts":null,"defaults":{"config":{"applicablePages":[],"description":"Adds buttons to share to various social media websites","fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"components":[{"id":"custom.widget.SocialSharing","form":null,"config":null,"props":[],"__typename":"Component"}],"grouping":"CUSTOM","__typename":"ComponentTemplate"},"properties":{"config":{"applicablePages":[],"description":"Adds buttons to share to various social media websites","fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"form":null,"__typename":"Component","localOverride":false},"globalCss":{"css":".custom_widget_SocialSharing_sharePage_6x3n8_1 {\n display: flex;\n justify-content: center;\n background: #d7d7d7;\n padding: 0;\n height: 3.75rem;\n}\n.custom_widget_SocialSharing_singleSocialIcons_6x3n8_8 {\n display: flex;\n gap: 0.75rem;\n list-style-type: none;\n padding: 0;\n margin: 0;\n}\n.custom_widget_SocialSharing_containers_6x3n8_15 {\n display: flex;\n gap: 1.875rem;\n}\n.custom_widget_SocialSharing_listIcon_6x3n8_20 {\n align-content: center;\n}\n.custom_widget_SocialSharing_headingShare_6x3n8_23 {\n display: inline;\n margin-right: 1.5625rem;\n margin-bottom: 0;\n font-size: 1.25rem;\n font-weight: 550;\n align-content: center;\n}\n@media (max-width: 990px) {\n .custom_widget_SocialSharing_sharePage_6x3n8_1 {\n display: flex;\n justify-content: center;\n }\n\n .custom_widget_SocialSharing_containers_6x3n8_15 {\n display: inline-block;\n justify-content: center;\n align-content: center;\n align-items: center;\n }\n .custom_widget_SocialSharing_headingShare_6x3n8_23 {\n display: flex;\n justify-content: center;\n }\n .custom_widget_SocialSharing_singleSocialIcons_6x3n8_8 {\n }\n}\n","tokens":{"sharePage":"custom_widget_SocialSharing_sharePage_6x3n8_1","singleSocialIcons":"custom_widget_SocialSharing_singleSocialIcons_6x3n8_8","containers":"custom_widget_SocialSharing_containers_6x3n8_15","listIcon":"custom_widget_SocialSharing_listIcon_6x3n8_20","headingShare":"custom_widget_SocialSharing_headingShare_6x3n8_23"}},"form":null},"localOverride":false},"CachedAsset:component:custom.widget.MicrosoftFooter-en-us-1776078299744":{"__typename":"CachedAsset","id":"component:custom.widget.MicrosoftFooter-en-us-1776078299744","value":{"component":{"id":"custom.widget.MicrosoftFooter","template":{"id":"MicrosoftFooter","markupLanguage":"HANDLEBARS","style":".context-uhf {\r\n min-width: 280px;\r\n font-size: 15px;\r\n box-sizing: border-box;\r\n -ms-text-size-adjust: 100%;\r\n -webkit-text-size-adjust: 100%;\r\n & *,\r\n & *:before,\r\n & *:after {\r\n box-sizing: inherit;\r\n }\r\n a.c-uhff-link {\r\n color: #616161;\r\n word-break: break-word;\r\n text-decoration: none;\r\n }\r\n &a:link,\r\n &a:focus,\r\n &a:hover,\r\n &a:active,\r\n &a:visited {\r\n text-decoration: none;\r\n color: inherit;\r\n }\r\n & div {\r\n font-family: 'Segoe UI', SegoeUI, 'Helvetica Neue', Helvetica, Arial, sans-serif;\r\n }\r\n}\r\n.c-uhff {\r\n background: #f2f2f2;\r\n margin: -1.5625;\r\n width: auto;\r\n height: auto;\r\n}\r\n.c-uhff-nav {\r\n margin: 0 auto;\r\n max-width: calc(1600px + 10%);\r\n padding: 0 5%;\r\n box-sizing: inherit;\r\n &:before,\r\n &:after {\r\n content: ' ';\r\n display: table;\r\n clear: left;\r\n }\r\n @media only screen and (max-width: 1083px) {\r\n padding-left: 12px;\r\n }\r\n .c-heading-4 {\r\n color: #616161;\r\n word-break: break-word;\r\n font-size: 15px;\r\n line-height: 20px;\r\n padding: 36px 0 4px;\r\n font-weight: 600;\r\n }\r\n .c-uhff-nav-row {\r\n .c-uhff-nav-group {\r\n display: block;\r\n float: left;\r\n min-height: 1px;\r\n vertical-align: text-top;\r\n padding: 0 12px;\r\n width: 100%;\r\n zoom: 1;\r\n &:first-child {\r\n padding-left: 0;\r\n @media only screen and (max-width: 1083px) {\r\n padding-left: 12px;\r\n }\r\n }\r\n @media only screen and (min-width: 540px) and (max-width: 1082px) {\r\n width: 33.33333%;\r\n }\r\n @media only screen and (min-width: 1083px) {\r\n width: 16.6666666667%;\r\n }\r\n ul.c-list.f-bare {\r\n font-size: 11px;\r\n line-height: 16px;\r\n margin-top: 0;\r\n margin-bottom: 0;\r\n padding-left: 0;\r\n list-style-type: none;\r\n li {\r\n word-break: break-word;\r\n padding: 8px 0;\r\n margin: 0;\r\n }\r\n }\r\n }\r\n }\r\n}\r\n.c-uhff-base {\r\n background: #f2f2f2;\r\n margin: 0 auto;\r\n max-width: calc(1600px + 10%);\r\n padding: 30px 5% 16px;\r\n &:before,\r\n &:after {\r\n content: ' ';\r\n display: table;\r\n }\r\n &:after {\r\n clear: both;\r\n }\r\n a.c-uhff-ccpa,\r\n a.c-uhff-consumer {\r\n display: flex;\r\n float: left;\r\n font-size: 11px;\r\n line-height: 16px;\r\n padding: 4px 24px 0 0;\r\n }\r\n a.c-uhff-ccpa:hover,\r\n a.c-uhff-consumer:hover {\r\n text-decoration: underline;\r\n }\r\n ul.c-list {\r\n font-size: 11px;\r\n line-height: 16px;\r\n float: right;\r\n margin: 3px 0;\r\n color: #616161;\r\n li {\r\n padding: 0 24px 4px 0;\r\n display: inline-block;\r\n }\r\n }\r\n .c-list.f-bare {\r\n padding-left: 0;\r\n list-style-type: none;\r\n }\r\n @media only screen and (max-width: 1083px) {\r\n display: flex;\r\n flex-wrap: wrap;\r\n padding: 30px 24px 16px;\r\n }\r\n}\r\n\r\n.social-share {\r\n position: fixed;\r\n top: 60%;\r\n transform: translateY(-50%);\r\n left: 0;\r\n z-index: 1000;\r\n}\r\n\r\n.sharing-options {\r\n list-style: none;\r\n padding: 0;\r\n margin: 0;\r\n display: block;\r\n flex-direction: column;\r\n background-color: white;\r\n width: 50px;\r\n border-radius: 0px 7px 7px 0px;\r\n}\r\n.linkedin-icon {\r\n border-top-right-radius: 7px;\r\n}\r\n.linkedin-icon:hover {\r\n border-radius: 0;\r\n}\r\n\r\n.social-share-email-image:hover {\r\n border-radius: 0;\r\n}\r\n\r\n.social-link-footer:hover .linkedin-icon {\r\n border-radius: 0;\r\n}\r\n.social-link-footer:hover .social-share-email-image {\r\n border-radius: 0;\r\n}\r\n\r\n.social-link-footer img {\r\n width: 30px;\r\n height: auto;\r\n transition: filter 0.3s ease;\r\n}\r\n\r\n.social-share-list {\r\n width: 50px;\r\n}\r\n.social-share-rss-image {\r\n width: 30px;\r\n height: auto;\r\n transition: filter 0.3s ease;\r\n}\r\n.sharing-options li {\r\n width: 50px;\r\n height: 50px;\r\n padding: 8px;\r\n box-sizing: border-box;\r\n border: 2px solid white;\r\n display: inline-block;\r\n text-align: center;\r\n opacity: 1;\r\n visibility: visible;\r\n transition: border 0.3s ease; /* Smooth transition effect */\r\n border-left: none;\r\n border-bottom: none; /* Apply bottom border to only last item */\r\n}\r\n\r\n.social-share-list-linkedin {\r\n background-color: #0474b4;\r\n border-top-right-radius: 5px; /* Rounded top right corner of first item*/\r\n}\r\n.social-share-list-facebook {\r\n background-color: #3c5c9c;\r\n}\r\n.social-share-list-xicon {\r\n background-color: #000;\r\n}\r\n.social-share-list-reddit {\r\n background-color: #fc4404;\r\n}\r\n.social-share-list-bluesky {\r\n background-color: #f0f2f5;\r\n}\r\n.social-share-list-rss {\r\n background-color: #ec7b1c;\r\n}\r\n.social-share-list-mail {\r\n background-color: #848484;\r\n border-bottom-right-radius: 5px; /* Rounded bottom right corner of last item*/\r\n}\r\n.sharing-options li.social-share-list-mail {\r\n border-bottom: 2px solid white; /* Add bottom border only to the last item */\r\n height: 52px; /* Increase last child height to make in align with the hover label */\r\n}\r\n.x-icon {\r\n filter: invert(100%);\r\n transition: filter 0.3s ease;\r\n width: 20px !important;\r\n height: auto;\r\n padding-top: 5px !important;\r\n}\r\n.bluesky-icon {\r\n filter: invert(20%) sepia(100%) saturate(3000%) hue-rotate(180deg);\r\n transition: filter 0.3s ease;\r\n padding-top: 5px !important;\r\n width: 25px !important;\r\n}\r\n\r\n.share-icon {\r\n border: 2px solid transparent;\r\n display: inline-block;\r\n position: relative;\r\n}\r\n\r\n.sharing-options li:hover {\r\n border: 2px solid white;\r\n border-left: none;\r\n border-bottom: none;\r\n border-radius: 0px;\r\n}\r\n.sharing-options li.social-share-list-mail:hover {\r\n border-bottom: 2px solid white; /* Add bottom border only to the last item */\r\n}\r\n\r\n.sharing-options li:hover .label {\r\n opacity: 1;\r\n visibility: visible;\r\n border: 2px solid white;\r\n box-sizing: border-box;\r\n border-left: none;\r\n}\r\n\r\n.label {\r\n position: absolute;\r\n left: 100%;\r\n white-space: nowrap;\r\n opacity: 0;\r\n visibility: hidden;\r\n transition: all 0.2s ease;\r\n color: white;\r\n border-radius: 0 10 0 10px;\r\n top: 50%;\r\n transform: translateY(-50%);\r\n height: 52px;\r\n display: flex;\r\n align-items: center;\r\n justify-content: center;\r\n padding: 10px 12px 15px 8px;\r\n border: 2px solid white;\r\n}\r\n.linkedin {\r\n background-color: #0474b4;\r\n border-top-right-radius: 5px; /* Rounded top right corner of first item*/\r\n}\r\n.facebook {\r\n background-color: #3c5c9c;\r\n}\r\n.twitter {\r\n background-color: black;\r\n color: white;\r\n}\r\n.reddit {\r\n background-color: #fc4404;\r\n}\r\n.mail {\r\n background-color: #848484;\r\n border-bottom-right-radius: 5px; /* Rounded bottom right corner of last item*/\r\n}\r\n.bluesky {\r\n background-color: #f0f2f5;\r\n color: black;\r\n}\r\n.rss {\r\n background-color: #ec7b1c;\r\n}\r\n\r\n@media (max-width: 991px) {\r\n .social-share {\r\n display: none;\r\n }\r\n}\r\n","texts":{"heading.whatsNew":"What's new","heading.store":"Microsoft Store","heading.education":"Education","heading.business":"Business","heading.developer":"Developer & IT","heading.company":"Company","link.whatsNew.surfacePro":"Surface Pro","aria.whatsNew.surfacePro":"Surface Pro What's new","link.whatsNew.surfaceLaptop":"Surface Laptop","aria.whatsNew.surfaceLaptop":"Surface Laptop What's new","link.whatsNew.surfaceLaptopStudio2":"Surface Laptop Studio 2","aria.whatsNew.surfaceLaptopStudio2":"Surface Laptop Studio 2 What's new","link.whatsNew.copilotOrganizations":"Copilot for organizations","aria.whatsNew.copilotOrganizations":"Copilot for organizations What's new","link.whatsNew.copilotPersonal":"Copilot for personal use","aria.whatsNew.copilotPersonal":"Copilot for personal use What's new","link.whatsNew.aiInWindows":"AI in Windows","aria.whatsNew.aiInWindows":"AI in Windows What's new","link.whatsNew.exploreProducts":"Explore Microsoft products","aria.whatsNew.exploreProducts":"Explore Microsoft products What's new","link.whatsNew.windows11Apps":"Windows 11 apps","aria.whatsNew.windows11Apps":"Windows 11 apps What's new","link.store.accountProfile":"Account profile","aria.store.accountProfile":"Account profile Microsoft Store","link.store.downloadCenter":"Download Center","aria.store.downloadCenter":"Download Center Microsoft Store","link.store.support":"Microsoft Store support","aria.store.support":"Microsoft Store support Microsoft Store","link.store.returns":"Returns","aria.store.returns":"Returns Microsoft Store","link.store.orderTracking":"Order tracking","aria.store.orderTracking":"Order tracking Microsoft Store","link.store.certifiedRefurbished":"Certified Refurbished","aria.store.certifiedRefurbished":"Certified Refurbished Microsoft Store","link.store.promise":"Microsoft Store Promise","aria.store.promise":"Microsoft Store Promise Microsoft Store","link.store.flexiblePayments":"Flexible Payments","aria.store.flexiblePayments":"Flexible Payments Microsoft Store","link.education.microsoftInEducation":"Microsoft in education","aria.education.microsoftInEducation":"Microsoft in education Education","link.education.devices":"Devices for education","aria.education.devices":"Devices for education Education","link.education.teams":"Microsoft Teams for Education","aria.education.teams":"Microsoft Teams for Education Education","link.education.m365":"Microsoft 365 Education","aria.education.m365":"Microsoft 365 Education Education","link.education.howToBuy":"How to buy for your school","aria.education.howToBuy":"How to buy for your school Education","link.education.training":"Educator training and development","aria.education.training":"Educator training and development Education","link.education.deals":"Deals for students and parents","aria.education.deals":"Deals for students and parents Education","link.education.ai":"AI for education","aria.education.ai":"AI for education Education","link.business.microsoftAi":"Microsoft AI","aria.business.microsoftAi":"Microsoft AI Business","link.business.security":"Microsoft Security","aria.business.security":"Microsoft Security Business","link.business.dynamics":"Dynamics 365","aria.business.dynamics":"Dynamics 365 Business","link.business.m365":"Microsoft 365","aria.business.m365":"Microsoft 365 Business","link.business.powerPlatform":"Microsoft Power Platform","aria.business.powerPlatform":"Microsoft Power Platform Business","link.business.teams":"Microsoft Teams","aria.business.teams":"Microsoft Teams Business","link.business.m365Copilot":"Microsoft 365 Copilot","aria.business.m365Copilot":"Microsoft 365 Copilot Business","link.business.smallBusiness":"Small Business","aria.business.smallBusiness":"Small Business Business","link.developer.azure":"Azure","aria.developer.azure":"Azure Developer & IT","link.developer.developerCenter":"Microsoft Developer","aria.developer.developerCenter":"Microsoft Developer Developer & IT","link.developer.learn":"Microsoft Learn","aria.developer.learn":"Microsoft Learn Developer & IT","link.developer.aiMarketplace":"Support for AI marketplace apps","aria.developer.aiMarketplace":"Support for AI marketplace apps Developer & IT","link.developer.techCommunity":"Microsoft Tech Community","aria.developer.techCommunity":"Microsoft Tech Community Developer & IT","link.developer.marketplace":"Microsoft Marketplace","aria.developer.marketplace":"Microsoft Marketplace Developer & IT","link.developer.marketplaceRewards":"Marketplace Rewards","aria.developer.marketplaceRewards":"Marketplace Rewards Developer & IT","link.developer.visualStudio":"Visual Studio","aria.developer.visualStudio":"Visual Studio Developer & IT","link.company.careers":"Careers","aria.company.careers":"Careers Company","link.company.about":"About Microsoft","aria.company.about":"About Microsoft Company","link.company.news":"Company news","aria.company.news":"Company news Company","link.company.privacy":"Privacy at Microsoft","aria.company.privacy":"Privacy at Microsoft Company","link.company.investors":"Investors","aria.company.investors":"Investors Company","link.company.diversity":"Diversity and inclusion","aria.company.diversity":"Diversity and inclusion Company","link.company.accessibility":"Accessibility","aria.company.accessibility":"Accessibility Company","link.company.sustainability":"Sustainability","aria.company.sustainability":"Sustainability Company","ccpa.label":"Your Privacy Choices","consumerhealthprivacy.label":"Consumer Health Privacy","corp.sitemap":"Sitemap","corp.contact":"Contact Microsoft","corp.privacy":"Privacy","corp.manageCookies":"Manage cookies","corp.terms":"Terms of use","corp.trademarks":"Trademarks","corp.safetyEco":"Safety & eco","corp.recycling":"Recycling","corp.aboutAds":"About our ads","corp.microsoft":"Microsoft","social.linkedin.alt":"Share to LinkedIn","social.linkedin.label":"Share on LinkedIn","social.facebook.alt":"Share to Facebook","social.facebook.label":"Share on Facebook","social.x.alt":"Share to X","social.x.label":"Share on X","social.reddit.alt":"Share to Reddit","social.reddit.label":"Share on Reddit","social.bluesky.alt":"Share to Blue Sky","social.bluesky.label":"Share on Bluesky","social.rss.alt":"Subscribe to RSS","social.rss.label":"Share on RSS","social.email.alt":"Share to Email","social.email.label":"Share on Email"},"defaults":{"config":{"applicablePages":[],"description":"The Microsoft Footer","fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"components":[{"id":"custom.widget.MicrosoftFooter","form":null,"config":null,"props":[],"__typename":"Component"}],"grouping":"CUSTOM","__typename":"ComponentTemplate"},"properties":{"config":{"applicablePages":[],"description":"The Microsoft Footer","fetchedContent":null,"__typename":"ComponentConfiguration"},"props":[],"__typename":"ComponentProperties"},"form":null,"__typename":"Component","localOverride":false},"globalCss":{"css":".custom_widget_MicrosoftFooter_context-uhf_qp4x5_1 {\r\n min-width: 17.5rem;\r\n font-size: 0.9375rem;\r\n box-sizing: border-box;\r\n -ms-text-size-adjust: 100%;\r\n -webkit-text-size-adjust: 100%;\r\n & *,\r\n & *:before,\r\n & *:after {\r\n box-sizing: inherit;\r\n }\r\n a.custom_widget_MicrosoftFooter_c-uhff-link_qp4x5_23 {\r\n color: #616161;\r\n word-break: break-word;\r\n text-decoration: none;\r\n }\r\n &a:link,\r\n &a:focus,\r\n &a:hover,\r\n &a:active,\r\n &a:visited {\r\n text-decoration: none;\r\n color: inherit;\r\n }\r\n & div {\r\n font-family: 'Segoe UI', SegoeUI, 'Helvetica Neue', Helvetica, Arial, sans-serif;\r\n }\r\n}\r\n.custom_widget_MicrosoftFooter_c-uhff_qp4x5_23 {\r\n background: #f2f2f2;\r\n margin: -1.5625;\r\n width: auto;\r\n height: auto;\r\n}\r\n.custom_widget_MicrosoftFooter_c-uhff-nav_qp4x5_69 {\r\n margin: 0 auto;\r\n max-width: calc(100rem + 10%);\r\n padding: 0 5%;\r\n box-sizing: inherit;\r\n &:before,\r\n &:after {\r\n content: ' ';\r\n display: table;\r\n clear: left;\r\n }\r\n @media only screen and (max-width: 1083px) {\r\n padding-left: 0.75rem;\r\n }\r\n .custom_widget_MicrosoftFooter_c-heading-4_qp4x5_97 {\r\n color: #616161;\r\n word-break: break-word;\r\n font-size: 0.9375rem;\r\n line-height: 1.25rem;\r\n padding: 2.25rem 0 0.25rem;\r\n font-weight: 600;\r\n }\r\n .custom_widget_MicrosoftFooter_c-uhff-nav-row_qp4x5_113 {\r\n .custom_widget_MicrosoftFooter_c-uhff-nav-group_qp4x5_115 {\r\n display: block;\r\n float: left;\r\n min-height: 0.0625rem;\r\n vertical-align: text-top;\r\n padding: 0 0.75rem;\r\n width: 100%;\r\n zoom: 1;\r\n &:first-child {\r\n padding-left: 0;\r\n @media only screen and (max-width: 1083px) {\r\n padding-left: 0.75rem;\r\n }\r\n }\r\n @media only screen and (min-width: 540px) and (max-width: 1082px) {\r\n width: 33.33333%;\r\n }\r\n @media only screen and (min-width: 1083px) {\r\n width: 16.6666666667%;\r\n }\r\n ul.custom_widget_MicrosoftFooter_c-list_qp4x5_155.custom_widget_MicrosoftFooter_f-bare_qp4x5_155 {\r\n font-size: 0.6875rem;\r\n line-height: 1rem;\r\n margin-top: 0;\r\n margin-bottom: 0;\r\n padding-left: 0;\r\n list-style-type: none;\r\n li {\r\n word-break: break-word;\r\n padding: 0.5rem 0;\r\n margin: 0;\r\n }\r\n }\r\n }\r\n }\r\n}\r\n.custom_widget_MicrosoftFooter_c-uhff-base_qp4x5_187 {\r\n background: #f2f2f2;\r\n margin: 0 auto;\r\n max-width: calc(100rem + 10%);\r\n padding: 1.875rem 5% 1rem;\r\n &:before,\r\n &:after {\r\n content: ' ';\r\n display: table;\r\n }\r\n &:after {\r\n clear: both;\r\n }\r\n a.custom_widget_MicrosoftFooter_c-uhff-ccpa_qp4x5_213,\r\n a.custom_widget_MicrosoftFooter_c-uhff-consumer_qp4x5_215 {\r\n display: flex;\r\n float: left;\r\n font-size: 0.6875rem;\r\n line-height: 1rem;\r\n padding: 0.25rem 1.5rem 0 0;\r\n }\r\n a.custom_widget_MicrosoftFooter_c-uhff-ccpa_qp4x5_213:hover,\r\n a.custom_widget_MicrosoftFooter_c-uhff-consumer_qp4x5_215:hover {\r\n text-decoration: underline;\r\n }\r\n ul.custom_widget_MicrosoftFooter_c-list_qp4x5_155 {\r\n font-size: 0.6875rem;\r\n line-height: 1rem;\r\n float: right;\r\n margin: 0.1875rem 0;\r\n color: #616161;\r\n li {\r\n padding: 0 1.5rem 0.25rem 0;\r\n display: inline-block;\r\n }\r\n }\r\n .custom_widget_MicrosoftFooter_c-list_qp4x5_155.custom_widget_MicrosoftFooter_f-bare_qp4x5_155 {\r\n padding-left: 0;\r\n list-style-type: none;\r\n }\r\n @media only screen and (max-width: 1083px) {\r\n display: flex;\r\n flex-wrap: wrap;\r\n padding: 1.875rem 1.5rem 1rem;\r\n }\r\n}\r\n.custom_widget_MicrosoftFooter_social-share_qp4x5_281 {\r\n position: fixed;\r\n top: 60%;\r\n transform: translateY(-50%);\r\n left: 0;\r\n z-index: 1000;\r\n}\r\n.custom_widget_MicrosoftFooter_sharing-options_qp4x5_297 {\r\n list-style: none;\r\n padding: 0;\r\n margin: 0;\r\n display: block;\r\n flex-direction: column;\r\n background-color: white;\r\n width: 3.125rem;\r\n border-radius: 0 0.4375rem 0.4375rem 0;\r\n}\r\n.custom_widget_MicrosoftFooter_linkedin-icon_qp4x5_317 {\r\n border-top-right-radius: 7px;\r\n}\r\n.custom_widget_MicrosoftFooter_linkedin-icon_qp4x5_317:hover {\r\n border-radius: 0;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-email-image_qp4x5_331:hover {\r\n border-radius: 0;\r\n}\r\n.custom_widget_MicrosoftFooter_social-link-footer_qp4x5_339:hover .custom_widget_MicrosoftFooter_linkedin-icon_qp4x5_317 {\r\n border-radius: 0;\r\n}\r\n.custom_widget_MicrosoftFooter_social-link-footer_qp4x5_339:hover .custom_widget_MicrosoftFooter_social-share-email-image_qp4x5_331 {\r\n border-radius: 0;\r\n}\r\n.custom_widget_MicrosoftFooter_social-link-footer_qp4x5_339 img {\r\n width: 1.875rem;\r\n height: auto;\r\n transition: filter 0.3s ease;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list_qp4x5_365 {\r\n width: 3.125rem;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-rss-image_qp4x5_371 {\r\n width: 1.875rem;\r\n height: auto;\r\n transition: filter 0.3s ease;\r\n}\r\n.custom_widget_MicrosoftFooter_sharing-options_qp4x5_297 li {\r\n width: 3.125rem;\r\n height: 3.125rem;\r\n padding: 0.5rem;\r\n box-sizing: border-box;\r\n border: 2px solid white;\r\n display: inline-block;\r\n text-align: center;\r\n opacity: 1;\r\n visibility: visible;\r\n transition: border 0.3s ease; /* Smooth transition effect */\r\n border-left: none;\r\n border-bottom: none; /* Apply bottom border to only last item */\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list-linkedin_qp4x5_411 {\r\n background-color: #0474b4;\r\n border-top-right-radius: 5px; /* Rounded top right corner of first item*/\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list-facebook_qp4x5_419 {\r\n background-color: #3c5c9c;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list-xicon_qp4x5_425 {\r\n background-color: #000;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list-reddit_qp4x5_431 {\r\n background-color: #fc4404;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list-bluesky_qp4x5_437 {\r\n background-color: #f0f2f5;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list-rss_qp4x5_443 {\r\n background-color: #ec7b1c;\r\n}\r\n.custom_widget_MicrosoftFooter_social-share-list-mail_qp4x5_449 {\r\n background-color: #848484;\r\n border-bottom-right-radius: 5px; /* Rounded bottom right corner of last item*/\r\n}\r\n.custom_widget_MicrosoftFooter_sharing-options_qp4x5_297 li.custom_widget_MicrosoftFooter_social-share-list-mail_qp4x5_449 {\r\n border-bottom: 2px solid white; /* Add bottom border only to the last item */\r\n height: 3.25rem; /* Increase last child height to make in align with the hover label */\r\n}\r\n.custom_widget_MicrosoftFooter_x-icon_qp4x5_465 {\r\n filter: invert(100%);\r\n transition: filter 0.3s ease;\r\n width: 1.25rem !important;\r\n height: auto;\r\n padding-top: 0.3125rem !important;\r\n}\r\n.custom_widget_MicrosoftFooter_bluesky-icon_qp4x5_479 {\r\n filter: invert(20%) sepia(100%) saturate(3000%) hue-rotate(180deg);\r\n transition: filter 0.3s ease;\r\n padding-top: 0.3125rem !important;\r\n width: 1.5625rem !important;\r\n}\r\n.custom_widget_MicrosoftFooter_share-icon_qp4x5_493 {\r\n border: 2px solid transparent;\r\n display: inline-block;\r\n position: relative;\r\n}\r\n.custom_widget_MicrosoftFooter_sharing-options_qp4x5_297 li:hover {\r\n border: 2px solid white;\r\n border-left: none;\r\n border-bottom: none;\r\n border-radius: 0;\r\n}\r\n.custom_widget_MicrosoftFooter_sharing-options_qp4x5_297 li.custom_widget_MicrosoftFooter_social-share-list-mail_qp4x5_449:hover {\r\n border-bottom: 2px solid white; /* Add bottom border only to the last item */\r\n}\r\n.custom_widget_MicrosoftFooter_sharing-options_qp4x5_297 li:hover .custom_widget_MicrosoftFooter_label_qp4x5_525 {\r\n opacity: 1;\r\n visibility: visible;\r\n border: 2px solid white;\r\n box-sizing: border-box;\r\n border-left: none;\r\n}\r\n.custom_widget_MicrosoftFooter_label_qp4x5_525 {\r\n position: absolute;\r\n left: 100%;\r\n white-space: nowrap;\r\n opacity: 0;\r\n visibility: hidden;\r\n transition: all 0.2s ease;\r\n color: white;\r\n border-radius: 0 10 0 0.625rem;\r\n top: 50%;\r\n transform: translateY(-50%);\r\n height: 3.25rem;\r\n display: flex;\r\n align-items: center;\r\n justify-content: center;\r\n padding: 0.625rem 0.75rem 0.9375rem 0.5rem;\r\n border: 2px solid white;\r\n}\r\n.custom_widget_MicrosoftFooter_linkedin_qp4x5_317 {\r\n background-color: #0474b4;\r\n border-top-right-radius: 5px; /* Rounded top right corner of first item*/\r\n}\r\n.custom_widget_MicrosoftFooter_facebook_qp4x5_585 {\r\n background-color: #3c5c9c;\r\n}\r\n.custom_widget_MicrosoftFooter_twitter_qp4x5_591 {\r\n background-color: black;\r\n color: white;\r\n}\r\n.custom_widget_MicrosoftFooter_reddit_qp4x5_599 {\r\n background-color: #fc4404;\r\n}\r\n.custom_widget_MicrosoftFooter_mail_qp4x5_605 {\r\n background-color: #848484;\r\n border-bottom-right-radius: 5px; /* Rounded bottom right corner of last item*/\r\n}\r\n.custom_widget_MicrosoftFooter_bluesky_qp4x5_479 {\r\n background-color: #f0f2f5;\r\n color: black;\r\n}\r\n.custom_widget_MicrosoftFooter_rss_qp4x5_621 {\r\n background-color: #ec7b1c;\r\n}\r\n@media (max-width: 991px) {\r\n .custom_widget_MicrosoftFooter_social-share_qp4x5_281 {\r\n display: none;\r\n }\r\n}\r\n","tokens":{"context-uhf":"custom_widget_MicrosoftFooter_context-uhf_qp4x5_1","c-uhff-link":"custom_widget_MicrosoftFooter_c-uhff-link_qp4x5_23","c-uhff":"custom_widget_MicrosoftFooter_c-uhff_qp4x5_23","c-uhff-nav":"custom_widget_MicrosoftFooter_c-uhff-nav_qp4x5_69","c-heading-4":"custom_widget_MicrosoftFooter_c-heading-4_qp4x5_97","c-uhff-nav-row":"custom_widget_MicrosoftFooter_c-uhff-nav-row_qp4x5_113","c-uhff-nav-group":"custom_widget_MicrosoftFooter_c-uhff-nav-group_qp4x5_115","c-list":"custom_widget_MicrosoftFooter_c-list_qp4x5_155","f-bare":"custom_widget_MicrosoftFooter_f-bare_qp4x5_155","c-uhff-base":"custom_widget_MicrosoftFooter_c-uhff-base_qp4x5_187","c-uhff-ccpa":"custom_widget_MicrosoftFooter_c-uhff-ccpa_qp4x5_213","c-uhff-consumer":"custom_widget_MicrosoftFooter_c-uhff-consumer_qp4x5_215","social-share":"custom_widget_MicrosoftFooter_social-share_qp4x5_281","sharing-options":"custom_widget_MicrosoftFooter_sharing-options_qp4x5_297","linkedin-icon":"custom_widget_MicrosoftFooter_linkedin-icon_qp4x5_317","social-share-email-image":"custom_widget_MicrosoftFooter_social-share-email-image_qp4x5_331","social-link-footer":"custom_widget_MicrosoftFooter_social-link-footer_qp4x5_339","social-share-list":"custom_widget_MicrosoftFooter_social-share-list_qp4x5_365","social-share-rss-image":"custom_widget_MicrosoftFooter_social-share-rss-image_qp4x5_371","social-share-list-linkedin":"custom_widget_MicrosoftFooter_social-share-list-linkedin_qp4x5_411","social-share-list-facebook":"custom_widget_MicrosoftFooter_social-share-list-facebook_qp4x5_419","social-share-list-xicon":"custom_widget_MicrosoftFooter_social-share-list-xicon_qp4x5_425","social-share-list-reddit":"custom_widget_MicrosoftFooter_social-share-list-reddit_qp4x5_431","social-share-list-bluesky":"custom_widget_MicrosoftFooter_social-share-list-bluesky_qp4x5_437","social-share-list-rss":"custom_widget_MicrosoftFooter_social-share-list-rss_qp4x5_443","social-share-list-mail":"custom_widget_MicrosoftFooter_social-share-list-mail_qp4x5_449","x-icon":"custom_widget_MicrosoftFooter_x-icon_qp4x5_465","bluesky-icon":"custom_widget_MicrosoftFooter_bluesky-icon_qp4x5_479","share-icon":"custom_widget_MicrosoftFooter_share-icon_qp4x5_493","label":"custom_widget_MicrosoftFooter_label_qp4x5_525","linkedin":"custom_widget_MicrosoftFooter_linkedin_qp4x5_317","facebook":"custom_widget_MicrosoftFooter_facebook_qp4x5_585","twitter":"custom_widget_MicrosoftFooter_twitter_qp4x5_591","reddit":"custom_widget_MicrosoftFooter_reddit_qp4x5_599","mail":"custom_widget_MicrosoftFooter_mail_qp4x5_605","bluesky":"custom_widget_MicrosoftFooter_bluesky_qp4x5_479","rss":"custom_widget_MicrosoftFooter_rss_qp4x5_621"}},"form":null},"localOverride":false},"CachedAsset:text:en_US-components/community/Breadcrumb-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/community/Breadcrumb-1776098500930","value":{"navLabel":"Breadcrumbs","dropdown":"Additional parent page navigation"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageBanner-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageBanner-1776098500930","value":{"messageMarkedAsSpam":"This post has been marked as spam","messageMarkedAsSpam@board:TKB":"This article has been marked as spam","messageMarkedAsSpam@board:BLOG":"This post has been marked as spam","messageMarkedAsSpam@board:FORUM":"This discussion has been marked as spam","messageMarkedAsSpam@board:OCCASION":"This event has been marked as spam","messageMarkedAsSpam@board:IDEA":"This idea has been marked as spam","manageSpam":"Manage Spam","messageMarkedAsAbuse":"This post has been marked as abuse","messageMarkedAsAbuse@board:TKB":"This article has been marked as abuse","messageMarkedAsAbuse@board:BLOG":"This post has been marked as abuse","messageMarkedAsAbuse@board:FORUM":"This discussion has been marked as abuse","messageMarkedAsAbuse@board:OCCASION":"This event has been marked as abuse","messageMarkedAsAbuse@board:IDEA":"This idea has been marked as abuse","preModCommentAuthorText":"This comment will be published as soon as it is approved","preModCommentModeratorText":"This comment is awaiting moderation","messageMarkedAsOther":"This post has been rejected due to other reasons","messageMarkedAsOther@board:TKB":"This article has been rejected due to other reasons","messageMarkedAsOther@board:BLOG":"This post has been rejected due to other reasons","messageMarkedAsOther@board:FORUM":"This discussion has been rejected due to other reasons","messageMarkedAsOther@board:OCCASION":"This event has been rejected due to other reasons","messageMarkedAsOther@board:IDEA":"This idea has been rejected due to other reasons","messageArchived":"This post was archived on {date}","relatedUrl":"View Related Content","relatedContentText":"Showing related content","archivedContentLink":"View Archived Content"},"localOverride":false},"Category:category:Exchange":{"__typename":"Category","id":"category:Exchange","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:Outlook":{"__typename":"Category","id":"category:Outlook","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:Community-Info-Center":{"__typename":"Category","id":"category:Community-Info-Center","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:EducationSector":{"__typename":"Category","id":"category:EducationSector","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:DrivingAdoption":{"__typename":"Category","id":"category:DrivingAdoption","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:Windows-Server":{"__typename":"Category","id":"category:Windows-Server","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:MicrosoftTeams":{"__typename":"Category","id":"category:MicrosoftTeams","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:PublicSector":{"__typename":"Category","id":"category:PublicSector","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:microsoft365":{"__typename":"Category","id":"category:microsoft365","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:IoT":{"__typename":"Category","id":"category:IoT","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:HealthcareAndLifeSciences":{"__typename":"Category","id":"category:HealthcareAndLifeSciences","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:ITOpsTalk":{"__typename":"Category","id":"category:ITOpsTalk","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:MicrosoftMechanics":{"__typename":"Category","id":"category:MicrosoftMechanics","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:MicrosoftforNonprofits":{"__typename":"Category","id":"category:MicrosoftforNonprofits","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:PartnerCommunity":{"__typename":"Category","id":"category:PartnerCommunity","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:Microsoft365Copilot":{"__typename":"Category","id":"category:Microsoft365Copilot","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:Windows":{"__typename":"Category","id":"category:Windows","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:Content_Management":{"__typename":"Category","id":"category:Content_Management","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:CommunityNewsDesk":{"__typename":"Category","id":"category:CommunityNewsDesk","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:microsoft-learn-for-educators":{"__typename":"Category","id":"category:microsoft-learn-for-educators","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:mvp":{"__typename":"Category","id":"category:mvp","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:microsoft-security":{"__typename":"Category","id":"category:microsoft-security","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:microsoftintune":{"__typename":"Category","id":"category:microsoftintune","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:microsoft-global-community-initiative":{"__typename":"Category","id":"category:microsoft-global-community-initiative","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:usergroups":{"__typename":"Category","id":"category:usergroups","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Category:category:skills-hub":{"__typename":"Category","id":"category:skills-hub","categoryPolicies":{"__typename":"CategoryPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"Blog:board:skills-hub-blog":{"__typename":"Blog","id":"board:skills-hub-blog","blogPolicies":{"__typename":"BlogPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}},"boardPolicies":{"__typename":"BoardPolicies","canReadNode":{"__typename":"PolicyResult","failureReason":null}}},"CachedAsset:text:en_US-components/community/Navbar-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/community/Navbar-1776098500930","value":{"community":"Community Home","inbox":"Inbox","manageContent":"Manage Content","tos":"Terms of Service","forgotPassword":"Forgot Password","themeEditor":"Theme Editor","edit":"Edit Navigation Bar","skipContent":"Skip to content","gxcuf89792":"Tech Community","windows-server":"Windows Server","ms-learn-ext-security":"Microsoft Security","Common_Enntvz-i-t-ops-talk-link":"ITOps Talk","education-sector":"Education Sector","Common-external-link-9":"Microsoft 365","Common-external-link-8":"Dynamics 365","Common-external-link-7":"Skilling Room Directory","Common-external-link-6":"Events","Common-external-link-5":"Blogs","Common-external-link-4":"View All","Common-gxcuf89792-community":"Community","Common-external-link-3":"Topics","microsoft365":"Microsoft 365","Common_Enntvz-community-news-desk-link":"Community News Desk","Common_Enntvz-azure-link":"Azure","Common-community-info-center-link":"Lounge","azure":"Azure","Common_Enntvz-windows-link":"Windows","Common_Enntvz-education-sector-link":"Education Sector","Common-windows-server-link":"Windows Server","products-link":"Products","Common_Enntvz-partner-community-link":"Microsoft Partner Community","microsoft-learn-blog":"Blog","Common-external-link-2":"View All","community-hub-link":"Community Hubs","Common-mvp-link":"Microsoft MVP Program","community-info-center":"Lounge","microsoft-endpoint-manager":"Microsoft Intune","startupsat-microsoft":"Startups at Microsoft","ms-learn-ext-azure":"Azure","Common_Enntvz-content_management-link":"Content Management","ms-learn-ext-github":"Github","Common-microsoft365-link":"Microsoft 365","Common-i-t-ops-talk-link":"ITOps Talk","Common_Enntvz-view-all-products-link":"View All","Common-microsoft-global-community-initiative-link":"Microsoft Global Community Initiative (MGCI)","all-events-link":"Events","Common_Enntvz-microsoft-learn-for-educators-link":"Microsoft Learn for Educators","Common-external-link":"Community Hubs","Common-partner-community-link":"Microsoft Partner Community","Common-microsoft-learn-for-educators-link":"Microsoft Learn for Educators","Common_Enntvz-microsoft-teams-link":"Microsoft Teams","driving-adoption":"Driving Adoption","microsoft-learn":"Microsoft Learn","Common-healthcare-and-life-sciences-link":"Healthcare and Life Sciences","planner":"Outlook","Common_Enntvz-exchange-link":"Exchange","healthcare-and-life-sciences":"Healthcare and Life Sciences","Common-external-link-10":"View All","Common-driving-adoption-link":"Driving Adoption","ms-learn-ext-pp":"Power Platform","Common_Enntvz-windows-server-link":"Windows Server","Common-io-t-link":"Internet of Things (IoT)","Skills-Hub":"Skills Hub","microsoft-teams":"Microsoft Teams","Common-outlook-link":"Outlook","Common_Enntvz-public-sector-link":"Public Sector","Common-windows-link":"Windows","all-blogs-link":"Blogs","communities":"Products","Common_Enntvz-usergroups-link":"User Groups","Common_Enntvz-microsoft-global-community-initiative-link":"Microsoft Global Community Initiative (MGCI)","Skills-Hub-link":"Community","Common_Enntvz-io-t-link":"Internet of Things (IoT)","ms-learn-ext-m365":"Microsoft 365","Common_Enntvz-microsoft-mechanics-link":"Microsoft Mechanics","microsoft-learn-community":"Community","partner-community":"Microsoft Partner Community","Common-microsoft-mechanics-link":"Microsoft Mechanics","Common_Enntvz-healthcare-and-life-sciences-link":"Healthcare and Life Sciences","microsoft-mechanics":"Microsoft Mechanics","Common-microsoft-security-link":"Microsoft Security","Common-education-sector-link":"Education Sector","Skills-Hub-Blog":"Blog","i-t-ops-talk":"ITOps Talk","microsoft-securityand-compliance":"Microsoft Security","Common_Enntvz-microsoftintune-link":"Microsoft Intune","Common-azure-link":"Azure","Common-microsoftintune-link":"Microsoft Intune","Common_Enntvz-view-all-topics-link":"View All","Common-usergroups-link":"User Groups","Common-public-sector-link":"Public Sector","Common_Enntvz-microsoft-security-link":"Microsoft Security","Common_Enntvz-outlook-link":"Outlook","Common_Enntvz-mvp-link":"Microsoft MVP Program","exchange":"Exchange","topics-link":"Topics","io-t":"Internet of Things (IoT)","Common-microsoft365-copilot-link":"Microsoft 365 Copilot","Common-microsoft-teams-link":"Microsoft Teams","s-m-b":"Nonprofit Community","Common_Enntvz-community-info-center-link":"Lounge","Common_Enntvz-microsoft365-copilot-link":"Microsoft 365 Copilot","Common_Enntvz-microsoftfor-nonprofits-link":"Nonprofit Community","Common_Enntvz-microsoft365-link":"Microsoft 365","Common-content_management-link":"Content Management","ms-learn-ext-teams":"Teams","s-q-l-server":"Content Management","products-services":"Products","Common-community-news-desk-link":"Community News Desk","ms-learn-ext-LD":"Skilling Room Directory","Common-exchange-link":"Exchange","Common-gxcuf89792-link":"Tech Community","windows":"Windows","public-sector":"Public Sector","Common_Enntvz-driving-adoption-link":"Driving Adoption","Common-microsoftfor-nonprofits-link":"Nonprofit Community","ms-learn-ext-net":".NET","ms-learn-ext-dynamics":"Dynamics 365","a-i":"AI and Machine Learning","outlook":"Microsoft 365 Copilot"},"localOverride":false},"CachedAsset:text:en_US-components/community/NavbarHamburgerDropdown-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/community/NavbarHamburgerDropdown-1776098500930","value":{"hamburgerLabelOpen":"Open Side Menu","hamburgerLabelClose":"Close Side Menu"},"localOverride":false},"CachedAsset:text:en_US-components/community/BrandLogo-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/community/BrandLogo-1776098500930","value":{"logoAlt":"Khoros","themeLogoAlt":"Brand Logo","linkAriaLabel":"Go to community home page"},"localOverride":false},"CachedAsset:text:en_US-components/community/NavbarTextLinks-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/community/NavbarTextLinks-1776098500930","value":{"more":"More"},"localOverride":false},"CachedAsset:text:en_US-components/search/SpotlightSearchIcon-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/search/SpotlightSearchIcon-1776098500930","value":{"search":"Search"},"localOverride":false},"CachedAsset:text:en_US-components/authentication/AuthenticationLink-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/authentication/AuthenticationLink-1776098500930","value":{"title.login":"Sign In","title.registration":"Register","title.forgotPassword":"Forgot Password","title.multiAuthLogin":"Sign In"},"localOverride":false},"CachedAsset:text:en_US-components/nodes/NodeLink-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/nodes/NodeLink-1776098500930","value":{"place":"Go back to {name}"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageView/MessageViewStandard-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageView/MessageViewStandard-1776098500930","value":{"anonymous":"Anonymous","author":"[{messageAuthorLogin}](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)","authorBy":"[{messageAuthorLogin}](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)","board":"[{messageBoardTitle}](https://techcommunity.microsoft.com/blog/azuredevcommunityblog/building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework/4488249)","replyToUser":" to {parentAuthor}","showMoreReplies":"Show More","replyText":"Reply","repliesText":"Replies","markedAsSolved":"Marked as Solution","messageStatus":"Status: ","statusChanged":"Status changed: {previousStatus} to {currentStatus}","statusAdded":"Status added: {status}","statusRemoved":"Status removed: {status}","labelExpand":"expand replies","labelCollapse":"collapse replies","unhelpfulReason.reason1":"Content is outdated","unhelpfulReason.reason2":"Article is missing information","unhelpfulReason.reason3":"Content is for a different Product","unhelpfulReason.reason4":"Doesn't match what I was searching for"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageReplyCallToAction-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageReplyCallToAction-1776098500930","value":{"leaveReply":"Leave a reply...","leaveReply@board:BLOG@message:root":"Leave a comment...","leaveReply@board:TKB@message:root":"Leave a comment...","leaveReply@board:IDEA@message:root":"Leave a comment...","leaveReply@board:OCCASION@message:root":"Leave a comment...","repliesTurnedOff.FORUM":"Replies are turned off for this topic","repliesTurnedOff.BLOG":"Comments are turned off for this topic","repliesTurnedOff.TKB":"Comments are turned off for this topic","repliesTurnedOff.IDEA":"Comments are turned off for this topic","repliesTurnedOff.OCCASION":"Comments are turned off for this topic","infoText":"Stop poking me!"},"localOverride":false},"CachedAsset:text:en_US-components/community/NavbarDropdownToggle-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/community/NavbarDropdownToggle-1776098500930","value":{"ariaLabelClosed":"Press the down arrow to open the menu"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageCoverImage-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageCoverImage-1776098500930","value":{"coverImageTitle":"Cover Image"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/nodes/NodeTitle-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/nodes/NodeTitle-1776098500930","value":{"nodeTitle":"{nodeTitle, select, community {Community} other {{nodeTitle}}} "},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageTimeToRead-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageTimeToRead-1776098500930","value":{"minReadText":"{min} MIN READ"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageSubject-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageSubject-1776098500930","value":{"noSubject":"(no subject)"},"localOverride":false},"CachedAsset:text:en_US-components/users/UserLink-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/users/UserLink-1776098500930","value":{"authorName":"View Profile: {author}","anonymous":"Anonymous","ariaLabel.rank":"Rank: {rankName}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/users/UserRank-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/users/UserRank-1776098500930","value":{"rankName":"{rankName}","userRank":"Author rank {rankName}"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageTime-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageTime-1776098500930","value":{"postTime":"Published: {time}","lastPublishTime":"Last Update: {time}","conversation.lastPostingActivityTime":"Last posting activity time: {time}","conversation.lastPostTime":"Last post time: {time}","moderationData.rejectTime":"Rejected time: {time}"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageBody-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageBody-1776098500930","value":{"showMessageBody":"Show More","mentionsErrorTitle":"{mentionsType, select, board {Board} user {User} message {Message} other {}} No Longer Available","mentionsErrorMessage":"The {mentionsType} you are trying to view has been removed from the community.","videoProcessing":"Video is being processed. Please try again in a few minutes.","bannerTitle":"Video provider requires cookies to play the video. Accept to continue or {url} it directly on the provider's site.","buttonTitle":"Accept","urlText":"watch"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageCustomFields-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageCustomFields-1776098500930","value":{"CustomField.default.label":"Value of {name}"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageRevision-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageRevision-1776098500930","value":{"lastUpdatedDatePublished":"{publishCount, plural, one{Published} other{Updated}} {date}","lastUpdatedDateDraft":"Created {date}","version":"Version {major}.{minor}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/common/QueryHandler-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/common/QueryHandler-1776098500930","value":{"title":"Query Handler"},"localOverride":false},"CachedAsset:text:en_US-components/tags/TagList-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/tags/TagList-1776098500930","value":{"showMoreFor":"Show more for {title}"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageReplyButton-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageReplyButton-1776098500930","value":{"repliesCount":"{count}","title":"Reply","title@board:BLOG@message:root":"Comment","title@board:TKB@message:root":"Comment","title@board:IDEA@message:root":"Comment","title@board:OCCASION@message:root":"Comment"},"localOverride":false},"CachedAsset:text:en_US-components/messages/MessageAuthorBio-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/messages/MessageAuthorBio-1776098500930","value":{"sendMessage":"Send Message","actionMessage":"Follow this blog board to get notified when there's new activity","coAuthor":"CO-PUBLISHER","contributor":"CONTRIBUTOR","userProfile":"View Profile","iconlink":"Go to {name} {type}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/users/UserAvatar-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/users/UserAvatar-1776098500930","value":{"altText":"{login}'s avatar","altTextGeneric":"User's avatar"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/ranks/UserRankLabel-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/ranks/UserRankLabel-1776098500930","value":{"altTitle":"Icon for {rankName} rank"},"localOverride":false},"CachedAsset:text:en_US-components/tags/TagView/TagViewChip-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/tags/TagView/TagViewChip-1776098500930","value":{"tagLabelName":"Tag name {tagName}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/common/Pager/PagerLoadMore-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/common/Pager/PagerLoadMore-1776098500930","value":{"loadMore":"Show More"},"localOverride":false},"CachedAsset:text:en_US-components/users/UserRegistrationDate-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-components/users/UserRegistrationDate-1776098500930","value":{"noPrefix":"{date}","withPrefix":"Joined {date}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/nodes/NodeAvatar-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/nodes/NodeAvatar-1776098500930","value":{"altTitle":"Node avatar for {nodeTitle}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/nodes/NodeDescription-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/nodes/NodeDescription-1776098500930","value":{"description":"{description}"},"localOverride":false},"CachedAsset:text:en_US-shared/client/components/nodes/NodeIcon-1776098500930":{"__typename":"CachedAsset","id":"text:en_US-shared/client/components/nodes/NodeIcon-1776098500930","value":{"contentType":"Content Type {style, select, FORUM {Forum} BLOG {Blog} TKB {Knowledge Base} IDEA {Ideas} OCCASION {Events} other {}} icon"},"localOverride":false}}}},"page":"/blogs/BlogMessagePage/BlogMessagePage","query":{"boardId":"azuredevcommunityblog","messageSubject":"building-interactive-agent-uis-with-ag-ui-and-microsoft-agent-framework","messageId":"4488249"},"buildId":"VXuOn2D5MfObWEiRanLQ9","runtimeConfig":{"buildInformationVisible":false,"logLevelApp":"info","logLevelMetrics":"info","surveysEnabled":true,"openTelemetry":{"clientEnabled":false,"configName":"o365","serviceVersion":"26.1.0","universe":"prod","collector":"http://localhost:4318","logLevel":"error","routeChangeAllowedTime":"5000","headers":"","enableDiagnostic":"false","maxAttributeValueLength":"4095"},"apolloDevToolsEnabled":false,"quiltLazyLoadThreshold":"3"},"isFallback":false,"isExperimentalCompile":false,"dynamicIds":["components_community_Navbar_NavbarWidget","components_community_Breadcrumb_BreadcrumbWidget","components_customComponent_CustomComponent","components_blogs_BlogArticleWidget","components_external_components_ExternalComponent","components_messages_MessageView_MessageViewStandard","shared_client_components_common_List_UnwrappedList","components_tags_TagView","components_tags_TagView_TagViewChip","shared_client_components_common_Pager_PagerLoadMore","components_customComponent_CustomComponentContent_TemplateContent"],"appGip":true,"scriptLoader":[{"id":"analytics","src":"https://techcommunity.microsoft.com/t5/s/gxcuf89792/pagescripts/1751476272000/analytics.js?page.id=BlogMessagePage&entity.id=board%3Aazuredevcommunityblog&entity.id=message%3A4488249","strategy":"afterInteractive"}]}

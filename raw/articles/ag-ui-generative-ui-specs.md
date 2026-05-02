Title: Generative UI - Agent User Interaction Protocol

URL Source: https://docs.ag-ui.com/concepts/generative-ui-specs

Markdown Content:
# Generative UI - Agent User Interaction Protocol

[Skip to main content](https://docs.ag-ui.com/concepts/generative-ui-specs#content-area)

[Agent User Interaction Protocol home page![Image 1: light logo](https://mintcdn.com/tawkitai/_M3XSWL1wNxyVwLE/logo/light.svg?fit=max&auto=format&n=_M3XSWL1wNxyVwLE&q=85&s=009d5382e12ddeff3eebc5530b3b7003)![Image 2: dark logo](https://mintcdn.com/tawkitai/_M3XSWL1wNxyVwLE/logo/dark.svg?fit=max&auto=format&n=_M3XSWL1wNxyVwLE&q=85&s=f765e7f86ada1de62caac61bcea6f57b)](https://docs.ag-ui.com/)

Search...

⌘K

*   [Discord](https://discord.gg/Jd3FzfdJa8)
*   [ag-ui-protocol/ag-ui](https://github.com/ag-ui-protocol/ag-ui "ag-ui-protocol/ag-ui")
*   [ag-ui-protocol/ag-ui](https://github.com/ag-ui-protocol/ag-ui "ag-ui-protocol/ag-ui")

Search...

Navigation

Concepts

Generative UI

[Documentation](https://docs.ag-ui.com/introduction)[SDKs](https://docs.ag-ui.com/sdk/js/core/overview)

*   [TypeScript SDK](https://docs.ag-ui.com/sdk/js/core/overview)
*   [Python SDK](https://docs.ag-ui.com/sdk/python/core/overview)

##### Get Started

*   [AG-UI Overview](https://docs.ag-ui.com/introduction)
*   [MCP, A2A, and AG-UI](https://docs.ag-ui.com/agentic-protocols)
*   Quickstart  

##### Concepts

*   [Core architecture](https://docs.ag-ui.com/concepts/architecture)
*   [Events](https://docs.ag-ui.com/concepts/events)
*   [Agents](https://docs.ag-ui.com/concepts/agents)
*   [Middleware](https://docs.ag-ui.com/concepts/middleware)
*   [Messages](https://docs.ag-ui.com/concepts/messages)
*   [Reasoning](https://docs.ag-ui.com/concepts/reasoning)
*   [State Management](https://docs.ag-ui.com/concepts/state)
*   [Interrupts](https://docs.ag-ui.com/concepts/interrupts)
*   [Serialization](https://docs.ag-ui.com/concepts/serialization)
*   [Tools](https://docs.ag-ui.com/concepts/tools)
*   [Capabilities](https://docs.ag-ui.com/concepts/capabilities)
*   [Generative UI](https://docs.ag-ui.com/concepts/generative-ui-specs)

##### Draft Proposals

*   [Overview](https://docs.ag-ui.com/drafts/overview)
*   [Generative User Interfaces](https://docs.ag-ui.com/drafts/generative-ui)
*   [Meta Events](https://docs.ag-ui.com/drafts/meta-events)

##### Tutorials

*   [Developing with Cursor](https://docs.ag-ui.com/tutorials/cursor)
*   [Debugging](https://docs.ag-ui.com/tutorials/debugging)

##### Development

*   [What's New](https://docs.ag-ui.com/development/updates)
*   [Roadmap](https://docs.ag-ui.com/development/roadmap)
*   [Contributing](https://docs.ag-ui.com/development/contributing)
*   [Enterprise](https://docs.ag-ui.com/talk-to-us)

On this page

*   [AG-UI and Generative UI Specs](https://docs.ag-ui.com/concepts/generative-ui-specs#ag-ui-and-generative-ui-specs)

Concepts

# Generative UI

Understanding AG-UI’s relationship with generative UI specifications

> ## Documentation Index
> 
> 
> Fetch the complete documentation index at: [https://docs.ag-ui.com/llms.txt](https://docs.ag-ui.com/llms.txt)
> 
> 
> Use this file to discover all available pages before exploring further.

## [​](https://docs.ag-ui.com/concepts/generative-ui-specs#ag-ui-and-generative-ui-specs)

**AG-UI and Generative UI Specs**

Several recently released specs have enabled agents to return generative UI, increasing the power and flexibility of the Agent<->User conversation.A2UI, MCP-UI, and Open-JSON-UI are all **generative UI specifications.** Generative UIs allow agents to respond to users not only with text but also with dynamic UI components.

| **Specification** | **Origin / Maintainer** | **Purpose** |
| --- | --- | --- |
| **A2UI** | Google | A declarative, LLM-friendly Generative UI spec. JSONL-based and streaming, designed for platform-agnostic rendering. |
| **Open-JSON-UI** | OpenAI | An open standardization of OpenAI’s internal declarative Generative UI schema. |
| **MCP-UI** | Microsoft + Shopify | A fully open, iframe-based Generative UI standard extending MCP for user-facing experiences. |

Despite the naming similarities, **AG-UI is not a generative UI specification** — it’s a **User Interaction protocol** that provides the **bi-directional runtime connection** between the agent and the application.AG-UI natively supports all of the above generative UI specs and allows developers to define **their own custom generative UI standards** as well.

Was this page helpful?

Yes No

[Capabilities Previous](https://docs.ag-ui.com/concepts/capabilities)[Overview Next](https://docs.ag-ui.com/drafts/overview)

⌘I

[github](https://github.com/ag-ui-protocol/ag-ui)

[Powered by This documentation is built and hosted on Mintlify, a developer documentation platform](https://www.mintlify.com/?utm_campaign=poweredBy&utm_medium=referral&utm_source=tawkitai)

Title: CopilotKit/examples/showcases/generative-ui at main · CopilotKit/CopilotKit

URL Source: https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui

Markdown Content:
## 🔮 Generative UI for Agentic Apps

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#-generative-ui-for-agentic-apps)
[![Image 1: Website: Generative UI](https://camo.githubusercontent.com/495bfbf77063e36b972c849025a31f3aab890ee0ee1ad3d8d0c1d4dbcd4f053e/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f576562736974652d47656e6572617469766525323055492d363936336666)](https://www.copilotkit.ai/generative-ui)[![Image 2: Docs: Generative UI](https://camo.githubusercontent.com/e72c73b820ff0844de2b15f5ccb431bb728880f27e625b0390a0d1deb29e3255/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f446f63732d47656e6572617469766525323055492d3639363366663f7374796c653d666c6174)](https://docs.copilotkit.ai/generative-ui)[![Image 3: Protocol: AG-UI](https://camo.githubusercontent.com/9e8856749a91d9ec2f0266403933e78788db3df3f2b5e319b04a61b1ce1eb7d0/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f50726f746f636f6c2d41472d2d55492d3639363366663f7374796c653d666c6174)](https://www.copilotkit.ai/blog/ag-ui-protocol-bridging-agents-to-any-front-end)[![Image 4: Discord](https://camo.githubusercontent.com/91ed4c9856b9b212b6f0ecccf0828a3ebc2cce998f4a6e3619aa7a2c075c333e/68747470733a2f2f696d672e736869656c64732e696f2f646973636f72642f313132323932363035373634313734323431383f6c6f676f3d646973636f7264266c6f676f436f6c6f723d253233464646464646266c6162656c3d446973636f726426636f6c6f723d253233363936336666)](https://discord.gg/6dffbvGU3D)[![Image 5: GitHub stars](https://camo.githubusercontent.com/cd46712fa005aaadc3b85be42ad9e995b54793ec6883f30878fe80afc1dc84a3/68747470733a2f2f696d672e736869656c64732e696f2f6769746875622f73746172732f436f70696c6f744b69742f436f70696c6f744b6974)](https://github.com/CopilotKit/CopilotKit)

Build apps that adapt to your users.

## Generative UI Resources

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#generative-ui-resources)
*   [What is Generative UI?](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#what-is-generative-ui)
*   [The 3 types of Generative UI](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#the-3-types-of-generative-ui)
    *   [Controlled Generative UI (AG-UI)](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#controlled-generative-ui-ag-ui)
    *   [Declarative Generative UI (A2UI + Open-JSON-UI)](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#declarative-generative-ui-a2ui--openjsonui)
    *   [Open-ended Generative UI (MCP Apps)](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#open-ended-generative-ui-mcp-apps)

*   [Generative UI Playground](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#generative-ui-playground)
*   [Blogs](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#blogs)
*   [Videos](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#videos)
*   [Additional Resources](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#additional-resources)
*   [Contributing](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#-contributions-are-welcome)

demo-generative-ui.mp4

This repository walks through how agentic UI protocols (AG-UI, A2UI, MCP Apps) enable Generative UI patterns (Controlled, Declarative, Open-ended) and how to implement them using CopilotKit.

👉 [Generative UI Guide (PDF)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/generative-ui-guide.pdf) - a conceptual overview of Generative UI, focused on trade-offs, UI surfaces and how agentic UI protocols work together.

* * *

## What is Generative UI?

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#what-is-generative-ui)
Generative UI is a pattern in which parts of the user interface are generated, selected, or controlled by an AI agent at runtime rather than being fully predefined by developers.

Instead of only generating text, agents can send UI state, structured UI specs, or interactive UI blocks that the frontend renders in real time. This turns UI from fixed, developer-defined screens into an interface that adapts as the agent works and as context changes.

In the CopilotKit ecosystem, Generative UI is approached in three practical patterns, implemented using different agentic UI protocols and specifications that define how agents communicate UI updates to applications:

*   Controlled Generative UI (high control, low freedom) → AG-UI
*   Declarative Generative UI (shared control) → [A2UI](https://docs.copilotkit.ai/learn/generative-ui/specs/a2ui), [Open-JSON-UI](https://docs.copilotkit.ai/learn/generative-ui/specs/open-json-ui)
*   Open-ended Generative UI (low control, high freedom) → [MCP Apps](https://docs.copilotkit.ai/generative-ui/specs/mcp-apps) / Custom UIs

[AG-UI (Agent-User Interaction Protocol)](https://github.com/ag-ui-protocol/ag-ui) serves as the bidirectional runtime interaction layer beneath these patterns, providing the agent ↔ application connection that enables Generative UI and works uniformly across A2UI, MCP Apps, Open-JSON-UI, and custom UI specifications.

[![Image 6: AG-UI runtime architecture](https://github.com/CopilotKit/CopilotKit/raw/main/examples/showcases/generative-ui/assets/ag-ui-a2ui-architecture.png)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/ag-ui-a2ui-architecture.png)

The rest of this repo walks through each pattern from most constrained to most open-ended and shows how to implement them using CopilotKit.

* * *

## The 3 Types of Generative UI

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#the-3-types-of-generative-ui)
## 1. Controlled Generative UI (AG-UI)

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#1-controlled-generative-ui-ag-ui)
[![Image 7: controlled Generative UI example](https://github.com/CopilotKit/CopilotKit/raw/main/examples/showcases/generative-ui/assets/static-generative-ui-example.png)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/static-generative-ui-example.png)

Controlled Generative UI means you pre-build UI components, and the agent chooses which component to show and passes it the data it needs.

This is the most controlled approach: you own the layout, styling, and interaction patterns, while the agent controls when and which UI appears.

In CopilotKit, this pattern is implemented using the `useFrontendTool` hook, which lets the application register the `get_weather` tool and define how predefined React UI is rendered across each phase of the tool’s execution lifecycle.

// Weather tool - callable tool that displays weather data in a styled card
useFrontendTool({
  name: "get_weather",
  description: "Get current weather information for a location",
  parameters: z.object({ location: z.string().describe("The city or location to get weather for") }),
  handler: async ({ location }) => {
    await new Promise((r) => setTimeout(r, 500));
    return getMockWeather(location);
  },
  render: ({ status, args, result }) => {
    if (status === "inProgress" || status === "executing") {
      return <WeatherLoadingState location={args?.location} />;
    }
    if (status === "complete" && result) {
      const data = JSON.parse(result) as WeatherData;
      return (
        <WeatherCard
          location={data.location}
          temperature={data.temperature}
          conditions={data.conditions}
          humidity={data.humidity}
          windSpeed={data.windSpeed}
        />
      );
    }
    return <></>;
  },
});

*   Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
*   Docs: [docs.copilotkit.ai/generative-ui](https://docs.copilotkit.ai/generative-ui)
*   Specs hub (overview): [docs.copilotkit.ai/learn/generative-ui/specs](https://docs.copilotkit.ai/learn/generative-ui/specs)
*   Ecosystem (how specs + runtime fit): [copilotkit.ai/generative-ui](https://www.copilotkit.ai/generative-ui)

* * *

## 2. Declarative Generative UI (A2UI + Open‑JSON‑UI)

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#2-declarative-generative-ui-a2ui--openjsonui)
[![Image 8: Declarative Generative UI overview](https://github.com/CopilotKit/CopilotKit/raw/main/examples/showcases/generative-ui/assets/declarative-generative-ui-overview.png)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/declarative-generative-ui-overview.png)

Declarative Generative UI sits between controlled and open-ended approaches. Here, the agent returns a structured UI description (cards, lists, forms, widgets) and the frontend renders it.

Two common declarative specifications used for Generative UI are A2UI and Open-JSON-UI.

1.   [A2UI](https://github.com/google/A2UI) → declarative Generative UI spec from Google, described as JSONL-based and streaming, designed for platform-agnostic rendering

2.   [Open‑JSON‑UI](https://docs.copilotkit.ai/learn/generative-ui/specs/open-json-ui) → open standardization of OpenAI’s internal declarative Generative UI schema

Let's first understand the basic flow of how to implement A2UI.

Instead of writing A2UI JSON by hand, you can use the [A2UI Composer](https://a2ui-composer.ag-ui.com/) to generate the spec for you. Copy the output and paste it into your agent’s prompt as a reference template.

[![Image 9: A2UI Composer](https://github.com/CopilotKit/CopilotKit/raw/main/examples/showcases/generative-ui/assets/a2ui-composer.png)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/a2ui-composer.png)

In `prompt_builder.py`, add one A2UI JSONL example so the agent learns the three message envelopes A2UI expects: `surfaceUpdate` (components), `dataModelUpdate` (state), then `beginRendering` (render signal).

UI_EXAMPLES = """
---BEGIN FORM_EXAMPLE---
{"surfaceUpdate":{"surfaceId":"form-surface","components":[ ... ]}}
{"dataModelUpdate":{"surfaceId":"form-surface","path":"/","contents":[ ... ]}}
{"beginRendering":{"surfaceId":"form-surface","root":"form-column","styles":{ ... }}}
---END FORM_EXAMPLE---
"""

Inject `UI_EXAMPLES` into the agent instruction so it can output valid A2UI message lines when a UI is requested.

instruction = AGENT_INSTRUCTION + get_ui_prompt(self.base_url, UI_EXAMPLES)

return LlmAgent(
    model=LiteLlm(model=LITELLM_MODEL),
    name="ui_generator_agent",
    description="Generates dynamic UI via A2UI declarative JSON.",
    instruction=instruction,
    tools=[],
)

Final step: on the frontend, pass `createA2UIMessageRenderer(...)` into `renderActivityMessages` so CopilotKit renders streamed A2UI output as UI and forwards UI actions back to the agent.

import { CopilotKitProvider, CopilotSidebar } from "@copilotkit/react-core/v2";
import { createA2UIMessageRenderer } from "@copilotkit/a2ui-renderer";
import { a2uiTheme } from "../theme";

const A2UIRenderer = createA2UIMessageRenderer({ theme: a2uiTheme });

export function A2UIPage({ children }: { children: React.ReactNode }) {
  return (
    <CopilotKitProvider
      runtimeUrl="/api/copilotkit-a2ui"
      renderActivityMessages={[A2UIRenderer]}   // ← hook in the A2UI renderer
    >
      {children}
      <CopilotSidebar defaultOpen labels={{ modalHeaderTitle: "A2UI Assistant" }} />
    </CopilotKitProvider>
  );
}

The pattern is the same for Open‑JSON‑UI. An agent can respond with an Open‑JSON‑UI payload that describes a UI “card” in JSON and the frontend renders it.

// Example (illustrative): Agent returns a declarative Open-JSON-UI–style specification
{
  type: "open-json-ui",
  spec: {
    components: [
      {
        type: "card",
        properties: {
          title: "Data Visualization",
          content: { ... }
        }
      }
    ]
  }
}

[![Image 10: Open-JSON-UI example](https://github.com/CopilotKit/CopilotKit/raw/main/examples/showcases/generative-ui/assets/open-json-ui-card-example.png)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/open-json-ui-card-example.png)

*   Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
*   Docs: [docs.copilotkit.ai/generative-ui](https://docs.copilotkit.ai/generative-ui)
*   Open‑JSON‑UI Specs (CopilotKit docs): [docs.copilotkit.ai/learn/generative-ui/specs/open-json-ui](https://docs.copilotkit.ai/learn/generative-ui/specs/open-json-ui)
*   A2UI Specs (CopilotKit docs): [docs.copilotkit.ai/learn/generative-ui/specs/a2ui](https://docs.copilotkit.ai/learn/generative-ui/specs/a2ui)
*   Ecosystem (how specs + runtime fit): [copilotkit.ai/generative-ui](https://www.copilotkit.ai/generative-ui)
*   How AG‑UI and A2UI fit together: [copilotkit.ai/ag-ui-and-a2ui](https://www.copilotkit.ai/ag-ui-and-a2ui)

* * *

## 3. Open-ended Generative UI (MCP Apps)

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#3-open-ended-generative-ui-mcp-apps)
[![Image 11: Open-ended Generative UI example](https://github.com/CopilotKit/CopilotKit/raw/main/examples/showcases/generative-ui/assets/open-ended-generative-ui-mcp-apps.png)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/open-ended-generative-ui-mcp-apps.png)

Open-ended Generative UI is when the agent returns a complete UI surface (often HTML/iframes/free-form content), and the frontend mostly serves as a container to display it.

The trade-offs are higher: security/performance concerns when rendering arbitrary content, inconsistent styling, and reduced portability outside the web.

This pattern is commonly used for MCP Apps. In CopilotKit, MCP Apps support is enabled by attaching `MCPAppsMiddleware` to your agent, which allows the runtime to connect to one or more MCP Apps servers.

import { BuiltInAgent } from "@copilotkit/runtime/v2";
import { MCPAppsMiddleware } from "@ag-ui/mcp-apps-middleware";

const agent = new BuiltInAgent({
  model: "openai/gpt-4o",
  prompt: "You are a helpful assistant.",
}).use(
  new MCPAppsMiddleware({
    mcpServers: [
      {
        type: "http",
        url: "http://localhost:3108/mcp",
        serverId: "my-server", // Recommended: stable identifier
      },
    ],
  }),
);

*   Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
*   Docs: [docs.copilotkit.ai/generative-ui](https://docs.copilotkit.ai/generative-ui)
*   MCP Apps spec: [docs.copilotkit.ai/learn/generative-ui/specs/mcp-apps](https://docs.copilotkit.ai/learn/generative-ui/specs/mcp-apps)
*   Practical guide (complete integration flow): [Bring MCP Apps into your OWN app with CopilotKit & AG-UI](https://www.copilotkit.ai/blog/bring-mcp-apps-into-your-own-app-with-copilotkit-and-ag-ui)

* * *

## Generative UI Playground

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#generative-ui-playground)
The Generative UI Playground is a hands-on environment for exploring how all three patterns work in practice and see how agent outputs map to UI in real time.

*   Try it out: [go.copilotkit.ai/gen-ui-demo](https://go.copilotkit.ai/gen-ui-demo)
*   Repo: [go.copilotkit.ai/gen-ui-repo-playground](https://go.copilotkit.ai/gen-ui-repo-playground)

demo-generative-ui.mp4

## Blogs

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#blogs)
*   [Agent Factory: The new era of agentic AI: common use cases and design patterns](https://azure.microsoft.com/en-us/blog/agent-factory-the-new-era-of-agentic-ai-common-use-cases-and-design-patterns/) - By Microsoft Azure
*   [Agentic AI vs AI Agents: A Deep Dive](https://uibakery.io/blog/agentic-ai-vs-ai-agents) - UI Bakery
*   [Introducing Agentic UI Interfaces: A Tactical Executive Guide](https://akfpartners.com/growth-blog/introducing-agentic-ui-interfaces-a-tactical-executive-guide) - AKF Partners
*   [Introducing A2UI: An open project for agent-driven interfaces](https://developers.googleblog.com/introducing-a2ui-an-open-project-for-agent-driven-interfaces/) - Google Developers
*   [From products to systems: The agentic AI shift](https://uxdesign.cc/from-products-to-systems-the-agentic-ai-shift-eaf6a7180c43) - UX Collective
*   [Generative UI: A rich, custom, visual interactive user experience for any prompt](https://research.google/blog/generative-ui-a-rich-custom-visual-interactive-user-experience-for-any-prompt/) - Google Research
*   [The State of Agentic UI: Comparing AG-UI, MCP-UI, and A2A Protocols](https://www.copilotkit.ai/blog/the-state-of-agentic-ui-comparing-ag-ui-mcp-ui-and-a2ui-protocols) - CopilotKit
*   [The Three Types of Generative UI: Controlled, Declarative and Fully Generated](https://www.copilotkit.ai/blog/the-three-kinds-of-generative-ui) - CopilotKit
*   [Generative UI Guide 2025: 15 Best Practices & Examples](https://www.mockplus.com/blog/post/gui-guide) - Mockplus

## Videos

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#videos)
*   [AI Agents Can Now Build Their Own UI in Real Time (Personalized to You)](https://www.youtube.com/watch?v=MD8VQzvMVek)
*   [Agentic AI Explained So Anyone Can Get It!](https://www.youtube.com/watch?v=Jj1-zb38Yfw)
*   [Generative vs Agentic AI: Shaping the Future of AI Collaboration](https://www.youtube.com/watch?v=EDb37y_MhRw)
*   [Generative UI: Specs, Patterns, and the Protocols Behind Them (MCP Apps, A2UI, AG-UI)](https://www.youtube.com/watch?v=Z4aSGCs_O5A)
*   [The Dojo: Agentic Building Blocks for Your UI](https://youtu.be/HlILkXpGYQc)
*   [What is Agentic AI? An Easy Explanation For Everyone](https://www.youtube.com/watch?v=-pqzyvRp3Tc)
*   [What is Agentic AI and How Does it Work?](https://www.youtube.com/watch?v=15_pppse4fY)

## Additional Resources

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#additional-resources)
*   [Agentic Protocols Landscape](https://go.copilotkit.ai/protocols)
*   [Generative UI PDF Download](https://go.copilotkit.ai/generative-ui-pdf-guide)
*   [12 Dos and Donts for Building Agentic Applications](https://go.copilotkit.ai/dos-donts)

* * *

## 🤝 Contributions are welcome

[](https://github.com/CopilotKit/CopilotKit/tree/main/examples/showcases/generative-ui#-contributions-are-welcome)
Contributions welcome: PRs adding examples (Controlled/Declarative/Open‑ended), improving explanations or adding assets.

[Discord](https://discord.com/invite/6dffbvGU3D) for help and discussions. [GitHub](https://github.com/CopilotKit/CopilotKit) to contribute. [@CopilotKit](https://x.com/copilotkit) for updates.

| Project | Preview | Description | Links |
| --- | --- | --- | --- |
| Generative UI Playground | [![Image 12: Generative UI playground preview](https://github.com/CopilotKit/CopilotKit/raw/main/examples/showcases/generative-ui/assets/generative-ui-playground-preview.png)](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/assets/generative-ui-playground-preview.png) | Shows the three Gen UI patterns with runnable, end-to-end examples. | [Repo](https://go.copilotkit.ai/gen-ui-repo-playground) [Demo](https://github.com/CopilotKit/CopilotKit/blob/main/examples/showcases/generative-ui/go.copilotkit.ai/gen-ui-demo) |

Built something? [Open a PR](https://github.com/CopilotKit/CopilotKit/pulls) or [share it in Discord](https://discord.com/invite/6dffbvGU3D).

For AI/LLM agents: [docs.copilotkit.ai/llms.txt](https://docs.copilotkit.ai/llms.txt)

---
title: The Ultimate Guide to MCP
date: 2025-02-22T20:10:00+08:00
tags: ["mcp", "ai", "gpt", "genai"]
series: ["GPT Development"]
featured: false
math: false
---

I haven't updated my AI-related blog for almost a year. Partly because I've been busy with side projects, and partly because although AI technology is rapidly evolving, AI application development hasn't changed much. It's still mostly about the three things I discussed in my [2023 blog](https://guangzhengli.com/blog/en/gpt-embeddings/): Prompts, RAG, and Agents.

However, since Claude (Anthropic) led the release of MCP (Model Context Protocol) in late November last year, AI application development has entered a new era.

There doesn't seem to be much material about MCP explanation and development yet, so I decided to organize my experiences and thoughts into an article to help everyone.

<!--more-->

## Why MCP is a Breakthrough

We know that AI models have developed very rapidly over the past year, from GPT-4 to Claude Sonnet 3.5 to Deepseek R1, with significant improvements in reasoning and reducing hallucinations. There are many new AI applications, but one thing we all feel is that current market AI applications are mostly brand new services, not integrated with our existing systems and services. In other words, the integration of AI models with our existing systems has been developing slowly.

For example, we still can't use an AI application to **simultaneously** search the web, send emails, publish our own blogs, etc. These functions are not difficult to implement individually, but integrating them all into one system becomes challenging.

If you don't have a concrete sense of this yet, consider daily development scenarios. Imagine in an IDE, we could use the IDE's AI to complete the following tasks:

- Ask AI to query existing data in a local database to assist development
- Ask AI to search GitHub Issues to determine if a problem is a known bug
- Use AI to send PR feedback to a colleague's instant messaging software (like Slack) for Code Review
- Use AI to query or even modify current AWS or Azure configurations to complete deployment

These features are becoming reality through MCP. You can check out [Cursor MCP](https://docs.cursor.com/context/model-context-protocol) and [Windsurf MCP](https://www.youtube.com/watch?v=Y_kaQmhGmZk) for more information. Try using Cursor MCP + [browsertools](https://browsertools.agentdesk.ai/installation) plugin to experience automatically retrieving Chrome dev tools console logs in Cursor.

Why has AI integration with existing services been so slow? There are many reasons. On one hand, enterprise data is sensitive, and most enterprises need a long time and process to move forward. On the other hand, from a technical perspective, we lack an open, general, consensus-based protocol standard.

MCP is an open, general, consensus-based protocol standard led by Claude (Anthropic). If you're a developer familiar with AI models, you should be familiar with Anthropic. They released the Claude 3.5 Sonnet model, which is probably still the strongest programming AI model (until they just released 3.7😅).

> I should mention that OpenAI probably had the best opportunity to release this protocol. If OpenAI had promoted a protocol when they first released GPT, everyone would likely have accepted it. But OpenAI became CloseAI, only releasing a closed GPTs system. This kind of standard protocol that needs leadership and consensus is generally difficult for communities to form spontaneously - it's usually led by industry giants.

After Claude released MCP, the official Claude Desktop opened MCP functionality and promoted the open source organization [Model Context Protocol](https://github.com/modelcontextprotocol), with participation from different companies and communities. Here are some examples of MCP servers released by different organizations:

### Official MCP Integrations:

-   **[Git](https://github.com/modelcontextprotocol/servers/blob/main/src/git)** - Git reading, operations, and search.
-   **[GitHub](https://github.com/modelcontextprotocol/servers/blob/main/src/github)** - Repo management, file operations, and GitHub API integration.
-   **[Google Maps](https://github.com/modelcontextprotocol/servers/blob/main/src/google-maps)** - Google Maps integration for location information.
-   **[PostgreSQL](https://github.com/modelcontextprotocol/servers/blob/main/src/postgres)** - Read-only database queries.
-   **[Slack](https://github.com/modelcontextprotocol/servers/blob/main/src/slack)** - Slack message sending and querying.

### 🎖️ Examples of Third-Party Platforms Officially Supporting MCP

MCP servers built by third-party platforms:

-   **[Grafana](https://github.com/grafana/mcp-grafana)** - Search query data in Grafana.
-   **[JetBrains](https://github.com/JetBrains/mcp-jetbrains)** – JetBrains IDEs.
-   **[Stripe](https://github.com/stripe/agent-toolkit)** - Interact with Stripe API.

### 🌎 Community MCP Servers

Here are some MCP servers developed and maintained by the open source community:

-   **[AWS](https://github.com/rishikavikondala/mcp-server-aws)** - Operate AWS resources with LLM.
-   **[Atlassian](https://github.com/sooperset/mcp-atlassian)** - Interact with Confluence and Jira, including searching/querying Confluence spaces/pages, accessing Jira Issues and projects.
-   **[Google Calendar](https://github.com/v-3/google-calendar)** - Google Calendar integration, scheduling, finding time, and adding/removing events.
-   **[Kubernetes](https://github.com/Flux159/mcp-server-kubernetes)** - Connect to Kubernetes clusters and manage pods, deployments, and services.
-   **[X (Twitter)](https://github.com/EnesCinr/twitter-mcp)** - Interact with Twitter API. Post tweets and search tweets through queries.
-   **[YouTube](https://github.com/ZubeidHendricks/youtube-mcp-server)** - YouTube API integration, video management, short video creation, etc.

## Why MCP?

You might be wondering: when OpenAI released GPT function calling in 2023, couldn't it achieve similar functionality? Didn't the AI Agent we introduced in previous blogs integrate different services? Why do we now have MCP?

What are the differences between function calling, AI Agent, and MCP?

### Function Calling

- Function Calling refers to the mechanism where AI models automatically execute functions based on context.
- Function Calling acts as a bridge between AI models and external systems. Different models have different Function Calling implementations, and code integration methods also differ. They are defined and implemented by different AI model platforms.

### Model Context Protocol (MCP)

- MCP is a standard protocol, like the Type C protocol for electronic devices (which can both charge and transfer data), enabling AI models to seamlessly interact with different APIs and data sources.
- MCP aims to replace fragmented Agent code integration, making AI systems more reliable and effective. By establishing common standards, service providers can launch AI capabilities for their own services based on the protocol, enabling developers to build more powerful AI applications faster. Developers also don't need to reinvent the wheel - they can build a strong AI Agent ecosystem through open source projects.
- MCP can maintain context between different applications/services, enhancing overall autonomous task execution capabilities.

### AI Agent

- An AI Agent is an intelligent system that can run autonomously to achieve specific goals. Traditional AI chat only provides suggestions or requires manual task execution, while AI Agents can analyze specific situations, make decisions, and take actions on their own.
- AI Agents can use functionality descriptions provided by MCP to understand more context and automatically execute tasks across various platforms/services.

### Differences

Simply put, MCP tells the AI Agent about the capabilities of different services and platforms. The AI Agent, based on context and model reasoning, determines whether to call a service, then uses Function Calling to execute functions. These functions are communicated to Function Calling through MCP, and the entire process is completed through specific code provided by the MCP protocol.

The main benefits of MCP for the community ecosystem are:

- Open standards for service providers, allowing them to open their APIs and partial capabilities for MCP.
- No need to reinvent the wheel - developers can use existing open source MCP services to enhance their Agents.

### Reflection

Why has Claude's MCP been widely accepted? During the past year, I've participated in the development of several small AI projects. In the development process, integrating AI models with existing systems or third-party systems is indeed troublesome.

Although there are some frameworks that support Agent development, such as [LangChain Tools](https://www.langchain.com/), [LlamaIndex](https://docs.llamaindex.ai/en/stable/), or [Vercel AI SDK](https://sdk.vercel.ai/docs/introduction).

LangChain and LlamaIndex, although open source projects, have developed quite chaotically. First, their code abstraction level is too high. They promote enabling developers to complete certain AI functions with just a few lines of code, which works well in the demo stage, but in actual development, once business starts to get complex, poor code design leads to a terrible programming experience. Also, these projects are too focused on commercialization, ignoring overall ecosystem building.

As for Vercel AI SDK, although I personally think its code abstraction is better, it's only good at front-end UI integration and packaging some AI functions. The biggest problem is that it's too deeply bound to Nextjs, with insufficient support for other frameworks and languages.

So Claude promoting MCP can be said to be good timing. First, Claude Sonnet 3.5 has a high standing among developers, and MCP is an open standard, so many companies and communities are willing to participate. I hope Claude continues to maintain a good open ecosystem.

## How MCP Works

Let's introduce how MCP works. First, let's look at the [official MCP architecture diagram](https://modelcontextprotocol.io/introduction).

![MCP Architecture Diagram](https://storage.guangzhengli.com/images/MCP.png)

It's divided into five parts:

- MCP Hosts: Applications where LLM initiates connections, like Cursor, Claude Desktop, [Cline](https://github.com/cline/cline).
- MCP Clients: Clients maintain 1:1 connections with Servers within Host applications.
- MCP Servers: Provide context, tools, and prompts to Clients through a standardized protocol.
- Local Data Sources: Local files, databases, and APIs.
- Remote Services: External files, databases, and APIs.

The core of the MCP protocol is the Server. Host and Client should be familiar to those who understand computer networks, but how do we understand Server?

Looking at Cursor's AI Agent development process, we can see that the entire AI automation process evolves from Chat to Composer and then to a complete AI Agent.

AI Chat only provides suggestions. Converting AI responses into actions and final results relies entirely on humans, such as manual copy-pasting or making certain modifications.

AI Composer can automatically modify code, but requires human participation and confirmation, and cannot perform operations other than code modification.

AI Agent is a completely automated program. In the future, it could automatically read images from Figma, automatically generate code, automatically read logs, automatically debug code, and automatically push code to GitHub.

MCP Server exists to enable AI Agent automation. It's a middle layer that tells the AI Agent what services, APIs, and data sources exist. The AI Agent can decide whether to call a service based on the information provided by the Server, then execute functions through Function Calling.

### How MCP Server Works

Let's look at a simple example. Suppose we want the AI Agent to automatically search for relevant GitHub Repositories, then search for Issues, then determine if it's a known bug, and finally decide whether to submit a new Issue.

We need to create a GitHub MCP Server that provides three capabilities: finding Repositories, searching Issues, and creating Issues.

Let's look at the code directly:

```ts
const server = new Server(
  {
    name: "github-mcp-server",
    version: VERSION,
  },
  {
    capabilities: {
      tools: {},
    },
  }
);

server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "search_repositories",
        description: "Search for GitHub repositories",
        inputSchema: zodToJsonSchema(repository.SearchRepositoriesSchema),
      },
      {
        name: "create_issue",
        description: "Create a new issue in a GitHub repository",
        inputSchema: zodToJsonSchema(issues.CreateIssueSchema),
      },
      {
        name: "search_issues",
        description: "Search for issues and pull requests across GitHub repositories",
        inputSchema: zodToJsonSchema(search.SearchIssuesSchema),
      }
    ],
  };
});

server.setRequestHandler(CallToolRequestSchema, async (request) => {
  try {
    if (!request.params.arguments) {
      throw new Error("Arguments are required");
    }

    switch (request.params.name) {
      case "search_repositories": {
        const args = repository.SearchRepositoriesSchema.parse(request.params.arguments);
        const results = await repository.searchRepositories(
          args.query,
          args.page,
          args.perPage
        );
        return {
          content: [{ type: "text", text: JSON.stringify(results, null, 2) }],
        };
      }

      case "create_issue": {
        const args = issues.CreateIssueSchema.parse(request.params.arguments);
        const { owner, repo, ...options } = args;
        const issue = await issues.createIssue(owner, repo, options);
        return {
          content: [{ type: "text", text: JSON.stringify(issue, null, 2) }],
        };
      }

      case "search_issues": {
        const args = search.SearchIssuesSchema.parse(request.params.arguments);
        const results = await search.searchIssues(args);
        return {
          content: [{ type: "text", text: JSON.stringify(results, null, 2) }],
        };
      }

      default:
        throw new Error(`Unknown tool: ${request.params.name}`);
    }
  } catch (error) {}
});

async function runServer() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("GitHub MCP Server running on stdio");
}

runServer().catch((error) => {
  console.error("Fatal error in main():", error);
  process.exit(1);
});
```

In the code above, we use `server.setRequestHandler` to tell the Client what capabilities we provide. The `description` field describes the purpose of the capability, and `inputSchema` describes the input parameters needed to complete this capability.

Now let's look at the specific implementation code:

```ts
export const SearchOptions = z.object({
  q: z.string(),
  order: z.enum(["asc", "desc"]).optional(),
  page: z.number().min(1).optional(),
  per_page: z.number().min(1).max(100).optional(),
});

export const SearchIssuesOptions = SearchOptions.extend({
  sort: z.enum([
    "comments",
    ...
  ]).optional(),
});

export async function searchUsers(params: z.infer<typeof SearchUsersSchema>) {
  return githubRequest(buildUrl("https://api.github.com/search/users", params));
}

export const SearchRepositoriesSchema = z.object({
  query: z.string().describe("Search query (see GitHub search syntax)"),
  page: z.number().optional().describe("Page number for pagination (default: 1)"),
  perPage: z.number().optional().describe("Number of results per page (default: 30, max: 100)"),
});

export async function searchRepositories(
  query: string,
  page: number = 1,
  perPage: number = 30
) {
  const url = new URL("https://api.github.com/search/repositories");
  url.searchParams.append("q", query);
  url.searchParams.append("page", page.toString());
  url.searchParams.append("per_page", perPage.toString());

  const response = await githubRequest(url.toString());
  return GitHubSearchResponseSchema.parse(response);
}
```

We can clearly see that our final implementation is through the `https://api.github.com` API to interact with Github. We use the `githubRequest` function to call GitHub's API and return results.

Before calling Github's official API, MCP's main work is to describe what capabilities the Server provides (to LLM), what parameters are needed (what the parameters specifically do), and what the final result is.

So the MCP Server is not a novel or profound thing; it's just a protocol with consensus.

If we want to implement a more powerful AI Agent, for example, we want the AI Agent to automatically search for relevant GitHub Repositories based on local error logs, then search for Issues, and finally send the results to Slack.

We might need to create three different MCP Servers: a Local Log Server to query local logs; a GitHub Server to search for Issues; and a Slack Server to send messages.

After the user inputs the instruction "I need to query local error logs and send relevant Issues to Slack," the AI Agent determines which MCP Servers to call, decides the calling order, and ultimately decides whether to call the next Server based on the return results of different MCP Servers, thus completing the entire task.

## How to Use MCP

If you haven't tried using MCP yet, we can consider using Cursor (I've only tried Cursor), Claude Desktop, or Cline to experience it.

Of course, we don't need to develop MCP Servers ourselves. The benefit of MCP is that it's universal and standard, so developers don't need to reinvent the wheel (though for learning purposes, you can).

First, I recommend some official organization Servers: [Official MCP Server List](https://github.com/modelcontextprotocol/servers).

Currently, community MCP Servers are quite chaotic, with many lacking tutorials and documentation, and many code functionalities have issues. We can try some examples from [Cursor Directory](https://cursor.directory). I won't elaborate on specific configurations and practical applications - please refer to the official documentation.

## Some MCP Resources

Here are some MCP resources I personally recommend:

### Official MCP Resources

[Official open source organization Model Context Protocol](https://github.com/modelcontextprotocol).
[Official documentation modelcontextprotocol](https://modelcontextprotocol.io/introduction).
[Official MCP Server List](https://github.com/modelcontextprotocol/servers)
[Claude Blog](https://www.anthropic.com/news/model-context-protocol)

### Community MCP Server Lists

- [Cursor Directory](https://cursor.directory)
- [Pulsemcp](https://www.pulsemcp.com/)
- [Glama MCP Servers](https://glama.ai/mcp/servers)

## Final Thoughts

This article may be reproduced, but please cite the source. It will be published simultaneously on X/Twitter and Xiaohongshu. Welcome to follow.

## References

- https://guangzhengli.com/blog/zh/gpt-embeddings/
- https://docs.cursor.com/context/model-context-protocol
- https://www.youtube.com/watch?v=Y_kaQmhGmZk
- https://browsertools.agentdesk.ai/installation
- https://github.com/modelcontextprotocol
- https://github.com/grafana/mcp-grafana
- https://github.com/JetBrains/mcp-jetbrains
- https://github.com/stripe/agent-toolkit
- https://github.com/rishikavikondala/mcp-server-aws
- https://github.com/sooperset/mcp-atlassian
- https://github.com/v-3/google-calendar
- https://github.com/Flux159/mcp-server-kubernetes
- https://github.com/EnesCinr/twitter-mcp
- https://github.com/ZubeidHendricks/youtube-mcp-server
- https://www.langchain.com/
- https://docs.llamaindex.ai/en/stable/
- https://sdk.vercel.ai/docs/introduction
- https://modelcontextprotocol.io/introduction
- https://github.com/cline/cline
- https://github.com/modelcontextprotocol/servers
- https://www.anthropic.com/news/model-context-protocol
- https://cursor.directory
- https://www.pulsemcp.com/
- https://glama.ai/mcp/servers

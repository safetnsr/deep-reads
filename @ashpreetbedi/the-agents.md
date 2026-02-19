# The Agents

**Author:** @ashpreetbedi  
**Date:** 2026-01-29T18:26:18.000Z  
**URL:** https://x.com/ashpreetbedi/article/2016940984366739882  
**Images:** 2  

---

Instead of a hello world tutorial, let me show you how to build a live multi-agent system. We'll run it locally with docker, then deploy to 

[@Railway](https://x.com/@Railway)

.

This is a production system that includes:

- Learning - Agents remember and improve over time
- Persistence - State, sessions, and memory backed by PostgreSQL
- Agentic RAG - Knowledge retrieval that knows when and how to search
- MCP Tools - Connect to external services via Model Context Protocol
- Monitoring - Full visibility via the AgentOS control plane

You'll also learn how to extend it with your own agents.

5 minute read. Running locally in 5. Deployed to production in <20.

## The Agents

We'll build three agents, each demonstrating a different pattern:

- Pal: AI-powered second brain. Captures notes, bookmarks, people, meetings. Researches the web. Learns over time.
- Knowledge Agent: Answers questions from a knowledge base.
- MCP Agent - Connects to external services via Model Context Protocol.

Each agent can be extended to fit your needs.

# Run Locally (5 minutes)

Prerequisites:

- Install 

  [Docker](https://www.docker.com/products/docker-desktop)
- Get an 

  [OpenAI API key](https://platform.openai.com/api-keys)

## Setup

Clone the repo and export your OpenAI API key:

shell

```
git clone \
    https://github.com/agno-agi/agentos-railway-template.git \
    agentos-railway

cd agentos-railway

export OPENAI_API_KEY="sk-***"
```

Start the application (API + Database):

shell

```
docker compose up -d --build
```

That's it. Your system is running. Here's how it looks:

[![](https://pbs.twimg.com/amplify_video_thumb/2016925085177618432/img/O9bOxwnma4lbTVr9.jpg)](blob:https://x.com/d69b61eb-6d95-4978-a600-12725b662dec)

0:01 / 0:35

## Connect to the UI

1. Open 

   [os.agno.com](https://os.agno.com)
2. Click Add OS → Local
3. Enter `http://localhost:8000` as the URL

Now chat with Pal:

plaintext

```
- Note: decided to use Postgres for the new project - better JSON support

- Research event sourcing patterns and save the key findings

- What do I know about event sourcing?
```

# Deploy to Production (10 minutes)

I've made it easy to deploy to Railway - just login and run a script.

Prerequisites

- Install the 

  [Railway CLI](https://docs.railway.com/guides/cli#installing-the-cli)

## Deploy

Login to Railway and run the deploy script:

shell

```
railway login

./scripts/railway_up.sh
```

The script provisions PostgreSQL, configures environment variables, and deploys your system. Give it a few minutes for the services to spin up.

## Connect to the UI

1. Open 

   [os.agno.com](https://os.agno.com)
2. Click Add OS → Live
3. Enter your Railway domain

You now have a production multi-agent system. Watch it go live in ~2 mins:

[![](https://pbs.twimg.com/amplify_video_thumb/2016926017844137984/img/B8cGUwpigiA9nY6X.jpg)](blob:https://x.com/65e0d0d1-83e3-42db-a24d-947a9d199404)

0:01 / 1:15

# What's Included

- Pal (Personal Agent that Learns)

Your AI-powered second brain. Captures notes, bookmarks, people, meetings. Researches the web and saves findings. Learns from errors so it doesn't repeat them. I wrote more about Pal here:

[![](https://pbs.twimg.com/profile_images/2024047696827273217/vlW-RvPT_normal.jpg)](/ashpreetbedi)

Ashpreet Bedi

![](https://pbs.twimg.com/profile_images/1984361332624306176/KaNuKvU4_bigger.jpg)

@ashpreetbedi

·

[Jan 29](/ashpreetbedi/status/2016702682925334818)

![Article cover image](https://pbs.twimg.com/media/G_zELdHbkAA2Coe?format=png&name=small)

Article

Building Pal: Personal Agent that Learns

My information is scattered everywhere.
Notes in text files. Bookmarks across three different browsers. People I meet (six or seven a day) living in my head until I forget them. Research I did last...

25

53

656

[59K](/ashpreetbedi/status/2016702682925334818/analytics)

- Knowledge Agent (Agentic RAG)

Store any type of docs in a vector store, chat with it using Agentic RAG.

python

```
knowledge_agent = Agent(
    model=OpenAIResponses(id="gpt-5.2"),
    knowledge=knowledge,
    search_knowledge=True,
)
```

- MCP Agent (MCP Tools)

Connects to external tools via the Model Context Protocol. Point it at any MCP server and it gets access to those tools.

python

```
mcp_agent = Agent(
    model=OpenAIResponses(id="gpt-5.2"),
    tools=[MCPTools(url="https://docs.agno.com/mcp")],
)
```

# Create Your Own Agent

Now let's add a custom agent to the system. We'll build a research agent that uses the 

[@ExaAILabs](https://x.com/@ExaAILabs)

 MCP server.

- Create `agents/research\_agent.py`:

python

```
from agno.agent import Agent
from agno.models.openai import OpenAIResponses
from agno.tools.mcp import MCPTools

from db import get_postgres_db

# Exa MCP for research
EXA_MCP_URL = (
    f"https://mcp.exa.ai/mcp?tools="
    "web_search_exa,company_research_exa,people_search_exa"
)

research_agent = Agent(
    id="research-agent",
    name="Research Agent",
    model=OpenAIResponses(id="gpt-5.2"),
    db=get_postgres_db(),
    tools=[MCPTools(url=EXA_MCP_URL)],
    instructions="""\
You are a research agent. You help users find information about:
- Companies and startups
- People and their backgrounds
- Topics and trends

Be thorough but concise. Cite your sources.
""",
)
```

- Register it in `app/main.py`:

python

```
from agents.research_agent import research_agent

agent_os = AgentOS(
    agents=[pal, knowledge_agent, mcp_agent, research_agent],
)
```

- Your agent is now part of the system. Chat with it:

[![](https://pbs.twimg.com/amplify_video_thumb/2016930342989434880/img/JkRVnUmz1MyQsWoa.jpg)](blob:https://x.com/e520592b-6814-4f1f-9056-da9454df04aa)

0:01 / 0:38

If the Agent doesn't show up:

- Press refresh on the UI (top right corner)
- Restart containers (it should auto-reload by default).

shell

```
docker compose restart
```

# Wrapping Up

You now have a live multi-agent system with:

- Learning - Agents that remember and improve over time
- Persistence - PostgreSQL for storing agent sessions, state and memory
- Research - Web search, company lookup, people search via Exa
- Monitoring - Full visibility via the AgentOS control plane
- Extensibility - Add agents, tools, and integrations as needed

# What's next

- Build more agents - Add specialized 

  [agents](https://docs.agno.com/agents/overview)

   for your use case.
- Add tools - Extend your agents with 

  [100+ toolkits](https://docs.agno.com/tools/toolkits)

  .
- Go multi-agent - Create multi-agent 

  [teams](https://docs.agno.com/teams/overview)

   and 

  [workflows](https://docs.agno.com/workflows/overview)

  .
- Go multi-channel - Expose your agents via Slack, Discord, WhatsApp.
- Build an AI product - 2-person startups to Fortune 500 companies use the AgentOS as the foundation for their AI products.

The system is yours. You have a head start - make it count.

# Learn More

- [GitHub repo](https://github.com/agno-agi/agentos-railway-template)
- [Agno documentation](https://docs.agno.com)
- [Built with Agno. Give it a](https://github.com/agno-agi/agno)

  ⭐️

---

## Images

![Image](https://pbs.twimg.com/media/G_2JSQjXcAAkSeR?format=png&name=900x900)

![Article cover image](https://pbs.twimg.com/media/G_zELdHbkAA2Coe?format=png&name=small)


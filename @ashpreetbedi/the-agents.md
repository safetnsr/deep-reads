# The Agents

**Author:** @ashpreetbedi  
**Date:** 2026-01-29T18:26:18.000Z  
**URL:** https://x.com/ashpreetbedi/article/2016940984366739882  
**Images:** 2  

---

The Agents
We'll build three agents, each demonstrating a different pattern:
Pal: AI-powered second brain. Captures notes, bookmarks, people, meetings. Researches the web. Learns over time.
Knowledge Agent: Answers questions from a knowledge base.
MCP Agent - Connects to external services via Model Context Protocol.
Each agent can be extended to fit your needs.
Run Locally (5 minutes)
Prerequisites:
Install Docker
Get an OpenAI API key
Setup
Clone the repo and export your OpenAI API key:
shell
git clone \
https://github.com/agno-agi/agentos-railway-template.git \
agentos-railway

cd agentos-railway

export OPENAI_API_KEY="sk-***"
Start the application (API + Database):
shell
docker compose up -d --build
That's it. Your system is running. Here's how it looks:
0:01 / 0:35
Connect to the UI
Open os.agno.com
Click Add OS → Local
Enter `http://localhost:8000` as the URL
Now chat with Pal:
plaintext
- Note: decided to use Postgres for the new project - better JSON support

- Research event sourcing patterns and save the key findings

- What do I know about event sourcing?
Deploy to Production (10 minutes)
I've made it easy to deploy to Railway - just login and run a script.
Prerequisites
Install the Railway CLI
Deploy
Login to Railway and run the deploy script:
shell
railway login

./scripts/railway_up.sh
The script provisions PostgreSQL, configures environment variables, and deploys your system. Give it a few minutes for the services to spin up.
Connect to the UI
Open os.agno.com
Click Add OS → Live
Enter your Railway domain
You now have a production multi-agent system. Watch it go live in ~2 mins:
0:01 / 1:15
What's Included
Pal (Personal Agent that Learns)
Your AI-powered second brain. Captures notes, bookmarks, people, meetings. Researches the web and saves findings. Learns from errors so it doesn't repeat them. I wrote more about Pal here:
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G_2JSQjXcAAkSeR?format=png&name=900x900)

![Article cover image](https://pbs.twimg.com/media/G_zELdHbkAA2Coe?format=png&name=small)


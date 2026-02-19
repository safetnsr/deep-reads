# What it does

**Author:** @ashpreetbedi  
**Date:** 2026-01-29T02:39:22.000Z  
**URL:** https://x.com/ashpreetbedi/article/2016702682925334818  
**Images:** 2  

---

What it does

Pal is a personal agent that acts like a second brain.

It captures notes, bookmarks, people, and meetings. It can research the web and save findings (thanks Exa for the free MCP). Then I can ask, “what do I know about X?” and it connects the dots.
## Capture

Here's me sharing some notes with it:

```
Note: decided to use Postgres for the new project - better JSON support

Bookmark https://docs.agno.com/first-multi-agent-system - great intro
```

## Research

Here's me asking it to do some research:

```
Research event sourcing patterns and save the key findings
```

## Retrieve

Of course let's ask it to retrieve and pull information:

```
What do I know about event sourcing?
```
What makes it different

Pal learns. It’s not just storage and retrieval. It improves with every interaction.

Pal has two storage systems (by design).
- DuckDB stores your actual stuff: notes, bookmarks, people, meetings, projects.
- A separate Learning System stores reusable knowledge: table schemas, research findings, error-fix patterns.

Pal creates tables on the fly. It spins up DuckDB tables for notes, bookmarks, people, meetings, research, and whatever else you end up needing. Nothing is pre-defined. It builds on the fly (lol good luck).

Pal learns continuously. It learns:
- about you (your preferences, projects, and context)
- from its errors (so it does not repeat the same mistakes)
- from the web (when you ask it to research something)
- from the people you meet (names, context, and follow-ups)
Pal compounds. The biggest advantage is that it improves its future behavior based on what worked and what broke. For example, if loading notes fails because a table is missing a primary key, it learns to create the notes table with a primary key by default next time.
I’ve only been using it for 6 hours, but the goal is that day 30 Pal is noticeably better than day 1 Pal.
## Try it

### Run locally (about 2 minutes)

Clone the repo, copy env file and add your OpenAI API key:

```shell
git clone \
https://github.com/agno-agi/agentos-railway-template.git \
agentos-railway

cd agentos-railway

cp example.env .env  # Add your OPENAI_API_KEY
```

Start the application (API + Database):

```shell
docker compose up -d --build
```

Connect to AgentOS and start chatting:

Open os.agno.com

Click "Add OS" → "Local"
Enter `http://localhost:8000` in the URL

### Deploy to production (Railway)

Pretty easy to deploy this to railway, just login and use the script i've provided:

```shell
railway login
./scripts/railway_up.sh
```

Give it a few minutes for services to spin up.

Connect to control plane:

Open os.agno.com

Click "Add OS" → "Live"
Enter your Railway domain

Built with Agno. Research powered by @ExaAILabs free MCP server - web search, company lookup, and people search out of the box. Thanks Exa 🙏
→ GitHub repo with full code

What’s next

Not sure. Here’s what I’ll try next:
Add Slack integration

Add Gmail integration

Pull context from Google Calendar

Sync projects and tasks with Linear

Add WhatsApp

The goal is simple: dump information in whatever way is easiest, then retrieve it in whatever way is fastest.
⚠️ This is a work in progress ⚠️
Please don't expect Pal to be perfect. I do not know if this is a weekend experiment or something i'll be using in 2 weeks. I just wanted to share what I am building.

What I’m curious about:
What would you want a second brain to remember?

What integrations matter most? Slack, email, calendar, WhatsApp?

How would you want to interact with it? Chat, voice, daily digests, reminders?

Let me know. DMs open.

Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G_zELdHbkAA2Coe?format=png&name=900x900)

![](https://pbs.twimg.com/amplify_video_thumb/2016694164050890754/img/3Bjk1_HdHta9ESCB.jpg)


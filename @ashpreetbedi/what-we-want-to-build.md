# What we want to build

**Author:** @ashpreetbedi  
**Date:** 2026-01-26T23:52:43.000Z  
**URL:** https://x.com/ashpreetbedi/article/2015935966268018823  
**Images:** 1  

---

What we want to build

We want agents that:
Continue conversations across sessions

Remember user preferences, history, and context

Get better by learning from every interaction

Can be debugged when they fail

Give us structured data to evaluate and improve

None of this is possible without persistent state.

Here's the thing: even Claude (my fav) doesn't "remember" anything. When it recalls my name or references a past conversation, it's reading from stored memories injected into its context or searching for them at runtime. The continuity we experience isn't a property of the LLM. It's beautiful engineering powered by a database. Our agents should work the same way.

We can ignore this. Most tutorials do. But then our agents forget everything between requests, can't learn from mistakes, and give us no way to debug failures or improve performance.

Or we can treat state as a foundational primitive and unlock capabilities that stateless wrappers can't touch.

What owning our database unlocks

Full context control: Decide what goes into the context window. Read previous messages. Include the last 3 turns, or 10, or just a summary. Context is our moat and we control it.

Smarter context management: Summarize long conversations. Compress verbose tool outputs. Prune irrelevant history. Enrich with retrieved knowledge. This is what good agent engineering actually is: delivering the right context for the right response.

Zero vendor dependency: No egress fees. No retention costs. No "we're deprecating this API" emails. Query our own data with SQL. Build a quick dashboard, or plug into any observability tool. Our data. Our choice.

Evaluation datasets: Pull examples, build few-shot prompts, run multi-turn simulations. Flag low-quality responses for review. All without asking a vendor for an export.

Self-learning loops: Track which responses users edited. Which tool calls failed. Which sessions ended in frustration. Feed this back into the system automatically.

This is how good software is built. Agents are no different.

## The implementation

```python
from agno.agent import Agent
from agno.db.sqlite import SqliteDb

agent = Agent(
    db=SqliteDb(db_file="agent.db"),
    add_history_to_context=True,
    num_history_runs=3,
)
```

Three lines of code. The agent now persists sessions, includes conversation history, and gives us full control over our data.

Need more control? It's just Python:

```python
# Access your data directly
history = agent.get_chat_history(session_id="session_123")
messages = agent.get_session_messages(session_id="session_123")
session = agent.get_session(session_id="session_123")

# Long conversations? Summarize them automatically
agent = Agent(
    db=SqliteDb(db_file="agent.db"),
    enable_session_summaries=True,   # Compress old context
    store_tool_messages=False,       # Skip the bloat
)
```

No API calls. No export requests. No waiting for a vendor to build the feature you need. Just SQL.

And this isn't SQLite-specific. Swap one import:

```python
from agno.db.mongodb import MongoDb
from agno.db.postgres import PostgresDb
# ...13+ databases supported

db = PostgresDb(db_url="postgresql://user:pass@localhost:5432/mydb")
agent = Agent(db=db)
```

SQLite for testing. Postgres for production. Our infrastructure. Our data.

The case against third-party state

The industry has normalized storing our data in someone else's database.

The Responses API gives us a `previous_response_id`. Managed memory services hold our context. It's convenient but there are trade-offs worth considering.

We're paying twice: once for the API call, again for storage and egress. We're dependent on their schema, their export tools, their roadmap. When we need a feature, we file a ticket and wait. When they have an outage, so do we.

And here's the thing: we still need a database. That `response_id`? We're storing it somewhere. The user session? That's in our system. We've just split our state across two places and added a network hop.

The alternative is simple: own our database. Query it directly.
AI engineering is just... software engineering

We keep discovering that AI engineering is just... software engineering.

Agents need databases for the same reason web apps need databases: stateless compute requires stateful storage. The patterns aren't new. We just forgot how to use them.

Agno is open-source infrastructure for agents. Database support is built in.

GitHub

Get started

Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G_oGGd7WQAAFr1i?format=png&name=900x900)


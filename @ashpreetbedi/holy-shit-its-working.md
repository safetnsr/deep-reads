# Holy Shit, It's Working!

**Author:** @ashpreetbedi  
**Date:** 2026-01-07T18:01:21.000Z  
**URL:** https://x.com/ashpreetbedi/article/2008962172177526995  
**Images:** 2  

---

Holy Shit, It's Working!

I've been building the learning machine for a week now (first commit: dec 31)

Running conversations. Fixing bugs. The usual. Then this happens...

**Day 1:** Alice on the infrastructure team asks the agent:

> "Our AWS egress costs are killing us -- we're paying way more than we expected. Help me figure out how to reduce them."

The agent helps. Behind the scenes, it picks up on something: *egress costs can dominate cloud bills unexpectedly.*

**Day 4:** Her teammate Bob, different session, evaluating observability tools:

> "I'm looking at DataDog vs Grafana Cloud for our monitoring stack. What should I consider?"

The agent responds -- and flags that DataDog's architecture could increase egress costs and that the team is looking for ways to reduce them.

Unprompted.

Bob never mentioned egress. Never talked to Alice about it. Had no idea this was even a concern. But the agent knew. Because it learned from Alice.

I sat there for a minute. This is the thing. Learning that transfers across people, across sessions, across time.

Powered by Postgres. No fine-tuning. No RLHF infrastructure. Just a database and some prompt engineering. GPU Poor Learning. It works.

## The Experiment

I started with seven cookbooks. Four learning stores. Different modes. Each one tests a different type of learning.

- `1_user_profile_background.py` — Auto-extract user info
- `2_user_profile_agentic.py` — Agent decides what to remember
- `3_session_context_summary.py` — Lightweight session summaries
- `4_session_context_planning.py` — Track goal → plan → progress
- `5_learned_knowledge.py` — Reusable insights across users
- `6_entity_memory_background.py` — Auto-extract company/people facts
- `7_entity_memory_agentic.py` — Agent manages entity knowledge

Let me show you the two that matter most.

## User Profile: Remembering Who You Are

This is what Claude and ChatGPT do. The baseline.

- **What it captures:** A structured UserProfile (name, work) and an unstructured list of memories (preferences, communication style, list of enemies).
- **Scope:** Per user.
- **Modes:** Background and Agentic.

### Background Mode: It Just Happens

```python
from agno.agent import Agent
from agno.db.postgres import PostgresDb
from agno.learn import LearningMachine, LearningMode, UserProfileConfig
from agno.models.openai import OpenAIResponses

db = PostgresDb(db_url="postgresql+psycopg://ai:ai@localhost:5532/ai")

agent = Agent(
    model=OpenAIResponses(id="gpt-5.2"),
    db=db,
    learning=LearningMachine(
        user_profile=UserProfileConfig(
            mode=LearningMode.BACKGROUND,
        ),
    ),
    markdown=True,
)
```

**First conversation:**

User: "Hi! I'm Alice, I work at Anthropic as a research scientist. I prefer concise responses without too much explanation."

The agent responds normally. Behind the scenes, it extracts: name (Alice), company (Anthropic), role (research scientist), preference (concise responses). The agent is learning about Alice.

**Next conversation, different session:**

User: "What's a good Python library for async HTTP requests?"

The agent responds concisely -- because it knows that's what Alice prefers.

No "please remember this". No magic words. It just works.

Background mode is invisible. The user never sees the extraction happening. Good or bad? not sure yet!

### Agentic Mode: Agent Decides

```python
agent = Agent(
    model=OpenAIResponses(id="gpt-5.2"),
    db=db,
    instructions="Remember important information about users using your memory tools.",
    learning=LearningMachine(
        user_profile=UserProfileConfig(
            mode=LearningMode.AGENTIC,
        ),
    ),
    markdown=True,
)
```

Now the agent has memory tools. It decides what's worth remembering, and you can see the tool calls.

User: "Hi! I'm Bob, a backend engineer at Stripe. I specialize in distributed systems and prefer Rust over Go."

The agent saves it. You see the `update_user_memory` tool call in the response.

Agentic mode is more selective. Less noise. User can see when the agent saves something, and can control their memories -- which might be a feature or a bug depending on your use case.

## Learned Knowledge: GPU Poor Learning

- **What it captures:** Insights. Patterns. Best practices. Shared Goals. Things that apply across users and sessions.
- **Scope:** Can be private to a user, shared across a team, or global.

Fine-tuning costs tens of thousands. RLHF needs infrastructure most teams don't have. This? A vector database and some prompt engineering.

```python
from agno.agent import Agent
from agno.db.postgres import PostgresDb
from agno.knowledge import Knowledge
from agno.knowledge.embedder.openai import OpenAIEmbedder
from agno.learn import LearnedKnowledgeConfig, LearningMachine, LearningMode
from agno.models.openai import OpenAIResponses
from agno.vectordb.pgvector import PgVector, SearchType

db_url = "postgresql+psycopg://ai:ai@localhost:5532/ai"
db = PostgresDb(db_url=db_url)

knowledge = Knowledge(
    vector_db=PgVector(
        db_url=db_url,
        table_name="learned_knowledge_demo",
        search_type=SearchType.hybrid,
        embedder=OpenAIEmbedder(id="text-embedding-3-small"),
    ),
)

agent = Agent(
    model=OpenAIResponses(id="gpt-5.2"),
    db=db,
    instructions="Be concise. Save valuable insights using your learning tools.",
    learning=LearningMachine(
        knowledge=knowledge,
        learned_knowledge=LearnedKnowledgeConfig(
            mode=LearningMode.AGENTIC,
        ),
    ),
    markdown=True,
)
```

**First conversation:**

User A: "Always check cloud egress costs first - they vary 10x between providers."

The agent saves it using the `save_learning` tool.

**Later, different user, different session:**

User B: "I'm picking a cloud provider for a 10TB daily data pipeline. Key considerations?"

The agent searches prior learnings. Finds the egress cost insight. Applies it.

This is the moment. User A taught the agent something. User B benefits. The agent gets smarter across the board.

Interaction 1000 is better than interaction 1.

## The Other Two Stores

**Session Context:** tracks what's happening right now -- summary, goal, plan, progress. Use `session_context=True` for lightweight continuity. Use `enable_planning=True` when you need structured task tracking.

**Entity Memory:** is User Profile for things that aren't the user -- companies, people, projects. It extracts facts, events, and relationships. Think of it as a CRM that builds itself.

Full examples for both are in the PR.

## Here's What I'm Finding

### Background vs Agentic

- **Background:** invisible, automatic, potentially noisy.
- **Agentic:** visible, selective, auditable.

For consumer products (like Claude), background feels magical. For enterprise, agentic might be better -- you can see exactly what the agent remembered and why. We can also use both together but then curation becomes important as memories will duplicate.

### The stores work together

The most interesting results come from combining different types of learning:

- User Profile + Session Context = knows who you are AND what you're doing
- Entity Memory + Learned Knowledge = knows your world AND gets smarter about it
- All four = a personalized, continuously improving agent

### Integration is the hard part

Extraction works well, and can be tested independently. Integration is not only hard to build, but to test:

- When do you extract? After every turn? In parallel? End of session?
- How do you present knowledge to the agent? Injected context vs tools?
- How do you teach the agent to use it naturally?

Still figuring these out.

## What's Next

Fine-tuning costs thousands. RLHF needs a full team. This runs on Postgres.

The endgame is simple: Agents analyzing their own failures and proposing: "I should stop doing X". Human approves. Agent improves.

The best part is that the LearningProtocol is ~50 lines to extend. Legal docs. Medical records. Codebases. Sales pipelines. Whatever your domain needs -- you (claude) can build it.

Learning customized to each domain is going to change the game.

Want to dig in? Here's the PR: learning-machine-v0

Memory was step one. Learning is what comes next.
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G-EbOQDXkAERX30?format=png&name=900x900)

![](https://pbs.twimg.com/tweet_video_thumb/G-DyDxxXMAEKU-h.jpg)


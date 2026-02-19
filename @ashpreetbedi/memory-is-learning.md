# Memory is Learning

**Author:** @ashpreetbedi  
**Date:** 2026-01-05T23:12:20.000Z  
**URL:** https://x.com/ashpreetbedi/article/2008315658417647895  
**Images:** 1  

---

Memory is Learning
This realization led me to build something different.
The Learning Machine: a unified learning system that helps agents continuously integrate information from their environment.
Here's the difference:
Traditional "Memory":
Message → Extract → Store → Retrieve → Dump into Prompt → Repeat

Learning Machine:
User Message ──────► Recall from Stores ◄────────┐
│                    │
▼                    │
Build Context              │
│                    │
▼                    │ LearningMachine
Agent Responds (with tools)      │
│                    │
▼                    │
Extract & Process             │
│                    │
▼                    │
Update Stores (agent learns) ──────┴──► Periodic Curation
The agent isn't just fed memories. It participates in learning, curating what it learns, and integrating that knowledge back into every response.
The goal: an agent on interaction 1000 is fundamentally better than it was on interaction 1 -- across the board, not just with the same user.
What It Looks Like in Action
A new employee on their first day asks: "I'm starting work on the cloud migration project. What should I know?"
The agent responds with full context, even though it's never talked to this person before. It knows Acme is migrating from AWS to GCP. It knows Alex (CTO) is leading it. It knows Phase 2 is the most compute-heavy. It shares migration patterns from similar past projects. It knows that the pricing is changing next quarter.
How? Three types of learning from past interactions:
Session 1 (Alex, CTO):
"I'm Alex, CTO at Acme. We're migrating from AWS to GCP and
I need help planning the timeline."

→ User Profile captures: Alex, CTO, involved in planning discussions
→ Entity Memory captures: Acme (company), AWS→GCP migration (project)
→ Session Context: Goal is migration timeline planning
Session 2 (next day, same user, different session):
"Just heard GCP is changing their pricing next quarter.
How does that affect our migration?"

→ Agent recalls: Acme, AWS→GCP migration, Alex is CTO, 3-phase timeline
→ Agent responds: "That could impact your timeline. Last time we mapped out a 3-phase approach with Phase 2 being the most compute-heavy. Want me to model the cost implications for each phase?"
Session 3 (different user, same org namespace):
"I just joined to help with the Acme cloud project. What should I know?"

→ Entity Memory: "Acme is migrating AWS to GCP. Alex (CTO) is leading it."
→ Learned Knowledge: Shares migration patterns from past projects
→ Agent responds with full context -- even though it never talked to this user
Three sessions. Three types of learning. Cross-user knowledge sharing.
This is possible. Today.
The Architecture: Learning Stores
The key innovation behind the Learning Machine is the learning protocol and learning stores. The protocol defines how stores capture, process, and integrate knowledge. Each store is configured independently, mix and match as needed. The Learning Machine orchestrates it all.
These are the stores I'm currently working on:
User Profile: Preferences, memories, personal context
Session Context: Goal, plan, progress, summary
Entity Memory: Facts, events, relationships
Learned Knowledge: Insights, patterns, best practices
Decision Logs: Why decisions were made
Behavioral Feedback: What worked, what didn't
Self-Improvement: Evolving instructions
Show Me Some Code
One agent. Four learning stores. Configured independently. Orchestrated by the Learning Machine.
python
from agno.agent import Agent
from agno.db.postgres import PostgresDb
from agno.models.openai import OpenAIResponses

agent = Agent(
model=OpenAIResponses(id="gpt-5.2"),
db=PostgresDb(db_url="postgresql://..."),
learning=LearningMachine(
knowledge=my_vector_store,  # or graph if that's your thing
user_profile=UserProfileConfig(
mode=LearningMode.BACKGROUND,
enable_agent_tools=True,
),
session_context=SessionContextConfig(
enable_planning=True,
),
learned_knowledge=LearnedKnowledgeConfig(
mode=LearningMode.PROPOSE,
),
entity_memory=EntityMemoryConfig(
mode=LearningMode.BACKGROUND,
),
),
)
The best part? You can build custom learning stores by extending the LearningStore protocol. Need project context? Build a ProjectContextStore. Need to track accounts? Build an AccountStore.
Taking Inspiration from Claude
Claude's memory feels magical. It's natural, contextual, never announces "saving to memory". It just knows you.
But here's the thing: you can't build with it. Claude's memory is a consumer product feature. The API gives you nothing. If you want learning for your agents, you're on your own. Enter Learning Machine.
Here's what Claude does well, and what Learning Machine adds:
Claude feels natural. It never announces "saving to memory". So does Learning Machine. We inject context based on each store and control how the agent learns from it. No fact dumps.
Claude learns about its users over time. Preferences, history, personal context. So does Learning Machine. But we also add sessions, entities, patterns, and decisions. The full picture, not just the user.
Claude is scoped to a single user. Makes sense for a consumer product. Learning Machine adds namespace scoping: keep it private to a user, share across a team, or make it global. You control the boundaries.
Claude has a fixed memory type. You can't change how it works. Learning Machine is extensible via protocol. Build your own stores for whatever your domain needs.
Claude is a closed system. Its memory lives inside Claude. Learning Machine is open source, fully customizable, and yours to extend.
I studied what makes Claude's memory great. Then built something we can actually use and extend.
What This Unlocks
Here's what's possible when agents learn across users, sessions, and time:
A support agent where ticket #1,000 gets resolved better and faster -- because it learned from tickets #1-999.
A customer success agent that remembers every account's stack, contracts, and conversations -- across your entire team.
A healthcare agent that knows your full history -- not just what's in today's chart, but every conversation (with different doctors), symptom, and concern you've ever mentioned.
A financial advisor that remembers your risk tolerance, goals, and every "what if" scenario you've ever explored -- across years of conversations.
An agent that rewrites itself -- analyzing its failures and proposing: "I should stop doing X."
That last one is the endgame. Agents that learn from their mistakes and rewrite their instructions. Human approves. Agent evolves.
Current status and what's next?
Learning Machine is a part of Agno and I'm testing Phase 1. Here's where things stand:
Phase 1: User Profile, Session Context, Entity Memory, Learned Knowledge -- built, testing now
Phase 2: Decision Logs, Behavioral Feedback -- planned
Phase 3: Self-Improvement -- planned
If you're eager to dig in, here's the PR: learning-machine-v0
Want to get involved? DM me
Memory was never the goal. Learning was.
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G96gMr-WsAAzUOu?format=jpg&name=900x900)


# First, Let's Recap

**Author:** @ashpreetbedi  
**Date:** 2026-01-08T20:08:14.000Z  
**URL:** https://x.com/ashpreetbedi/article/2009356490939773325  
**Images:** 1  

---

On Monday I introduced Learning Machines and yesterday I shared that it's finally working. Today I'll show you how it works under the hood.

⚠️ WARNING: TECHNICAL CONTENT AHEAD

# First, Let's Recap

After reading hundreds of papers on agentic memory and trying out every possible tool, I came to the simple conclusion that maybe we're looking at memory wrong.

Memory is just... learning. Learning about the user, the task at hand, learning insights and patterns, learning from decisions - good and bad, the feedback received. Learning from every interaction. Everything else is integration (how the agent uses these learnings) and curation (decay, pruning, deduplication).

So I built Learning Machines: A system that helps agents continuously learn from every interaction.

I started working on it dec 31, and got a basic working version yesterday. Here's the PR for those interested: 

[learning-machine-v0](https://github.com/agno-agi/agno/pull/5897)

Now let's dig into the technical details.

# The Learning Protocol

The key behind it all is the Learning Protocol. It's a simple interface for building Learning Stores -- user profiles, session context, learned knowledge, entity memory, etc.

Let's take a look at the protocol:

python

```
@runtime_checkable
class LearningStore(Protocol):
    """Protocol that all learning stores must implement."""

    @property
    def learning_type(self) -> str:
        """Unique identifier (e.g., 'user_profile')."""
        ...

    def recall(self, **context) -> Optional[Any]:
        """Retrieve learnings from storage."""
        ...

    def process(self, messages: List[Any], **context) -> None:
        """Extract and save learnings from conversation."""
        ...

    def build_context(self, data: Any) -> str:
        """Build context string for agent's system prompt."""
        ...

    def get_tools(self, **context) -> List[Callable]:
        """Get tools to expose to agent."""
        ...
```

Five functions. Everything else is optional.

Why this matters: You can build your own learning store in ~50 lines. Most memory systems are thousands of lines of config. This is ~50. Legal docs. Medical records. Codebases. Sales pipelines. Whatever your domain needs.

You can even build personalized LearningStores for your writing styles, for your daily to-do's, for your emails, for your shopping lists. The real value of this approach is its extensibility.

# The Learning Machine

The protocol lets you build stores. But stores need to plug into the agent somehow. That's what LearningMachine does.

```
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
```

Recall → Build context → Run agent → Extract → Store. That's the loop.

# Developer Experience

Three levels of complexity:

## Dead Simple

python

```
agent = Agent(
    model=model,
    db=db,
    learning=True,  # Enables user_profile in BACKGROUND mode
)
```

## Pick What You Want

python

```
agent = Agent(
    model=model,
    db=db,
    learning=LearningMachine(
        user_profile=True,
        session_context=True,
        learned_knowledge=True,
        entity_memory=True,
    ),
)
```

## Full Control

python

```
agent = Agent(
    model=model,
    db=db,
    learning=LearningMachine(
        user_profile=UserProfileConfig(
            mode=LearningMode.AGENTIC,
        ),
        session_context=SessionContextConfig(
            enable_planning=True,
        ),
        learned_knowledge=LearnedKnowledgeConfig(
            mode=LearningMode.PROPOSE,
            namespace="engineering",
        ),
        entity_memory=EntityMemoryConfig(
            mode=LearningMode.BACKGROUND,
        ),
    ),
)
```

# Build Your Own Learning Store

This is the win. Implement the protocol, plug it in:

python

```
@dataclass
class ProjectContextStore:
    """Custom store for project-specific context."""

    @property
    def learning_type(self) -> str:
        return "project_context"

    def recall(self, project_id: str, **kwargs) -> Optional[ProjectContext]:
        # Retrieve from your storage
        ...

    def process(self, messages: List[Any], project_id: str, **kwargs) -> None:
        # Extract and save
        ...

    def build_context(self, data: Any) -> str:
        if not data:
            return ""
        return f"<project_context>\n{data.summary}\n</project_context>"

    def get_tools(self, **kwargs) -> List[Callable]:
        return []  # Or return tools for agentic mode

# Plug it in
learning = LearningMachine(
    custom_stores={
        "project": ProjectContextStore(),
    },
)
```

~50 lines. 5 functions. Your domain, your rules. Build a Learning Store for legal docs, medical records, codebases, sales pipelines.

This is the whole point behind the Learning Machine.

# Built-in Stores

Phase 1 includes four stores:

## User Profile

- What it captures: Name, work context, preferences, communication style.
- Scope: Per user (`user\_id`).
- Storage: Database (direct lookup).

## Session Context

- What it captures: Summary of conversation, goal, plan steps, progress.
- Scope: Per session (`session\_id`).
- Storage: Database (direct lookup).

## Learned Knowledge

- What it captures: Insights, patterns, best practices. Things that apply across users.
- Scope: Configurable namespace -- private to a user, shared with a team, or global.
- Storage: Knowledge base (vector search).

## Entity Memory

- What it captures: Facts, events, and relationships about external things -- companies, people, projects.
- Scope: Configurable namespace.
- Storage: Database (direct lookup + search).

# Key Design Decisions

## Learning Modes

Different use cases need different extraction modes.

python

```
class LearningMode(Enum):
    BACKGROUND = "background"   # Automatic extraction after each conversation
    AGENTIC = "agentic"         # Agent decides via tools
    PROPOSE = "propose"         # Agent proposes, user confirms
    HITL = "hitl"               # Human-in-the-loop approval (future)
```

- BACKGROUND is invisible. The user never sees extraction happening. This is what makes Claude's memory feel natural.
- AGENTIC gives control. The agent decides what's worth remembering. You can see the tool calls. Less noise, more transparency.
- PROPOSE is for medium-stakes learning. Agent suggests, human approves. Good for shared knowledge bases where bad data spreads.
- HITL is for highest-stakes learning. Explicit human approval required.

## Namespace Scoping

Some learnings should be private. Some should be shared. Namespaces enable this.

python

```
# Private to this user
LearnedKnowledgeConfig(namespace="user")

# Shared within engineering team
LearnedKnowledgeConfig(namespace="engineering")

# Shared with everyone
LearnedKnowledgeConfig(namespace="global")
```

This is what enables cross-user learning. This is what made yesterday's experiment work -- Alice's insight helped Bob because they shared a namespace.

## Entity Memory: Three-Tier Memory System

Entities (people, companies, projects) hold different types of information:

- Facts: Semantic knowledge ("Uses PostgreSQL", "Based in London")
- Events: Episodic memories ("Launched v2 on Jan 15", "Raised Series A")
- Relationships: Graph connections ("Bob is CEO of Acme", "Acme acquired StartupX")

Flat list doesn't work. You need to query "what do we know about Acme" differently than "what happened with Acme."

# What's Next

- Phase 1 (now): Learning Protocol, Learning Machine + 4 Learning Stores. Built, currently testing and fixing bugs.
- Phase 2: Decision Logs and Behavioral Feedback. Agents that know why they did what they did, and what worked.
- Phase 3: Self-Improvement. This is the endgame. Agents that analyze their own failures and propose: "I should stop doing X." Human approves. Agent evolves. No retraining. No fine-tuning. Just learning.

Want to dig in? Here's the PR: 

[learning-machine-v0](https://github.com/agno-agi/agno/pull/5897)

Memory was step one. Learning is what comes next.

---

## Images

![Image](https://pbs.twimg.com/media/G-KVtJfWQAAyQlV?format=png&name=900x900)


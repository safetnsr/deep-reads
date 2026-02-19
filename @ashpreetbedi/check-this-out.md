# Check this out

**Author:** @ashpreetbedi  
**Date:** 2026-01-12T18:29:15.000Z  
**URL:** https://x.com/ashpreetbedi/article/2010781132418064750  
**Images:** 1  

---

Check this out

I've been testing a prototype with my team for a few days, here's a sample session:

**Session 1** — an engineer tells the agent:

> "We're trying to cut down on egress costs. Any ideas?"

The agent saves that the team is working on cutting egress costs.

**Session 2** — a different engineer, different conversation, asks:

> "I'm deciding between datadog and grafana. Any recommendations?"

The agent lets the second engineer know about datadog's egress costs, mentioning it's something the team is trying to reduce. Unprompted.

One person taught the agent something. Another person on the team benefited from it. No shared context. No explicit handoff. The agent just... knew.

If you're on a team of people working with agents all day, you know how crazy this is. When one person figures out how to test a tricky feature or shares an insight, everyone benefits automatically. The knowledge compounds.

If Agents are the future then cross-user, cross-session knowledge continuity is a must.

## We've been asking the wrong question
I kept trying to solve the memory problem. Better extraction. Smarter retrieval. More context. It felt like tightening a screw with a hammer.
Then it clicked.
I was asking "what should the agent remember?" and instead should have asked "what should the agent learn?"
Memory is a noun. Learning is a verb.

This might seem like wordplay but the change in perspective yields a fundamental shift. Memory is static: a database of facts about the user. Learning is dynamic: it evolves, compounds, gets sharper and has a purpose.

Agents can now learn! Every interaction makes them sharper.

## Learning Machines

```
Agent = LLM + Tools + Instructions
Learning Machine = Agent + Learning Stores
```
Learning stores run in the background and capture different types of knowledge, picking stuff up that can be helpful in future runs.

But here's the breakthrough: they're extensible by design. You can build custom stores for your domain, workflow and weird specific way of doing things.

For me, when I'm coding, I want my agent to learn where the source code for a feature lives. Where the testing cookbooks are. How to run the tests. Which environment to use. When the agent learns how to test my feature, everyone on the team should benefit from it.

## How It Works

Here's how you turn an agent into a Learning Machine:

```python
from agno.agent import Agent
from agno.db.postgres import PostgresDb
from agno.learn import LearningMachine, LearnedKnowledgeConfig, LearningMode

agent = Agent(
    model=OpenAIResponses(id="gpt-5.2"),
    db=PostgresDb(db_url="..."),
    learning=LearningMachine(
        user_memory=True,
        learned_knowledge=LearnedKnowledgeConfig(
            mode=LearningMode.AGENTIC,  # agent decides when to save/search
        ),
    ),
)
```

That's it. Your agent can capture insights, patterns and know-how from your interactions, it has tools to save learnings and search them later.

## The protocol

The default stores handle generic knowledge. But what if I need my agent to learn project-specific stuff: where the source code lives, how to run tests, which environment to use.

Solution: Built your own Learning Store:

```python
from agno.learn import LearningStore

class ProjectContext(LearningStore):
    """Learns the structure and workflow of a specific project."""

    def recall(self, project_id: str, **kwargs):
        # What has the agent learned about this project?
        ...

    def process(self, messages: List[Message], **kwargs):
        # Extract learnings from every conversation:
        # - Where does the source code live?
        # - How do we run tests?
        # - Which environment to use?
        # - What are the gotchas?
        ...

    def build_context(self, data) -> str:
        # Inject learnings into the agent's context
        return f"<project_context>\n{data}\n</project_context>"

# Plug it in
learning = LearningMachine(
    custom_stores={
        "project": ProjectContextStore(
            context={
                "project_id": "learning-machine",
            },
        ),
    },
)
```

Three methods and we have a domain-specific learning store.

Now every nudge I give the agent gets extracted and available next run. This is what I use with my homegrown `gcode` agent.

The default stores get you started. Custom stores teach your agent anything.

## Where this goes
This is Phase 1. It works. Let's see how far we can push this.

Phase 2 adds decision logging - agents that learn from what happened.

Phase 3 is the weird one: self-improvement. Agents that propose updates to their own instructions.

I don't know if all of this works yet. We're building to find out.

Here's what I do know: the most valuable learning stores don't exist yet. And they won't come from us. They'll come from developers who understand their domain - legal, medical, finance, ops - stuff we'll never know as well as they do.

My bet's not that we built the right stores, but that we built the right protocol for others to build theirs.

If you're interested, checkout the cookbooks on Github.

Build something. Break something. Tell me what's missing.

Learning Machines is part of Agno, the multi-agent runtime.
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G-eEiRhXMAAzAqk?format=png&name=900x900)


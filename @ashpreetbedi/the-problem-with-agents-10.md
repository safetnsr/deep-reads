# The Problem with Agents 1.0

**Author:** @ashpreetbedi  
**Date:** 2026-01-21T00:51:41.000Z  
**URL:** https://x.com/ashpreetbedi/article/2013776479633482201  
**Images:** 1  

---

The Problem with Agents 1.0
Today's agents are reactive by design. Input comes in. Agent processes it. Output goes out. Next input? Start from scratch.
Session history helps, your agent can remember what happened earlier in the conversation. But that's limited and doesn't scale across hundreds of users, conversations and interactions.
The moment the session ends, it's gone. No memory. No improvement. No evolution.
Think about what this means:
User corrects the agent? Forgotten by next session.
Agent makes the same mistake twice? No mechanism to prevent it.
User shares preferences over months of usage? Starts fresh every time.
Agent discovers a useful insight? Lost forever.
Humans don't learn this way. And there's zero chance this is the final form of agents either.
The Moment It Clicked
We've been testing a new concept internally called the Learning Machine. Here's when it clicked for us:
Session 1 — an engineer tells the agent:
> "We're trying to cut down on egress costs. Any ideas?"
The agent helps. Behind the scenes, it picks up on something: *egress costs can dominate cloud bills unexpectedly.*
Session 2 — a different engineer, different conversation, asks:
> "I'm deciding between datadog and grafana. Any recommendations?"
The agent responds and flags that datadog's architecture could increase egress costs. Mentions it's something the team is trying to reduce.
Unprompted. No shared context. No explicit handoff.
One person taught the agent something. Another person benefited. The knowledge transferred. This is what Agents 2.0 looks like.
Agents 2.0
Real learning means the agent on interaction 1000 behaves better than it did on interaction 1. This evolution is guided by six types of learning:
User Memory. Preferences, context, communication style.
"She prefers bullet points for executive summaries"
Session Context. Goal, plan, progress.
"Working on LearningMachine design, completed DX section"
Entity Memory. Facts, events, relationships about external things.
"Acme Corp: fintech, 50 employees, uses Postgres"
Learned Knowledge. Insights, patterns, reusable facts.
"Tech stocks typically trade at 20-35x P/E"
Decision Logs. Why decisions were made, reasoning traces.
"Approved 20% discount due to churn risk"
Behavioral Feedback. What worked, what didn't, corrections.
"Long responses to quick questions get thumbs down"
Models are already capable of this. Agents 2.0 is an engineering problem, not an intelligence problem.
Memory, knowledge bases, decision logs, feedback signals, persistent storage. The primitives exist. We just need to wire them together.
More soon.
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G_JYDr0XUAAYKZR?format=png&name=900x900)


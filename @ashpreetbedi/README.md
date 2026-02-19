# @ashpreetbedi

**founder of [agno](https://github.com/agno-agi) (formerly phidata) — multi-agent runtime**

17 articles, published jan-feb 2026. read chronologically, they tell the story of agno's evolution from framework to runtime to "programming language for agents."

## our take

ashpreet is a better writer than most AI founders and a better marketeer than most engineers. the articles are clear, jargon-free, and well-structured. but they're also a sales funnel disguised as a technical blog.

### what's genuinely good

**the 3-layer knowledge pattern** is the best technical insight across all 17 articles. not everything belongs in a vector store:
- layer 1: static context (always in prompt, never searched — mandates, rules, constraints)
- layer 2: vector search (large corpus, semantic retrieval)
- layer 3: file browsing (structured docs read in full, not chunked)

most RAG tutorials miss this entirely. this pattern is worth adopting.

**"memory is learning"** is a real reframe. memory = noun (static database of facts). learning = verb (evolves, compounds, improves). the distinction matters for how you design agent systems.

**the investment committee article** is the standout. five orchestration patterns (route, broadcast, coordinate, task, workflow) applied to the same 7 agents, with real trade-offs discussed. if all 17 articles were this level, it'd be a killer blog.

### what's marketing

- the "Learning Machine" demo (alice mentions egress costs, bob benefits unprompted) appears in **6 different articles** as if it's new each time
- every article follows the same arc: genuine problem → "existing tools don't solve this" → "we built agno"
- `learning=True` and your agent "learns" — in practice, extraction is noisy, retrieval hallucinates, and cross-user knowledge sharing is a privacy/security nightmare that's never discussed
- "GPU Poor Learning" is catchy but misleading — it's prompt injection + vector search, not comparable to fine-tuning or RLHF despite being framed that way
- "agno is a programming language" is classic VC-pitch overreach. a framework is not a language

### what's worth stealing

1. **3-layer knowledge** — explicit separation of static context / searchable corpus / browsable documents
2. **learning modes** — BACKGROUND (auto), AGENTIC (agent decides), PROPOSE (agent suggests, human approves), HITL (human required)
3. **namespace scoping** — learnings can be private (per user), shared (per team), or global
4. **decision logs** — not just WHAT the agent did, but WHY (reasoning traces)
5. **spec-first development** — the claude code workflow in "The Shift" is solid practical advice

### what he doesn't do that matters

- **self-observation** — his agents learn from users, not from themselves. no introspection on own performance, costs, or efficiency
- **forgetting** — agno only accumulates. no pruning, archival, or decay. memory grows unbounded
- **cost awareness** — no mention of token costs, trace costs, or budget constraints anywhere
- **framework independence** — agno learning only works inside agno. vendor lock-in by design

## articles (chronological)

| date | article | chars | our notes |
|------|---------|-------|-----------|
| 2026-01-05 | [memory-is-learning](memory-is-learning.md) | 7K | the founding thesis. introduces learning machine concept |
| 2026-01-07 | [holy-shit-its-working](holy-shit-its-working.md) | 8K | the egress cost demo. repeated in 5 more articles |
| 2026-01-08 | [first-lets-recap](first-lets-recap.md) | 7K | technical deep dive on learning protocol. best technical writing |
| 2026-01-10 | [the-pitch-vs-the-reality](the-pitch-vs-the-reality.md) | 4K | anti-eval take. valid points but self-serving |
| 2026-01-12 | [check-this-out](check-this-out.md) | 5K | learning machine demo with code examples |
| 2026-01-13 | [the-shift](the-shift.md) | 7K | claude code workflow. genuinely useful practical advice |
| 2026-01-15 | [ai-engineering-has-a-runtime-problem](ai-engineering-has-a-runtime-problem.md) | 6K | runtime thesis. well-articulated problem statement |
| 2026-01-21 | [the-problem-with-agents-10](the-problem-with-agents-10.md) | 3K | agents 1.0 vs 2.0 framing |
| 2026-01-21 | [the-path-to-production](the-path-to-production.md) | 4K | AgentOS launch post |
| 2026-01-26 | [what-we-want-to-build](what-we-want-to-build.md) | 4K | "own your database" thesis. strongest argument for self-hosted |
| 2026-01-28 | [what-learning-isnt](what-learning-isnt.md) | 10K | most complete learning machine article. code-heavy |
| 2026-01-29 | [what-it-does](what-it-does.md) | 4K | Pal (personal agent) intro |
| 2026-01-29 | [the-agents](the-agents.md) | 2K | AgentOS template: 3 agents, deploy guide |
| 2026-02-01 | [the-6-layers-of-context](the-6-layers-of-context.md) | 4K | Dash (text-to-SQL agent). OpenAI collab |
| 2026-02-12 | [the-architecture-stays-the-same](the-architecture-stays-the-same.md) | 5K | "agents are the new programs" thesis |
| 2026-02-17 | [what-makes-a-programming-language](what-makes-a-programming-language.md) | 6K | "agno is a language" — peak overreach |
| 2026-02-19 | [why-an-investment-committee](why-an-investment-committee.md) | 18K | best article. 5 orchestration patterns, 3-layer knowledge |

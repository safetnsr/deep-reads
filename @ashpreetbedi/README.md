# @ashpreetbedi

**founder of [agno](https://github.com/agno-agi) — multi-agent runtime. prev airbnb, facebook.**

17 articles, published jan-feb 2026. read chronologically, they tell the story of agno's evolution from framework to runtime to "programming language for agents."

---

## the good

**he writes clearly.** no jargon soup, no hand-waving. when he explains a technical concept, you understand it. that's rarer than it should be in AI.

**the 3-layer knowledge pattern** is the best idea across all 17 articles. his argument: not everything belongs in a vector store.
- layer 1: static context — always in the prompt, never searched. rules, mandates, constraints. if the risk officer misses a position limit because vector search returned a different chunk, you have a real problem.
- layer 2: vector search — large corpus, semantic retrieval. company profiles, research docs. this is where RAG shines.
- layer 3: file browsing — structured documents read in full, not chunked. investment memos lose their meaning when split into 512-token fragments.

most RAG tutorials dump everything into one vector store and hope for the best. this is a better mental model.

**"memory is learning"** is a genuine insight. memory is a noun — a static database of facts. learning is a verb — it evolves, compounds, gets sharper. the distinction changes how you design agent systems. whether you use agno or not, this reframe is useful.

**the investment committee article** is the standout piece. five orchestration patterns (route, broadcast, coordinate, task, workflow) applied to the same 7 agents, with honest trade-offs. concrete, opinionated, shows real output. if all 17 articles were at this level, it'd be one of the best AI blogs out there.

**the claude code workflow** in "The Shift" is practical and honest. spec-first development, layered CLAUDE.md files, cookbooks-or-it-didn't-happen. this is advice you can use tomorrow regardless of what framework you're on.

## the bad

**the same demo appears in 6 articles.** alice mentions egress costs, bob benefits unprompted, we're told this is revolutionary. by article 4, you're skimming. the learning machine concept deserved one definitive post, not six incremental ones.

**every article follows the same arc:** genuine problem → existing tools fail → agno solves it. the runtime problem is real. the eval critique has merit. but when every post ends at the same destination, the journey stops feeling like exploration and starts feeling like a funnel.

**`learning=True` and your agent "learns"** — this undersells the complexity. in practice: extraction is noisy. retrieval hallucinates. cross-user knowledge sharing is a privacy and security minefield that's never discussed. the gap between the demo and production is exactly the gap he criticizes others for ignoring.

**"GPU Poor Learning" is catchy but misleading.** it's prompt engineering + vector search. comparing it to fine-tuning and RLHF as if they're interchangeable approaches to the same problem misrepresents what each actually does.

**"agno is a programming language"** is the weakest article. a framework with agents, tools, and workflows is not a language in any meaningful sense. python didn't call itself a language because it had lists and functions — it earned that label through semantics, a type system, and an execution model. this reads like a pitch deck slide that escaped into a blog post.

## worth stealing (for anyone building agent systems)

1. **3-layer knowledge** — separate static context, searchable corpus, and browsable documents. know which layer each piece of knowledge belongs in.
2. **learning modes** — not every type of knowledge capture should work the same way. some should be automatic, some agent-controlled, some human-approved. design for this explicitly.
3. **spec-first development** — keep design docs, decision records, and implementation status alongside your code. let your coding agent read them.
4. **cookbooks over tests** — runnable examples that prove a feature works end-to-end are worth more than unit tests for agent systems.
5. **decision logs** — track not just what your agent did, but why. invaluable for debugging non-deterministic systems.

## what's missing

- **self-observation.** his agents learn from user interactions but never examine their own performance. no cost tracking, no efficiency analysis, no pattern detection on the agent's own behavior.
- **forgetting.** agno only accumulates. no pruning, archival, or decay. memory grows without bound. any system that only remembers and never forgets will drown in noise.
- **cost awareness.** 17 articles about agent infrastructure and not a single mention of token costs, budget constraints, or the economics of running these systems in production.
- **failure modes.** the demos always work. what happens when cross-user knowledge transfer surfaces something wrong? when the learning store fills with contradictory insights? when extraction hallucinates a "pattern" that doesn't exist?

---

## articles (chronological)

| date | article | size | notes |
|------|---------|------|-------|
| jan 5 | [memory-is-learning](memory-is-learning.md) | 7K | the founding thesis. introduces the learning machine concept |
| jan 7 | [holy-shit-its-working](holy-shit-its-working.md) | 8K | the egress cost demo. first appearance of 6 |
| jan 8 | [first-lets-recap](first-lets-recap.md) | 7K | technical deep dive on the learning protocol. strongest technical writing |
| jan 10 | [the-pitch-vs-the-reality](the-pitch-vs-the-reality.md) | 4K | anti-eval take. valid points, self-serving conclusion |
| jan 12 | [check-this-out](check-this-out.md) | 5K | learning machine with code. "GPU Poor Learning" coined here |
| jan 13 | [the-shift](the-shift.md) | 7K | claude code workflow. genuinely useful practical advice |
| jan 15 | [ai-engineering-has-a-runtime-problem](ai-engineering-has-a-runtime-problem.md) | 6K | the runtime thesis. well-articulated problem, obvious product placement |
| jan 21 | [the-problem-with-agents-10](the-problem-with-agents-10.md) | 3K | agents 1.0 vs 2.0 framing |
| jan 21 | [the-path-to-production](the-path-to-production.md) | 4K | AgentOS launch. "50+ API endpoints" |
| jan 26 | [what-we-want-to-build](what-we-want-to-build.md) | 4K | "own your database." strongest argument for self-hosted state |
| jan 28 | [what-learning-isnt](what-learning-isnt.md) | 10K | most complete learning machine article. code-heavy, thorough |
| jan 29 | [what-it-does](what-it-does.md) | 4K | Pal personal agent. weekend experiment energy |
| jan 29 | [the-agents](the-agents.md) | 2K | AgentOS template walkthrough |
| feb 1 | [the-6-layers-of-context](the-6-layers-of-context.md) | 4K | Dash (text-to-SQL). OpenAI collaboration |
| feb 12 | [the-architecture-stays-the-same](the-architecture-stays-the-same.md) | 5K | "agents are the new programs." thoughtful but overstated |
| feb 17 | [what-makes-a-programming-language](what-makes-a-programming-language.md) | 6K | "agno is a language." peak overreach |
| feb 19 | [why-an-investment-committee](why-an-investment-committee.md) | 18K | best article. 5 orchestration patterns, 3-layer knowledge, real trade-offs |

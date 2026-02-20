# @hwchase17

**founder of [LangChain](https://github.com/langchain-ai/langchain) and [LangSmith](https://smith.langchain.com). the most influential and most criticized figure in the LLM tooling space.**

4 articles, published jan-feb 2026. all focused on the same thesis: observability is the foundational layer for agent systems. written during the LangSmith Agent Builder launch.

---

## context

harrison chase built LangChain in late 2022 — the right framework at the right moment. when GPT-3.5 dropped and everyone wanted to build with LLMs, LangChain was there. it grew to 90k+ github stars and became the default starting point for agent tutorials everywhere.

then the backlash came. LangChain became the framework everyone complained about — over-abstracted, poorly documented, constantly breaking APIs, hiding complexity behind wrappers that made debugging harder, not easier. "remove LangChain" threads became a genre. competitors (llamaindex, haystack, now agno) positioned themselves explicitly as the antidote.

chase's response wasn't to fight back. LangChain the framework took a backseat. LangSmith — the observability and evaluation platform — became the real product. that pivot is visible in every article here. he's no longer selling the framework. he's selling the insight layer on top of whatever framework you use.

these 4 articles are his public case for why that matters.

---

## the good

**the core thesis is correct.** the argument in [why code doesn't document agent behavior](why-code-doesnt-document-agent-behavior.md#why-code-doesnt-document-agent-behavior) — that traces replace code as the source of truth when the decision logic lives in the model — is genuinely right and genuinely underappreciated. most teams building agents in 2025 treat observability as a nice-to-have. this is the argument for why it's load-bearing infrastructure.

the specific reframings are sharp:
- [debugging becomes trace analysis](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents), not code review
- [product analytics merges with debugging](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents) — you can't understand user behavior without understanding agent behavior
- [testing becomes eval-driven](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents), because you need to continuously eval in production, not just pre-deployment

these aren't obvious points dressed up as obvious. the merge of product analytics and debugging is particularly underexplored — most teams still run them separately.

**the sandbox architecture article is the best technical writing in the set.** [pattern 1 vs pattern 2](pattern-1-agent-runs-in-sandbox.md#choosing-between-patterns) — agent-in-sandbox vs sandbox-as-tool — is a clean decision framework with honest trade-offs on both sides. the security insight from nuno campos at witan labs is valuable and not something you'd easily think through yourself: [when the agent runs inside the sandbox, every tool it has gets the same privilege level as bash](pattern-1-agent-runs-in-sandbox.md#pattern-1-agent-runs-in-sandbox). you can't have finer-grained permissions. that's a real architectural constraint that affects real systems.

ben guo's point about future GPU agent harnesses is also worth noting — it anticipates a separation of concerns that the field will probably arrive at: [execution environment requirements will diverge from inference harness requirements](pattern-1-agent-runs-in-sandbox.md#pattern-2-sandbox-as-tool).

**the LangSmith Agent Builder memory design is thoughtful.** using a [virtual filesystem backed by postgres](what-is-langsmith-agent-builder.md#how-we-built-our-memory-system) — exposing it to the agent as a filesystem because LLMs are good at filesystems, while keeping infrastructure sane — is a clean design decision. the [COALA paper](https://arxiv.org/abs/2309.02427) mapping (procedural = AGENTS.md, semantic = skills files, episodic = missing) is intellectually honest. acknowledging the gap instead of papering over it is good.

the [concrete memory evolution example](what-is-langsmith-agent-builder.md#how-memory-editing-works-a-concrete-example) — from "summarize meeting notes" to a full AGENTS.md with formatting rules, meeting types, and named person preferences over months of use — is the best illustration of what agent memory actually looks like in practice across the 4 articles. specific, plausible, useful.

**the production analytics argument has real teeth.** the quote that opens [why you can't predict agent behavior](why-you-cant-predict-agent-behavior.md) — teams recording 100k+ traces daily and doing literally nothing with them — names a real problem. the three-part diagnosis ([non-determinism, prompt sensitivity, unbounded input space](why-you-cant-predict-agent-behavior.md#why-you-cant-predict-agent-behavior)) is accurate, and the conclusion that you discover how agents fail in production rather than pre-production is correct even if uncomfortable.

---

## the bad

**every article is product marketing dressed as technical education.** this isn't a subtle problem — it's the defining structural feature. each piece:
1. correctly identifies a real problem
2. explains it clearly
3. arrives at LangSmith as the solution

the analysis in [why code doesn't document agent behavior](why-code-doesnt-document-agent-behavior.md#make-the-shift) ends with "if you're building agents and you don't have this, you're working blind" — where "this" is implicitly LangSmith. the [insights agent article](why-you-cant-predict-agent-behavior.md#langsmith-insights-agent) is barely disguised as technical content. the sandbox article is more neutral than the others, but still ends with a deepagents code sample and "try it out" link.

this is his job, so it's not an unfair criticism of him personally. but it does mean the articles should be read accordingly — as well-argued sales pitches, not independent analysis.

**the LangSmith Agent Builder article skips the hard questions about memory.** the [memory evolution example](what-is-langsmith-agent-builder.md#how-memory-editing-works-a-concrete-example) shows AGENTS.md growing from one line to a complex specification. but what happens when:
- the agent contradicts itself over time — one memory update conflicts with an earlier one?
- [agents were good at adding things but didn't compact](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system) — this is mentioned as a solved problem, but the solution (explicit prompting) is a workaround, not an architecture
- the memory grows large enough to hit context limits or start degrading inference quality?

the [multi-org memory section](what-is-langsmith-agent-builder.md#different-levels-of-memory) (user-level vs org-level memory) is listed as future work without acknowledging that org-level memory is a privacy and access control problem, not just an infrastructure problem. who decides what gets shared across users? what happens when org-level memory contains outdated or wrong information?

**"one person working full-time on prompting for memory" is mentioned as a learning, not a warning.** the [hardest part is prompting](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system) section buries the lede. the team spent enormous effort on a system that still required explicit user prompting to compact correctly, still missed things in the hot path, and still needed human-in-the-loop approval to avoid prompt injection. for a no-code product targeting "technically lite" citizen developers, this is a significant gap between the demo and the deployment reality.

**the insights agent article is the weakest.** [why you can't predict agent behavior](why-you-cant-predict-agent-behavior.md) makes a valid point — you can't discover unknown patterns with upfront evaluators — but the solution (LangSmith Insights Agent does clustering, here are screenshots) doesn't really engage with the hard part: how good is the clustering? what happens when the model hallucinates patterns? how do you validate that the clusters it finds are real? none of that is addressed.

---

## patterns worth stealing

1. **[traces as source of truth](why-code-doesnt-document-agent-behavior.md#traces-as-the-new-documentation)** — when decision logic lives in the model, observability isn't debugging infrastructure, it's documentation. design your systems so traces are first-class artifacts, not afterthoughts.

2. **[filesystem-shaped memory](what-is-langsmith-agent-builder.md#how-we-built-our-memory-system)** — expose memory to the agent as files because LLMs are good at filesystems. back it with postgres (or whatever). the shape the model sees doesn't have to match the storage layer.

3. **[validate LLM-generated schemas immediately](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system)** — when agents write structured files (JSON config, markdown with frontmatter), validate the schema and throw errors back to the LLM before committing. don't let bad writes silently persist.

4. **[sandbox placement is an architectural decision, not an implementation detail](pattern-1-agent-runs-in-sandbox.md#choosing-between-patterns)** — the choice between agent-in-sandbox and sandbox-as-tool affects security posture, iteration speed, API key exposure, and privilege separation. make it deliberately.

5. **[combine quantitative signals with qualitative trace analysis](why-you-cant-predict-agent-behavior.md#langsmith-insights-agent)** — thumbs-down feedback tells you something is wrong. traces tell you what. the habit of filtering to failed runs and then examining patterns in those traces is operationally useful regardless of what tool you use to do it.

6. **[human-in-the-loop for memory writes by default](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system)** — require approval before agents update persistent memory. prompt injection via memory manipulation is a real attack vector. "yolo mode" as an explicit opt-in is the right default polarity.

---

## what's missing

**forgetting.** the memory system accumulates. there's no discussion of pruning, contradiction resolution, or decay. [episodic memory is listed as future work](what-is-langsmith-agent-builder.md#episodic-memory) but the more fundamental gap is forgetting — what happens when old memories are wrong, stale, or contradict current behavior?

**the LangChain debt.** these articles assume you're building on deepagents or at least a sane agent framework. they don't acknowledge that a meaningful fraction of their user base is still maintaining LangChain code from 2023 that's brittle, over-abstracted, and hard to instrument properly. LangSmith observability is most valuable for systems that are designed for observability from the start.

**cost.** the sandbox article mentions [paying for sandboxes only when executing code](pattern-1-agent-runs-in-sandbox.md#pattern-2-sandbox-as-tool) as a pattern 2 benefit, but nowhere across 4 articles about agent infrastructure is there a serious treatment of cost — token costs, eval costs, storage costs. if you're running LangSmith Insights Agent over 100k traces daily, that's not free.

**failure modes for clustering.** the insights agent is presented as the solution to "you can't read 100k traces manually." maybe. but the failure mode — the agent confidently presenting hallucinated clusters as real patterns — is potentially worse than having no analysis at all. a false pattern can send your team chasing the wrong problem for weeks.

---

## honest take on the LangChain controversy

the criticism of LangChain as a framework was largely fair. it added abstraction without clarity, made simple things complex, and optimized for tutorial demos over production reliability. the "remove LangChain" movement was a legitimate response to real engineering pain.

the LangSmith pivot is more defensible. the case that traces are the source of truth for agent systems is solid, and LangSmith addresses a real gap that exists regardless of what framework you use. chase is not wrong that most teams building agents have no idea what their agents are actually doing in production.

what these 4 articles don't do is reckon with the LangChain legacy honestly. the person who popularized agent development patterns that many people now have to maintain and debug is selling the observability tool for when those patterns fail. that's not dishonest — but it's worth naming.

the writing is clear, the technical content is mostly sound, and the product direction makes sense. read with the understanding that it's advocacy, and extract the genuine insights that stand on their own.

---

## articles

| date | article | notes |
|------|---------|-------|
| jan 10 2026 | [why-code-doesnt-document-agent-behavior](why-code-doesnt-document-agent-behavior.md) | the foundational thesis. traces replace code as source of truth. best standalone piece in the set. |
| jan 15 2026 | [what-is-langsmith-agent-builder](what-is-langsmith-agent-builder.md) | deep dive on memory architecture. filesystem-shaped memory, COALA mapping, honest learnings. most technical. |
| jan 20 2026 | [why-you-cant-predict-agent-behavior](why-you-cant-predict-agent-behavior.md) | production analytics argument. weakest of the 4 — mostly a product pitch with a valid premise. |
| feb 10 2026 | [pattern-1-agent-runs-in-sandbox](pattern-1-agent-runs-in-sandbox.md) | sandbox architecture patterns. best external contributions (nuno campos, ben guo). most framework-neutral. |

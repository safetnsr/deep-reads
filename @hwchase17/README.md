# @hwchase17

**founder of [LangChain](https://github.com/langchain-ai/langchain) and [LangSmith](https://smith.langchain.com). the most influential and most criticized figure in the LLM tooling space.**

4 articles, jan–feb 2026. all focused on the same thesis: observability is the foundational layer for agent systems. written during the LangSmith Agent Builder launch. these are the most honest and most self-serving articles simultaneously — the diagnoses are genuinely correct, and the destination is always LangSmith.

---

## context that shapes everything

harrison chase built LangChain in late 2022 — the right framework at the right moment. when GPT-3.5 dropped, LangChain was there. 90k+ github stars. the default starting point for agent tutorials.

then the backlash came. over-abstracted, poorly documented, constantly breaking APIs, hiding complexity behind wrappers that made debugging harder. "remove LangChain" threads became a genre. competitors (llamaindex, haystack, agno) positioned themselves explicitly as the antidote. [@ashpreetbedi](../@ashpreetbedi/README.md) built Agno's identity partly on being "not LangChain."

chase's response: LangChain the framework took a backseat. LangSmith — the observability platform — became the real product. that pivot is the subtext of every article here. he's no longer selling the framework. he's selling the insight layer on top of *whatever framework you use*. and the case for that layer is strong — stronger than the framework ever was.

---

## the good

**the core thesis is the most important idea in these 4 articles, and it's genuinely underappreciated.** [why code doesn't document agent behavior](why-code-doesnt-document-agent-behavior.md#why-code-doesnt-document-agent-behavior): when decision logic lives in the model, traces replace code as the source of truth. this isn't an obvious point dressed up as obvious. the specific reframings are sharp:

- [debugging becomes trace analysis](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents), not code review
- [product analytics merges with debugging](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents) — you can't understand user behavior without understanding agent behavior
- [testing becomes continuous production eval](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents), not pre-deployment gating
- [collaboration moves to observability platforms](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents), not just GitHub

the merge of product analytics and debugging is the insight nobody else is saying clearly. most teams run them separately. chase argues they're the same thing when your product *is* the agent's decisions. he's right. compare this with [@ashpreetbedi](../@ashpreetbedi/the-pitch-vs-the-reality.md)'s anti-eval stance — ashpreet says evals don't get you to production, the runtime does. chase says you can't understand production without traces. they're both right, and they're both selling the part they built. the truth is you need both: a runtime that can ship and an observability layer that shows what shipped actually does.

**the sandbox architecture article is the best technical writing in the set and the most framework-neutral.** [pattern 1 vs pattern 2](pattern-1-agent-runs-in-sandbox.md#choosing-between-patterns) — agent-in-sandbox vs sandbox-as-tool — is a clean decision framework with honest trade-offs. the security insight from nuno campos is the kind of thing you'd only learn through painful experience: [when the agent runs inside the sandbox, every tool it has gets the same privilege level as bash](pattern-1-agent-runs-in-sandbox.md#pattern-1-agent-runs-in-sandbox). you can't have finer-grained permissions. that's a real architectural constraint.

this matters beyond sandboxes. it's an instance of a general principle: where you place the agent in your architecture determines your security boundary. if the agent is *inside* the execution environment, everything it can touch has the same trust level. if the agent *calls into* the execution environment, you can gate each call. [@ashpreetbedi](../@ashpreetbedi/the-architecture-stays-the-same.md#when-software-makes-decisions)'s "agents are the new programs" thesis implicitly assumes pattern 2 (agents as callers, not inhabitants), but never discusses the security implications explicitly.

ben guo's point about [execution environment requirements diverging from inference harness requirements](pattern-1-agent-runs-in-sandbox.md#pattern-2-sandbox-as-tool) anticipates a separation of concerns the field will probably arrive at — GPU harnesses for reasoning, sandboxed environments for execution, connected by tool calls.

**the filesystem-shaped memory is a design decision with surprising depth.** the [LangSmith Agent Builder memory system](what-is-langsmith-agent-builder.md#how-we-built-our-memory-system) — virtual filesystem backed by postgres, exposed to the agent as files because LLMs are good at filesystems — solves a real UX problem. the shape the model sees doesn't have to match the storage layer. the [COALA paper mapping](what-is-langsmith-agent-builder.md#how-we-built-our-memory-system) (procedural = AGENTS.md, semantic = skills files, episodic = missing) is intellectually honest — acknowledging the gap instead of papering over it.

the [memory evolution example](what-is-langsmith-agent-builder.md#how-memory-editing-works-a-concrete-example) — from "summarize meeting notes" to a full AGENTS.md with formatting rules, meeting types, and named preferences over months of use — is the best illustration of what agent memory looks like in practice across all five authors. more concrete than [@ashpreetbedi](../@ashpreetbedi/what-learning-isnt.md)'s learning stores (which show code patterns but not long-term evolution) and more grounded than [@manthanguptaa](../@manthanguptaa/why-do-current-memory-systems-fail.md)'s architecture (which is theoretical).

**the production analytics argument names a real problem.** the opening of [why you can't predict agent behavior](why-you-cant-predict-agent-behavior.md) — teams recording 100k+ traces daily and doing literally nothing with them — names a gap that exists in most agent teams right now. the three-part diagnosis ([non-determinism, prompt sensitivity, unbounded input space](why-you-cant-predict-agent-behavior.md#why-you-cant-predict-agent-behavior)) is accurate. the conclusion that you discover failure modes in production, not pre-production, is correct and uncomfortable.

## the bad

**every article is product marketing dressed as technical education.** each piece follows the same arc: correctly identify a real problem → explain it clearly → arrive at LangSmith as the solution. the [traces article](why-code-doesnt-document-agent-behavior.md#make-the-shift) ends with "if you're building agents and you don't have this, you're working blind" — where "this" is implicitly LangSmith. the [insights agent article](why-you-cant-predict-agent-behavior.md#langsmith-insights-agent) is barely disguised product marketing. the sandbox article is the most neutral but still ends with a deepagents code sample.

this is his job. the articles should be read as well-argued sales pitches that contain genuine insights, not as independent analysis.

**the memory article skips the hard questions.** the [memory evolution example](what-is-langsmith-agent-builder.md#how-memory-editing-works-a-concrete-example) shows AGENTS.md growing from one line to a complex specification. but:
- what happens when memory updates contradict each other over time?
- [agents were good at adding but didn't compact](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system) — the fix (explicit prompting) is a workaround, not an architecture
- what happens when memory grows large enough to hit context limits?
- [multi-org memory](what-is-langsmith-agent-builder.md#different-levels-of-memory) is listed as future work without acknowledging it's a privacy and access control problem, not just infrastructure

the admission that ["one person is basically working full-time on prompting for the memory system"](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system) is buried as a learning rather than highlighted as a warning. for a no-code product targeting "technically lite" citizen developers, the gap between the demo and the deployment reality is significant. this is the same notebook-to-production gap that [@ashpreetbedi](../@ashpreetbedi/the-pitch-vs-the-reality.md#what-evals-dont-test) writes about — except neither author applies it to their own product.

**the insights agent article is the weakest.** [why you can't predict agent behavior](why-you-cant-predict-agent-behavior.md) makes a valid point (you can't discover unknown patterns with upfront evaluators) but the solution (LangSmith does clustering, here are screenshots) doesn't engage with the hard part: how good is the clustering? what happens when the model hallucinates patterns? how do you validate that discovered clusters are real? a false pattern can send a team chasing the wrong problem for weeks. the failure mode of AI-powered analytics is potentially worse than having no analytics — confident wrong answers beat confused right ones for damage potential.

**the LangChain elephant is in every room.** the person who popularized agent development patterns that many teams now have to maintain and debug is selling the observability tool for when those patterns fail. this isn't dishonest — trace analysis *is* genuinely needed — but the articles never reckon with the LangChain legacy. a meaningful fraction of LangSmith's user base is maintaining brittle LangChain code from 2023. LangSmith observability is most valuable for systems designed for observability from the start — which is not what LangChain code from 2023 was designed for.

## patterns worth stealing

1. **[traces as source of truth](why-code-doesnt-document-agent-behavior.md#traces-as-the-new-documentation)** — when decision logic lives in the model, observability isn't debugging infrastructure, it's documentation. design systems so traces are first-class artifacts.

2. **[filesystem-shaped memory](what-is-langsmith-agent-builder.md#how-we-built-our-memory-system)** — expose memory to the agent as files. back it with postgres. the shape the model sees doesn't have to match the storage layer.

3. **[validate LLM-generated schemas immediately](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system)** — when agents write structured files, validate the schema and throw errors back before committing. don't let bad writes silently persist.

4. **[sandbox placement as architectural decision](pattern-1-agent-runs-in-sandbox.md#choosing-between-patterns)** — agent-in-sandbox vs sandbox-as-tool affects security posture, privilege separation, and iteration speed. the choice is load-bearing, not incidental.

5. **[human-in-the-loop for memory writes by default](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system)** — require approval before agents update persistent memory. prompt injection via memory manipulation is a real attack vector. "yolo mode" as explicit opt-in is the right default.

6. **[product analytics = agent analytics](why-code-doesnt-document-agent-behavior.md#how-this-changes-building-agents)** — when your product *is* the agent's decisions, user behavior and agent behavior are the same data. don't run them as separate systems.

## what's missing

**forgetting.** like [@ashpreetbedi](../@ashpreetbedi/README.md), chase's memory system accumulates. no pruning, contradiction resolution, or decay. [episodic memory is listed as future work](what-is-langsmith-agent-builder.md#episodic-memory) but the more fundamental gap is forgetting — what happens when old memories conflict with current behavior? [@manthanguptaa](../@manthanguptaa/why-do-current-memory-systems-fail.md#consolidation-and-forgetting) is the only author who treats this as a design requirement.

**cost.** the sandbox article mentions [paying for sandboxes only when executing code](pattern-1-agent-runs-in-sandbox.md#pattern-2-sandbox-as-tool) as a pattern 2 benefit. but nowhere across 4 articles is there a serious treatment of cost — token costs, eval costs, storage costs, the cost of running an insights agent over 100k daily traces. if you're building production agent systems, cost is the constraint that shapes every other decision. none of these authors (except [@venturetwins](../@venturetwins/the-great-expansion.md) on the monetization side) take it seriously.

**failure modes for AI-powered analytics.** the insights agent is presented as the solution to "you can't read 100k traces." but the failure mode — the model confidently presenting hallucinated clusters as real patterns — is potentially worse than no analysis. how do you validate that the patterns it finds are real? how do you prevent a hallucinated pattern from becoming the team's working hypothesis? this is the trust problem applied to the diagnostic tool itself.

**the framework debt.** these articles assume you're building on deepagents or a sane agent framework. they don't address the reality that a large fraction of LangSmith users are maintaining LangChain code from 2023 that was designed for tutorials, not production. the advice is good for greenfield projects. the gap for brownfield LangChain codebases is unaddressed.

---

## honest take on the LangChain controversy

the criticism of LangChain as a framework was largely fair. it added abstraction without clarity, made simple things complex, and optimized for tutorial demos over production reliability.

the LangSmith pivot is more defensible. the case that traces are the source of truth for agent systems is solid, and LangSmith addresses a real gap regardless of what framework you use.

the irony: the person who taught the world to build agents with unnecessary abstractions is now selling the tool for debugging those abstractions. that's not dishonest — LangSmith genuinely helps — but the articles never acknowledge it. a reckoning with the LangChain legacy would make the LangSmith case *stronger*, not weaker. "we learned from our mistakes, and here's what we built with that understanding" is more compelling than pretending the framework era didn't happen.

the writing is clear. the technical content is mostly sound. the product direction makes sense. read with the filter on, and extract the genuine insights that stand independent of LangSmith.

---

## articles

| date | article | notes |
|------|---------|-------|
| jan 10 | [why-code-doesnt-document-agent-behavior](why-code-doesnt-document-agent-behavior.md) | foundational thesis. [traces replace code](why-code-doesnt-document-agent-behavior.md#traces-as-the-new-documentation) as source of truth. best standalone piece |
| jan 15 | [what-is-langsmith-agent-builder](what-is-langsmith-agent-builder.md) | memory deep dive. [filesystem-shaped memory](what-is-langsmith-agent-builder.md#how-we-built-our-memory-system), [COALA mapping](what-is-langsmith-agent-builder.md#how-we-built-our-memory-system), [honest learnings](what-is-langsmith-agent-builder.md#learnings-from-building-this-memory-system). most technical |
| jan 20 | [why-you-cant-predict-agent-behavior](why-you-cant-predict-agent-behavior.md) | production analytics. weakest — mostly product pitch. [100k traces, zero analysis](why-you-cant-predict-agent-behavior.md) names a real problem |
| feb 10 | [pattern-1-agent-runs-in-sandbox](pattern-1-agent-runs-in-sandbox.md) | sandbox architecture. [pattern 1 vs 2](pattern-1-agent-runs-in-sandbox.md#choosing-between-patterns). best external contributions. most framework-neutral |

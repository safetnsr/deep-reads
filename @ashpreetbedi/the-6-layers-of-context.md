# The 6 Layers of Context

**Author:** @ashpreetbedi  
**Date:** 2026-02-01T20:30:52.000Z  
**URL:** https://x.com/ashpreetbedi/article/2018059495335764273  
**Images:** 1  

---

OpenAI recently published 

[how they built their internal data agent](https://openai.com/index/inside-our-in-house-data-agent/)

. 6 layers of context, a self-learning memory system, and real lessons from running it in production. One of the best enterprise use-cases for agents I've read.

I've been working on 

[a similar problem](https://www.ashpreetbedi.com/articles/sql-agent)

 for a while and their architecture validates the gpu-poor continuous learning approach I've been testing.

Today I'm open-sourcing my version. It's called Dash.

[Dash](https://github.com/agno-agi/dash)

 is a self-learning data agent that grounds its answers in 6 layers of context and improves with every run.

## The 6 Layers of Context

OpenAI's insight: context is everything. Without it, even strong models hallucinate column names, miss type quirks, and ignore tribal knowledge.

Another problem is that most Text-to-SQL agents are stateless, they make mistakes, you fix them, then they make the same mistake again because every session starts fresh.

Dash fixes this by implementing 6 layers of context:

1. Table Usage: schema, columns, relationships
2. Human Annotations: metrics, definitions, gotchas
3. Query Patterns: SQL that's known to work
4. Institutional Knowledge: external docs, research
5. Memory: error patterns, discovered fixes
6. Runtime Context: live schema when things change

The agent retrieves relevant context at runtime via hybrid search, uses this to generate grounded SQL, then uses the results to deliver insights.

[![](https://pbs.twimg.com/amplify_video_thumb/2018053532503408640/img/2qKNH_V1wxRPsyOa.jpg)](blob:https://x.com/59e263d7-55bf-4720-a684-0b77d16d8634)

0:01 / 0:34

[OpenAI's post](https://openai.com/index/inside-our-in-house-data-agent/)

 goes into more detail about each layer.

## The Self-Learning Loop

Instead of fine-tuning or retraining, Dash learns through two complementary systems:

- Static Knowledge: Validated queries, business context, table schemas, data quality notes, metric definitions, tribal knowledge and gotchas. These are curated by your team and maintained alongside Dash (it also updates successful queries as it comes across them).
- Continuous Learning: Patterns that Dash discovers through trial and error. The more you use Dash, the better it gets. Eg: Columns named `state` in one table map to `status` in another. It also learns what your team is focused on: preparing for an IPO? Dash learns that S-1 metrics live in a separate dataset, that "revenue" means ARR not bookings, and that the board wants cohort retention broken out by enterprise vs SMB. Every learning becomes a data point that improves Dash.

I call this gpu-poor continuous learning (no GPUs are harmed in these experiments) and it's literally 5 lines of code:

python

```
learning=LearningMachine(
    knowledge=data_agent_learnings,
    user_profile=UserProfileConfig(mode=LearningMode.AGENTIC),
    user_memory=UserMemoryConfig(mode=LearningMode.AGENTIC),
    learned_knowledge=LearnedKnowledgeConfig(mode=LearningMode.AGENTIC),
)
```

## Build your own

Follow the 

[README](https://github.com/agno-agi/dash)

 for an in-depth guide. Here's a quick start:

python

```
git clone https://github.com/agno-agi/dash && cd dash
cp example.env .env  # Add OPENAI_API_KEY

docker compose up -d --build

docker exec -it dash-api python -m dash.scripts.load_data
docker exec -it dash-api python -m dash.scripts.load_knowledge
```

This loads sample data (F1 race data from 1950-2020) and the knowledge base (table metadata, validated queries, business rules).

## Connect to the UI

Dash comes with a UI out of the box (via Agno). Use it to interact with Dash, view sessions and traces:

1. Open 

   [os.agno.com](https://os.agno.com/)
2. Add OS → Local → http://localhost:8000
3. Connect

[![](https://pbs.twimg.com/amplify_video_thumb/2018056004307505152/img/rNUnLnheqbansEWh.jpg)](blob:https://x.com/16a79a82-f6b0-4d60-b205-c850f14ed820)

0:01 / 1:04

Try these on the F1 dataset:

shell

```
- Who won the most F1 World Championships?
- How many races has Lewis Hamilton won?
- Compare Ferrari vs Mercedes points 2015-2020
```

## Run evals

Dash ships with an extensive evaluation suite. String matching, LLM grading, and golden SQL comparison. Extend and add your own, this is one of those projects where evals work surprisingly well.

shell

```
docker exec -it dash-api python -m dash.evals.run_evals         # string matching
docker exec -it dash-api python -m dash.evals.run_evals -g      # LLM grader
docker exec -it dash-api python -m dash.evals.run_evals -g -r   # both + golden SQL
```

## Closing thoughts

Data agents are one of the best enterprise use cases for AI right now. Every company (over a certain size) should have one. Vercel has d0, OpenAI built one. Dash is my attempt to make that accessible to everyone.

- GitHub: 

  [github.com/agno-agi/dash](https://github.com/agno-agi/dash)
- OpenAI's post: 

  [Inside OpenAI's In-House Data Agent](https://openai.com/index/inside-our-in-house-data-agent/)
- Previous work: 

  [Self-Improving SQL Agent](https://www.ashpreetbedi.com/articles/sql-agent)

---

## Images

![Image](https://pbs.twimg.com/media/HAGOOgkWYAA5qIX?format=png&name=900x900)


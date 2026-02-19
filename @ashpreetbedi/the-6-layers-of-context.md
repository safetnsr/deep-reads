# The 6 Layers of Context

**Author:** @ashpreetbedi  
**Date:** 2026-02-01T20:30:52.000Z  
**URL:** https://x.com/ashpreetbedi/article/2018059495335764273  
**Images:** 1  

---

The 6 Layers of Context
OpenAI's insight: context is everything. Without it, even strong models hallucinate column names, miss type quirks, and ignore tribal knowledge.
Another problem is that most Text-to-SQL agents are stateless, they make mistakes, you fix them, then they make the same mistake again because every session starts fresh.
Dash fixes this by implementing 6 layers of context:
Table Usage: schema, columns, relationships
Human Annotations: metrics, definitions, gotchas
Query Patterns: SQL that's known to work
Institutional Knowledge: external docs, research
Memory: error patterns, discovered fixes
Runtime Context: live schema when things change
The agent retrieves relevant context at runtime via hybrid search, uses this to generate grounded SQL, then uses the results to deliver insights.
0:01 / 0:34
OpenAI's post goes into more detail about each layer.
The Self-Learning Loop
Instead of fine-tuning or retraining, Dash learns through two complementary systems:
Static Knowledge: Validated queries, business context, table schemas, data quality notes, metric definitions, tribal knowledge and gotchas. These are curated by your team and maintained alongside Dash (it also updates successful queries as it comes across them).
Continuous Learning: Patterns that Dash discovers through trial and error. The more you use Dash, the better it gets. Eg: Columns named `state` in one table map to `status` in another. It also learns what your team is focused on: preparing for an IPO? Dash learns that S-1 metrics live in a separate dataset, that "revenue" means ARR not bookings, and that the board wants cohort retention broken out by enterprise vs SMB. Every learning becomes a data point that improves Dash.
I call this gpu-poor continuous learning (no GPUs are harmed in these experiments) and it's literally 5 lines of code:

```python
learning=LearningMachine(
    knowledge=data_agent_learnings,
    user_profile=UserProfileConfig(mode=LearningMode.AGENTIC),
    user_memory=UserMemoryConfig(mode=LearningMode.AGENTIC),
    learned_knowledge=LearnedKnowledgeConfig(mode=LearningMode.AGENTIC),
)
```

## Build your own

Follow the README for an in-depth guide. Here's a quick start:

```shell
git clone https://github.com/agno-agi/dash && cd dash
cp example.env .env  # Add OPENAI_API_KEY

docker compose up -d --build

docker exec -it dash-api python -m dash.scripts.load_data
docker exec -it dash-api python -m dash.scripts.load_knowledge
```

This loads sample data (F1 race data from 1950-2020) and the knowledge base (table metadata, validated queries, business rules).

## Connect to the UI

Dash comes with a UI out of the box (via Agno). Use it to interact with Dash, view sessions and traces:

Open os.agno.com
Add OS → Local → http://localhost:8000
Connect

Try these on the F1 dataset:

```shell
- Who won the most F1 World Championships?
- How many races has Lewis Hamilton won?
- Compare Ferrari vs Mercedes points 2015-2020
```

## Run evals

Dash ships with an extensive evaluation suite. String matching, LLM grading, and golden SQL comparison. Extend and add your own, this is one of those projects where evals work surprisingly well.

```shell
docker exec -it dash-api python -m dash.evals.run_evals         # string matching
docker exec -it dash-api python -m dash.evals.run_evals -g      # LLM grader
docker exec -it dash-api python -m dash.evals.run_evals -g -r   # both + golden SQL
```
Closing thoughts
Data agents are one of the best enterprise use cases for AI right now. Every company (over a certain size) should have one. Vercel has d0, OpenAI built one. Dash is my attempt to make that accessible to everyone.
GitHub: github.com/agno-agi/dash
OpenAI's post: Inside OpenAI's In-House Data Agent
Previous work: Self-Improving SQL Agent
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/HAGOOgkWYAA5qIX?format=png&name=900x900)


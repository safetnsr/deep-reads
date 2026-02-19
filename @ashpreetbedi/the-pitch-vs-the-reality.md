# The Pitch vs. The Reality

**Author:** @ashpreetbedi  
**Date:** 2026-01-10T02:25:31.000Z  
**URL:** https://x.com/ashpreetbedi/article/2009813827253047675  
**Images:** 1  

---

The Pitch vs. The Reality
Here's what the eval-industrial complex told us:
"Evals are the key to production-ready agents" — Databricks
Here's what actually happens:
You build an agent in a Python script. It works. You run your eval suite. Green lights everywhere. You demo it to stakeholders. They love it. Then you try to ship it: Everything falls apart.
What Evals Don't Test
Here's what evals miss:
Your agent isn't a function. It's a long-running, stateful process interacting with a user.
A single response might take >30 seconds. Or 3 minutes. Or 10 minutes.. Traditional servers handle stateless request-response cycles in milliseconds. Your agent thinks, waits, calls tools, thinks again.
Try fitting that into an eval with a 15-second timeout.
State breaks at scale: Works great with 1 user on 1 container. Add more users? State bleeds across sessions. Add containers, follow the "file system is all you need" trend? State disappears.  Store it in a database? Now you're building infrastructure you didn't plan for.
Streaming is harder than it looks. In your notebook, responses just appeared. In production, users stare at a blank screen for 8 seconds wondering if the app crashed. You try SSE. Then WebSockets. Then you realize you need durable streams that survive network hiccups, handle backpressure, and resume gracefully after disconnects.
The Trap: Evals Too Early
Here's the thing that really kills projects: writing test cases before you have a working product. Every hour spent writing evals is an hour not spent learning what your product actually needs. You're locking yourself into test cases for a system that doesn't exist yet.
The agent you're building now? It's not the one that's going to ship. It's going to be the second iteration. Or the fifth. The eval suite you wrote for version one is useless for version three.
The eval-industrial complex sold you on this idea that evals-first is disciplined. It's not. The right sequence:
Build something that runs
Get it in front of real users (internal users are fine)
Learn what breaks, what matters, what "good" actually looks like
Then write evals to lock in that understanding
You can't evaluate what you can't run.
What Evals Are Actually Good For
I'm not saying evals are useless. They're critical for model providers shipping foundation models. If you're training GPT-5, you need benchmarks. Even for AI engineers building products on top of those models, evals help with:
Catching regressions after you change something
Comparing model versions
Compliance checkboxes
That's it. They won't help you ship. They won't help you scale. They won't help you handle the thousand edge cases that only appear in production.
The Questions That Actually Matter
You have a working agent in a Python script. Great. Now answer these:
Where will it run?
Can it handle 100 concurrent users? 1,000?
What happens when a container crashes mid-conversation?
Is streaming smooth or do users watch a loading spinner for 10 seconds?
Where does the agent's memory live? Who owns it?
How do you deploy updates without breaking active sessions?
Evals don't answer any of these questions. The runtime does.
The Path Forward
I built Agno because I got tired of watching good agents die in the gap between "works in a notebook" and "runs in production"
Agno is a runtime for agents. It handles the stuff evals can't test:
Concurrent execution: thousands of users, isolated state
Persistent storage: sessions survive crashes, memory persists across conversation.
Streaming that works: SSE out of the box, handles disconnects gracefully
Your infrastructure: runs in your cloud, data never leaves your environment
The eval-industrial complex had their shot. Two years. Billions in funding. The production numbers haven't moved.
Maybe it's time to focus on actually shipping.
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G-RJrP3WYAA1hji?format=jpg&name=900x900)


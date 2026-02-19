# The architecture stays the same

**Author:** @ashpreetbedi  
**Date:** 2026-02-12T01:01:38.000Z  
**URL:** https://x.com/ashpreetbedi/article/2021751517376590069  
**Images:** 1  

---

The architecture stays the same

Here's what most people miss about the "AI revolution": the fundamental software architecture will not change.

We'll still have a client - the mobile app, web app, or desktop app - talking to a server. They'll still communicate through API endpoints (the contract between them), and state will still live in a database. Boring!

But while the architecture stays the same, how work gets done on the backend is changing completely.

The static backend is becoming agentic

Two things change when software goes from static to agentic.

The first is the UI. Every product is becoming conversational, but that's just the beginning. The real shift happens when the interface itself is generated from context the agent understands. The UI stops being a fixed screen and starts being a response in itself.

The second is the backend. The static programs behind your API endpoints, the ones that follow a script, query a database, return a result, are being replaced by agents that reason and act. Agents are the new programs.

Not only that, the UI becoming agentic is entirely dependent on the backend becoming agentic. The interface can only be as intelligent as the system powering it. So the real shift, the one everything else depends on, is this new programming model - agents.

When software makes decisions

When the backend was static, every decision was made by a developer in advance. If the user clicks this, do that. If the balance is below zero, reject the transaction. Every path was written. The program just followed the script.

When the backend becomes agentic, the agent starts making decisions on its own. And not all decisions are equal.

Some are easy. Summarize this document. Draft a reply. Look up the answer to a question. The agent handles these without a second thought.

Some need the user. The agent is about to do something that affects you directly: send this email, book this flight, restructure your project. It should pause and ask. "Here's what I'm about to do. Should I proceed?" The user stays in control.

And some need an authority. An agent processing refunds shouldn't approve a $5,000 return on its own. An agent managing infrastructure shouldn't delete a database row without someone signing off. These aren't edge cases, these are everyday operations in real software, and they require explicit approval from someone with the authority to give it.

This is a problem the static backend never had. Static programs don't make decisions - they execute instructions. There's nothing to approve because there's nothing to decide. The moment your backend becomes agentic, you need a system that understands which decisions an agent can make freely, which need the user, and which need an admin.

And that system doesn't exist in the old world. You have to build it.

The Runtime Problem

When programs were static, the runtime wasn't something you thought about. Your backend was a web server running API endpoints. A request came in, a program executed, a response went out. Fast, predictable, done. The infrastructure was mature. Most problems were solved decades ago.

When programs become agents, the runtime becomes a real problem.

Agents take longer to run. A static program queries a database and returns in milliseconds. An agent might reason through multiple steps, call several tools, and take seconds or minutes to produce a result. Your API endpoints need to handle that: streaming partial responses, maintaining connections, recovering gracefully when things take longer than expected.

And because agents make decisions, the runtime takes on a responsibility that static runtimes never had. It's no longer just executing code, it's governing behavior. Which agents can act autonomously? Which actions need confirmation? Which need approval? The runtime has to enforce these boundaries in real time, across every request, for every user.

This is more than an infrastructure problem. It's a philosophical one. The runtime is no longer just the thing that runs your code. It's the thing that runs your programs - programs that think, remember, and decide - and it has to be built for that from the ground up.

The New Software

Software has always been a collection of programs. That hasn't changed. What's changing is the type of programs that make up software.

For decades, every program followed a script. Every path was written in advance. Every decision was made by a developer before the user ever touched the product. Software was static. Powerful, but static.

That era is ending.

The programs behind your favorite products are starting to reason, remember, and decide. The backend is going from static to agentic. And with that shift comes a new set of problems. How do you run software that makes decisions? How do you isolate users? How do you handle approvals? How do you build a runtime for programs that think?

These are the problems worth solving. These are the problems that will define the next generation of software.

Agents are the new programs.

Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/HA6tqguWUAA_Jvy?format=png&name=900x900)


# The Shift

**Author:** @ashpreetbedi  
**Date:** 2026-01-13T23:33:16.000Z  
**URL:** https://x.com/ashpreetbedi/article/2011220028453241218  
**Images:** 1  

---

The Shift
When you're using coding agents, you need to completely change how you work. Claude handles feature design, implementation, testing, and documentation. I just orchestrate.
My new workflow:
Designs in Notion → Designs in Markdown
Humans write code → Claude writes code
Humans write tests → Claude writes tests + cookbooks
Humans review code → Claude reviews, human approves
Context scattered → Context lives in code
I define what to build and why. Claude handles how.
Spec-First Development
To use coding agents well, you need to give them the right context. I follow what I call spec-first development.
I keep a private specs/ repository symlinked into Agno:

```shell
ln -s /path/to/specs /path/to/agno/specs
```

The symlink is gitignored. Invisible to Git, visible to Claude.

Inside, every feature follows the same structure:

```shell
specs/learning-machine/
├── CLAUDE.md           # Instructions for Claude Code
├── design.md           # Design spec
├── implementation.md   # What's done, what's in progress
├── decisions.md        # Why decisions were made
├── prompts.md          # Stored prompts I reuse
└── future-work.md      # What's deferred
```

## Why This Works

design.md is the source of truth. Claude and I align on the details before development starts. No spec, no implementation.

implementation.md tracks progress - what's done, what's blocked. Claude updates this as we go. This is crucial for session continuity: when we hit context limits and need to restart, Claude picks up where it left off.

decisions.md captures the "why". I think people are calling these decision traces now - it's how the agent (and future me) knows why we chose one approach over another. This is also where I weigh in on important calls.

Here's an actual decision record:

```markdown
## ADR-003: Learning Modes (ALWAYS, AGENTIC, PROPOSE)

### Context
Different use cases need different learning behaviors.

### Options
1. Single mode — Always automatic
2. Per-store modes — Each store can operate differently

### Decision
Per-store LearningMode enum.

### Consequences
- Each store declares which modes it supports
- Invalid combinations fall back gracefully
```

prompts.md stores reusable prompts to Re-sync design docs with the codebase. Refine and review. Test after a new implementation. Common tasks become copy-paste.

future-work.md captures what's deferred. Ideas worth revisiting.

I know you might think this is a lot of work to use a coding agent, but Claude Code writes it. I don't write any of it. I barely write any code, do you think I'm gonna write docs?

## Layered Instructions

This is where it gets interesting.

I have CLAUDE.md at the repo level:

```markdown
# CLAUDE.md — Agno

## Code Locations

| What | Where |
|------|-------|
| Core agent code | libs/agno/agno/agent/ |
| Learning | libs/agno/agno/learn/ |
| Tests | libs/agno/tests/ |

## Design Documents

The specs/ folder contains design documents. Always check here first.

## Don't

- Don't implement features without checking for a design doc
- Don't skip async variants of public methods
```

And CLAUDE.md at the feature level:

```markdown
# CLAUDE.md — Learning Machine

## Project Context

Learning Machine is a unified learning system for Agno agents.

## Before Starting Work

1. Read specs/learning-machine/design.md
2. Check specs/learning-machine/implementation.md
3. Look at existing stores for patterns

## Code Patterns

All stores implement the LearningStore protocol.
Use user_profile.py as the reference implementation.

## Don't

- Don't add features not in design.md
- Don't break the LearningStore protocol
```

The root CLAUDE.md tells claude how to navigate the codebase. The feature CLAUDE.md tells claude how to work on this specific thing. The root file stays stable. The feature file evolves with the work.

When I open claude code in the Agno repo and start working on Learning Machine, claude has both sets of instructions.

## The Actual Workflow

Here's how a feature gets built:

1. I badly transcribe a feature using whispr flow
2. Claude reviews the codebase, the `specs/` repo, then writes a design document from my loosely defined idea
3. I review everything. This is where most of my brainpower goes
4. I ask claude to implement a specific piece
5. I ask claude to write cookbooks and tests
6. Review what claude produces, ask it to test and improve
7. Iterate until correct
8. Claude updates implementation.md
9. Move to the next piece

It's very important that claude works on one small pieces at a time. Rule of thumb: every PR should take less than 10 minutes to review. I enforce this in the CLAUDE.md:

```markdown
## The Most Important Rule

**Every PR must be reviewable in under 10 minutes.**

This means:
- Max 5-7 files changed (excluding tests/cookbooks)
- Max 500 lines changed
- One focused change per PR
- Clear review checklist in PR description

If your change is bigger, **split it into multiple PRs**.
```

## Cookbooks or It Didn't Happen

Every pattern needs a runnable example. No exceptions.

When claude implements something, it also writes a cookbook:

```shell
python cookbook/15_learning/01_basics/1a_user_profile_always.py
```

If the cookbook doesn't run, the implementation isn't done. I don't care how clean the code looks.

The Learning Machine has 26 cookbook files across 8 folders. Each one tests a specific pattern. Every single one runs. That's how I know it works.

The fun part is that claude runs the cookbooks, and stores the results in TESTING.md. You think I'm kidding, check this out.

## Model Choice

Opus 4.5. iykyk.

## Plan Mode. Use It.

Shift + Tab. Plan mode.

The Agno codebase is pretty complex and every time I've jumped straight into implementation, the output has been dogshit.

I prefer to front-load information. Architecture decisions, edge cases, constraints. All of it. Five minutes of planning has saved me hours. If you're concerned about typing this out, just transcribe it. I barely type anymore.

## TLDR

- Use Spec files. Symlink your specs folder in your codebase. design.md, implementation.md, decisions.md, prompts.md. Claude writes them.
- CLAUDE.md at two levels. Root level for the codebase, feature level for specific work.
- Save your prompts. Common tasks become copy-paste. Too much typing? You guessed it, claude writes the prompts too. Keep up.
- Cookbooks for everything. If it doesn't run, it's not done.
- Context degrades at 30%. One conversation per feature. External memory via spec files. Clear when needed.

## Opus 4.5 is AGI

When I say claude writes 100% of my code, I mean it. I still review every line. I'm just not the one typing.
Ashpreet Bedi

---

## Images

![Image](https://pbs.twimg.com/media/G-j6Gy7WMAAc5c9?format=png&name=900x900)


# 1. Understanding Foundation Models

**Author:** @manthanguptaa  
**Date:** 2026-02-02T12:17:32.000Z  
**URL:** https://x.com/manthanguptaa/article/2018297734995075200  
**Images:** 1  

---

AI Engineering has emerged as a distinct discipline, separate from traditional Machine Learning Engineering. While ML engineers train models from scratch, AI engineers build applications on top of foundation models. This shift fundamentally changes what you need to learn.

## 1. Understanding Foundation Models

Foundation models (GPT-5.2, Claude, Gemini, Llama) are the building blocks of modern AI applications. Unlike traditional ML, where you train models from scratch, AI engineering leverages pre-trained models. Understanding their capabilities, limitations, tokenization, context windows, and pricing is fundamental to building cost-effective applications.

Project: Model Comparison Notebook

Create a simple Python notebook that sends the same 10 prompts to different models (use free tiers: Gemini API, Groq for Llama, OpenAI playground) and compare responses side by side. Document differences in quality, speed, and style. No infrastructure needed, just API keys and a Jupyter notebook.

## 2. Prompt Engineering

Your prompts are your “code” in AI engineering. The difference between a mediocre and excellent AI application often comes down to prompt design. Techniques like few-shot learning, chain-of-thought, and structured outputs can dramatically improve results without any model training.

Project: Prompt Experiments Spreadsheet

Pick one task (e.g., “summarize this article”). Write 5 different prompts: zero-shot, few-shot, chain-of-thought, persona based, and structured output. Test each on 10 examples and score results in a spreadsheet. You will see firsthand which techniques work best.

## 3. Retrieval Augmented Generation (RAG)

LLMs have knowledge cutoffs and hallucinate. RAG grounds them in your data. It’s the most common pattern for building production AI applications, from customer support bots to internal knowledge assistants. Understanding chunking strategies, embedding models, vector databases, and retrieval metrics is essential.

Project: Chat With Your Notes

Build a simple RAG app over 5-10 of your markdown notes or text files. Use any agentic framework with ChromaDB (runs locally, no setup). Split documents into chunks, embed them, and query with natural language. Start simple, and you can add complexity later. One Python file, ~50 lines of code.

## 4. Evaluation and Testing

“Vibes based evaluation” doesn’t scale. You need systematic ways to measure if your AI application is improving. This includes building eval datasets, choosing metrics (accuracy, helpfulness, safety), running A/B tests, and detecting regressions. Without good evals, you are flying blind.

Project: Simple Eval Script

Create a JSON file with 20 question-answer pairs for your use case. Write a Python script that runs your prompt against each question, compares outputs to expected answers (exact match or LLM-as-judge), and prints a score. Run it every time you change your prompt. That’s it, no CI/CD needed to start.

## 5. Agents and Tool Use

Agents extend LLMs from text generators to action takers. They can browse the web, execute code, query databases, and call APIs. Understanding agent architectures (ReAct, function calling, planning), tool design, and failure modes is critical for building autonomous AI systems.

Project: Calculator Agent

Build an agent that can answer math questions by calling a calculator tool. Use OpenAI function calling or any agentic framework. Give the LLM access to add, subtract, multiply, and divide functions. Ask it, “What’s 15% tip on $47.50?” and watch it break down the problem and call tools. Simple, visual, and teaches the core concept.

## 6. Structured Outputs and Data Extraction

Real applications need structured data (JSON, SQL, API calls), not free form text. Techniques like JSON mode, function calling, and constrained generation ensure LLM outputs integrate with downstream systems. This bridges the gap between conversational AI and software engineering.

Project: Recipe Extractor

Paste a recipe from any website into your app and extract structured JSON: ingredients (with quantities), steps, prep time, cook time. Use OpenAI’s structure output. Input: messy text. Output: clean, usable data structure. Great for understanding how LLMs can replace regex nightmares.

## 7. Guardrails and Safety

AI applications can be jailbroken, produce harmful content, or leak sensitive information. Implementing input/output guardrails, PII detection, content filtering, and adversarial testing is essential for production deployment, especially in regulated industries.

Project: Input/Output Filter

Wrap any chatbot you have built with simple guards. Input: check for prompt injection attempts (“ignore previous instructions…”) using keyword matching or a classifier. Output: check if the response contains any words from a blocklist. Log flagged messages. Simple if-statements teach the concept before you reach for libraries like Guardrails AI.

## 8. Observability and Monitoring

You can’t improve what you can’t measure. Production AI systems need logging, tracing, cost tracking, quality monitoring, and alerting. Understanding tools like LangSmith, Weights & Biases, and custom dashboards helps you debug issues and optimize over time.

Project: LLM Call Logger

Add logging to any LLM app: timestamp, prompt, response, latency, token count, estimated cost. Write to a JSON file or SQLite database. After a week of usage, analyze: What are your most common queries? Slowest responses? Most expensive calls? A CSV and some pandas go a long way before you need fancy tools, or just try out the tools like LangFuse, Weights & Biases, etc.

## 9. AI System Architecture

Real AI applications combine multiple components, retrievers, models, guardrails, caches, and databases. Understanding patterns like the “compound AI system” helps you design maintainable, testable, and scalable architectures.

Project: Personal Assistant Bot

Combine what you have learned into one app: a Telegram/Discord bot that answers questions about a topic you care about. Include RAG over your documents, structured output for actions (e.g., “add to my todo list” -> JSON), basic input validation, and logging. Deploy free on Railway or Render. You now have a portfolio project that demonstrates real skills.

If you found this roadmap helpful, share it on 

[Twitter](https://twitter.com/manthanguptaa)

, or 

[LinkedIn](https://www.linkedin.com/in/manthanguptaa/)

. Have questions? Reach out at guptaamanthan01[at]gmail[dot]com.

---

## Images

![Image](https://pbs.twimg.com/media/HAJutFAaIAAYwJ4?format=jpg&name=900x900)


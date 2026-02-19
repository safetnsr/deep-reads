# @manthanguptaa

**software engineer, technical writer. blogs at [manthanguptaa.in](https://manthanguptaa.in)**

10 articles, published dec 2025 - jan 2026. a mix of deep technical explainers and reverse-engineered system analyses. not building a product — teaching how things work.

---

## the good

**he's an educator, not a salesman.** every article exists to explain something, not to sell something. that's refreshing in AI twitter. there's no product pitch at the end, no "and that's why you should use our framework." just "here's how this works, here's the code, here are the references."

**the tokenization article is outstanding.** 17K+ chars walking through BPE, WordPiece, and Unigram from first principles. builds a tokenizer from scratch, shows real benchmarks, explains why LLMs can't do arithmetic (it's the tokenizer, not the model). code examples that actually run with real output. if you want to understand tokenization, this is the article.

**he reverse-engineers systems better than their own docs.** the Clawdbot (OpenClaw) memory analysis is more detailed and clearer than the official documentation. he traces the full lifecycle: how context is built, how memory gets indexed (sqlite-vec + FTS5 hybrid search), how compaction works, how the pre-compaction memory flush prevents data loss. the ChatGPT memory analysis does the same for OpenAI's approach.

**the LLM-as-judge article is practical and honest.** covers all four evaluation modes (pointwise, reference-based, pairwise, listwise), names the real biases (position, verbosity, self-enhancement, authority, format), and gives production-ready optimization patterns. the de-biased pairwise comparison technique (run twice with swapped positions) is immediately usable.

**he shows his work.** code → output → explanation. not "here's some pseudocode that might work." actual Python with actual output pasted below it. this is how technical writing should be done.

## the bad

**some articles are chapters, not articles.** "methodology," "sampling-controls," "the-rubric-cost-latency-and-accuracy," and "conclusion" are clearly sections of one longer piece that got split for engagement metrics. each one is too short to stand alone and too dependent on the others to make sense independently. should have been published as one comprehensive post.

**no original systems.** every article analyzes something someone else built — ChatGPT's memory, OpenClaw's context system, how tokenizers work, how evaluation frameworks work. the analysis is excellent, but there's no "here's what I built and what I learned." the closest is "why do current memory systems fail," but even that stops at diagnosis without proposing a solution.

**the memory failure article promises more than it delivers.** it correctly identifies problems with current approaches (RAG hallucination, context window limits, summarization loss) but doesn't go deep enough on any of them. each failure mode gets a paragraph when it deserves a section. compared to the tokenization article where every concept gets a thorough treatment, this feels rushed.

**"1-understanding-foundation-models" is generic.** it reads like the intro chapter of an ML textbook. transformer architecture, attention mechanisms, pre-training vs fine-tuning — all correct but nothing you can't find in a dozen other places. his strength is detailed system analysis, not survey content.

## what you can learn from this

1. **hybrid search is better than vector-only.** the OpenClaw analysis shows why: `finalScore = (0.7 * vectorScore) + (0.3 * textScore)`. semantic search catches meaning, BM25 catches exact terms (names, IDs, dates). both miss things the other catches.

2. **pre-compaction memory flush.** before summarizing a long conversation, write important facts to disk first. compaction is lossy — don't lose data you haven't saved yet. simple pattern, easy to miss.

3. **de-biased pairwise evaluation.** run every A/B comparison twice with positions swapped. if A wins both times, it's genuinely better. if results flip, call it a tie. doubles cost, eliminates position bias.

4. **tokenization determines model capabilities.** LLMs can't do arithmetic because "317+492" tokenizes inconsistently. they can't spell backwards because "hello" is one atomic token. if your model is failing at something, check the tokenizer first.

5. **context ≠ memory.** context is what the model sees right now (ephemeral, expensive, bounded). memory is what's on disk (persistent, cheap, searchable). the two need different strategies.

6. **chunking parameters matter.** 400 tokens per chunk with 80 token overlap balances granularity vs coherence. too small = lost context. too large = diluted relevance. overlap prevents facts at boundaries from being missed.

## what's missing

- **original work.** great at analyzing other people's systems, but where's his? the best technical writers build things and then explain what they learned. he's doing the second half well but skipping the first.
- **opinions.** the analyses are thorough but neutral. he rarely says "this is wrong" or "this should be different." the LLM-as-judge article comes closest with practical recommendations, but even there it feels like documentation rather than argument.
- **depth on his own ideas.** the memory failure article hints at original thinking but doesn't develop it. what would a better memory system look like? he clearly has thoughts — would be more interesting than another system walkthrough.

---

## articles

### system analyses
| date | article | size | notes |
|------|---------|------|-------|
| jan 26 | [how-context-is-built](how-context-is-built.md) | 18K | best piece. deep dive into OpenClaw memory: indexing, search, compaction, flush. clearer than the official docs |
| jan 9 | [chatgpts-context-structure](chatgpts-context-structure.md) | 8K | reverse-engineers ChatGPT's context window. solid technical analysis |
| jan 3 | [why-do-current-memory-systems-fail](why-do-current-memory-systems-fail.md) | 6K | good diagnosis of RAG/context/summarization failures. stops short of solutions |

### deep technical explainers
| date | article | size | notes |
|------|---------|------|-------|
| jan 17 | [what-is-tokenization](what-is-tokenization.md) | 28K | outstanding. BPE/WordPiece/Unigram from scratch. code that works, real benchmarks, practical implications |
| dec 31 | [why-traditional-evaluation-methods-fail](why-traditional-evaluation-methods-fail.md) | 16K | LLM-as-judge comprehensive guide. biases, modes, production optimizations. practical and honest |

### series fragments (should be one article)
| date | article | size | notes |
|------|---------|------|-------|
| — | [1-understanding-foundation-models](1-understanding-foundation-models.md) | 4K | generic intro. transformer basics, nothing novel |
| — | [methodology](methodology.md) | 3K | evaluation methodology fragment |
| — | [sampling-controls](sampling-controls.md) | 3K | temperature, top-p, etc. textbook content |
| — | [the-rubric-cost-latency-and-accuracy](the-rubric-cost-latency-and-accuracy.md) | 4K | eval rubric framework. decent but needs the other parts |
| — | [conclusion](conclusion.md) | 2K | concluding fragment |

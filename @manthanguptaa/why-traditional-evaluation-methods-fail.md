# Why Traditional Evaluation Methods Fail

**Author:** @manthanguptaa  
**Date:** 2025-12-31T04:32:56.000Z  
**URL:** https://x.com/manthanguptaa/article/2006222014265393316  
**Images:** 1  

---

> TL;DR: LLM as a judge works well when you (1) use reference based evaluation over pointwise scoring, (2) debias pairwise comparisons by swapping positions, (3) ensemble across multiple models, (4) require reasoning before scores, and (5) calibrate against human judgments. Skip it when ground truth exists or for safety critical decisions.

Last month, I ran thousands of evals on an agentic system I was building. The responses were open-ended, context dependent, and impossible to validate with simple string matching. I tried exact match comparisons, they failed miserably. I tried BLEU scores, they penalized semantically correct but differently worded responses. I was stuck! Traditional metrics could not handle the nuance I needed, and manual evaluation was too slow and expensive.

That is when I moved to LLM as a judge: using language models to evaluate the outputs of another. It is fast, cheap, and surprisingly effective, but it comes with its own set of pitfalls that most people don't talk about.

In this article, I will walk you through the different ways to use LLMs as judges, the hidden biases you need to watch out for, and the optimizations I have learned from running thousands of evals in production.

# Why Traditional Evaluation Methods Fail

Before diving into LLM as a judge, it is worth understanding why we need it in the first place. Traditional evaluation methods were designed for a different era of software engineering, where outputs were predictable and constrained.

Exact Match works great for classification tasks or structured outputs, but fails completely for open-ended generation. "The capital of France is Paris" and "Paris is France's capital city" are semantically identical but would score 0% on exact match.

BLEU and ROUGE were designed for machine translation and summarization. They measure n-gram overlap between the generated and reference text. But they penalize valid paraphrasing and reward surface level similarity over actual correctness. A response could be factually wrong but score highly because it uses similar words.

Human Evaluation is the gold standard, but it does not scale. It is expensive (easily $1-5 per evaluation), slow (days to weeks for large datasets), and surprisingly inconsistent (inter-annotator agreement is often below 80%). When you need to evaluate thousands of responses during development iteration, human eval becomes a bottleneck.

LLM as a judge offers a middle ground: the nuanced understanding of human evaluation at the speed and cost of automated metrics. It is not perfect, but for most use cases, it gets you 80% of the accuracy at 1% of the cost.

# Evaluation Modes

There are four primary ways to use an LLM as a judge. Each has its strengths and ideal use cases:

• Pointwise: Score single response (quality, moderation)

• Reference-based: Compare to gold standard (accuracy, regression)

• Pairwise: Pick better of two (A/B testing, RLHF)

• Listwise: Rank multiple (leaderboards, best-of-N)

## 1. Pointwise Evaluation

Pointwise evaluation scores a single response based on the question alone, without any reference to compare against. The LLM judge assesses quality on dimensions like correctness, completeness, and helpfulness.

```
You are an expert evaluator. Assess the following response based on the question asked.

## Evaluation Criteria
- Correctness: Is the information factually accurate?
- Completeness: Does it fully address the question?
- Helpfulness: Is it clear and actionable?

## Scoring
Rate the response from 1-5 where:
1 = Poor, 2 = Below Average, 3 = Average, 4 = Good, 5 = Excellent

Question: {question}
Response: {response}

Provide your reasoning first, then the final score.
```

## 2. Reference based Evaluation

Reference based evaluation compares the generated response against a known good "gold standard" answer. This is particularly useful when you have ground truth data and want to measure how close your model's outputs are to the ideal answer.

```
You are an expert evaluator. Compare the following response against the reference answer.

## Task
Evaluate how well the response matches the reference in terms of:
- Factual alignment: Does it convey the same information?
- Completeness: Does it cover all key points from the reference?
- Accuracy: Are there any contradictions or errors?

## Scoring
Rate from 1-5 where:
1 = Completely misaligned, 2 = Mostly incorrect, 3 = Partially correct,
4 = Mostly correct, 5 = Fully aligned with reference

Question: {question}
Reference Answer: {reference}
Response to Evaluate: {response}

Provide your reasoning first, then the final score.
```

## 3. Pairwise Comparison

Pairwise comparison presents two responses side-by-side and asks the judge to pick the better one. This is widely used for model comparison, RLHF preference data collection, and A/B testing. Platforms like Chatbot Arena use this approach extensively.

```
You are an expert evaluator. Compare the two responses below and determine which one better answers the question.

## Evaluation Criteria
Consider: accuracy, completeness, clarity, and helpfulness.

Question: {question}
Response A: {response_a}
Response B: {response_b}

## Instructions
1. Analyze both responses against the criteria
2. Explain the strengths and weaknesses of each
3. Declare a winner: A, B, or Tie

Your analysis:
```

Important caveat: Pairwise comparison is susceptible to position bias. LLMs tend to favor responses in certain positions (often the first one). I cover how to mitigate this in the Optimizations section.

## 4. Listwise Ranking

Listwise evaluation extends pairwise to multiple responses, asking the judge to rank them from best to worst. This is useful for leaderboard generation or selecting the best response from multiple candidates.

```
You are an expert evaluator. Rank the following responses from best to worst.

Question: {question}
Response 1: {response_1}
Response 2: {response_2}
Response 3: {response_3}

## Instructions
Rank all responses from best (1st) to worst (last).
Provide brief reasoning for your ranking.

Ranking:
```

These four modes cover most evaluation scenarios. You can also create variations like multi-dimensional pointwise scoring where you rate correctness, completeness, and style separately, or binary reference comparison that outputs true/false for semantic equivalence.

# Crafting Effective Judge Prompts

The quality of your judge prompt directly impacts the reliability of your evaluations. A well structured judge prompt should have four key components:

1. Clear Task Definition

State exactly what the model should evaluate. Vague instructions like "rate this response" lead to inconsistent results. Be specific, "Evaluate the factual accuracy of this response compared to the reference answer."

2. Detailed Evaluation Criteria

Define what "good" means. Do not just say "correctness", specify what correctness entails. For example:

- Correctness: All factual claims are accurate and verifiable
- Completeness: Addresses all parts of the question without omissions
- Clarity: Well organized, easy to understand, free of jargon

3. Explicit Scoring Rubric

Provide clear definitions for each score level. Without this, a "3 out of 5" from one run might mean something different from another run.

```
1 = Completely incorrect or irrelevant
2 = Mostly incorrect with minor valid points
3 = Partially correct but missing key information
4 = Mostly correct with minor issues
5 = Fully correct and comprehensive
```

4. Few-shot Examples

Including 2-3 examples of scored responses significantly improves consistency. Show the model what a "2" looks like versus a "4" for your specific criteria.

## A Production Ready Judge Prompt

Here is a comprehensive prompt template that incorporates all these principles:

```
You are an expert evaluator assessing the quality of an AI assistant's response.

## Your Task
Evaluate the response below against the reference answer. Your evaluation should be thorough, fair, and consistent.

## Evaluation Criteria

### Correctness (0-2 points)
- 0: Contains factual errors or contradicts the reference
- 1: Partially correct but missing key facts or contains minor inaccuracies
- 2: Factually accurate and aligned with the reference

### Completeness (0-2 points)
- 0: Misses most key points from the reference
- 1: Covers some key points but has significant gaps
- 2: Addresses all major points from the reference

### Clarity (0-1 point)
- 0: Confusing, poorly organized, or difficult to understand
- 1: Clear, well-structured, and easy to follow

## Examples

### Example 1: Score 5/5
Question: What causes seasons on Earth?
Reference: Earth's axial tilt of 23.5 degrees causes seasons. As Earth orbits the Sun, different hemispheres receive more direct sunlight at different times, creating summer and winter.
Response: Seasons occur because Earth's axis is tilted at about 23.5 degrees. This means as our planet orbits the Sun throughout the year, the Northern and Southern hemispheres take turns being tilted toward the Sun, receiving more direct sunlight and experiencing summer.
Analysis: Fully correct (mentions axial tilt and its effect), complete (covers the mechanism), and clearly written.

### Example 2: Score 2/5
Question: What causes seasons on Earth?
Reference: [same as above]
Response: Seasons happen because Earth gets closer to and farther from the Sun during its orbit.
Analysis: Incorrect (this is a common misconception—Earth's distance from the Sun does not cause seasons), incomplete (does not mention axial tilt), though clearly written.

## Input
Question: {question}
Reference Answer: {reference}
Response to Evaluate: {response}

## Instructions
1. Carefully compare the response against the reference answer
2. Score each criterion according to the rubric above
3. Provide specific evidence for each score
4. Calculate the total score (out of 5)

## Output Format

### Analysis
[Your detailed analysis with specific quotes and comparisons]

### Scores
- Correctness: X/2 - [brief justification]
- Completeness: X/2 - [brief justification]
- Clarity: X/1 - [brief justification]

### Total Score: X/5
```

This prompt works well because it:

- Defines exactly what each score means (no ambiguity)
- Shows concrete examples of high and low scores
- Forces reasoning before scoring
- Uses a structured output format for easy parsing

## Additional Tips

Prefer discrete over continuous scoring. LLMs handle classification better than regression. Use scales like 1-5 or 1-10 rather than continuous ranges like 0.0 to 1.0. They tend to cluster around certain values with continuous scales.

Set temperature to 0 or near-zero. For evaluation tasks, you want deterministic outputs. Temperature 0 eliminates randomness. Some practitioners use 0.1-0.2 for slight variation while maintaining consistency, but for production evals, stick with 0.

Ask for reasoning before the score. This is crucial. When you ask the model to explain its reasoning first, it is forced to think through the evaluation systematically rather than jumping to a number. This reduces arbitrary scoring and makes the evaluation more transparent and debuggable.

# Limitations and Biases

LLM as a judge has become the de facto approach for evaluating agentic systems, but it is far from perfect. Understanding its limitations is crucial for building reliable evaluation pipelines.

## Inconsistency

Even with the temperature set to 0, LLM judges can produce different scores for identical inputs across runs. This happens due to floating-point non-determinism, API side variations, and model updates. The same response might score 4/5 today and 3/5 tomorrow.

This inconsistency compounds when you are trying to detect small improvements between model versions. A 0.1 point improvement in average score might be noise rather than signal.

## Criteria Ambiguity

When you ask an LLM to evaluate "correctness", what does that mean exactly? Each model has its own implicit definition shaped by its training data. GPT-5.2 might interpret correctness differently than Claude Sonnet, leading to systematic disagreements.

Even established eval frameworks like Ragas and DeepEval use different prompts for the same criteria, making it hard to compare results across tools.

## Known Biases

Research has identified several systematic biases in LLM judges:

Position Bias: In pairwise comparisons, LLMs tend to favor responses in certain positions, often the first one. Studies show this can swing results by 10-20%.

Verbosity Bias: Longer responses tend to score higher regardless of actual quality. A verbose but mediocre answer often beats a concise but correct one.

Self-Enhancement Bias: Models prefer outputs that match their own style. GPT-5.2 tends to rate GPT-5.2 outputs higher than Claude Sonnet outputs, and vice versa.

Authority Bias: Confident, assertive responses score higher than hedged, nuanced ones, even when the hedging is appropriate.

Format Bias: Well-formatted responses with markdown, bullet points, and clear structure receive higher scores than plain text with identical content.

## Cost

Using frontier models like GPT-5.2 or Claude Opus as judges gets expensive at scale. If you are evaluating 10,000 responses at $0.01-0.03 per evaluation, that is $100-300 per eval run. During rapid iteration, this adds up quickly.

You can use cheaper models, but their judgment quality degrades, especially for nuanced criteria like reasoning quality or factual accuracy.

# When NOT to Use LLM as a judge

LLM as a judge is powerful, but it is not the right tool for every situation. Here are some scenarios when you should avoid it:

## When Ground Truth Exists

If you can compute an exact answer, do that instead. For math problems with definite solutions, code that either runs or does not, or structured outputs that can be validated programmatically, use deterministic evaluation. It is faster, cheaper, and 100% reliable.

python

```
# Do not use LLM as a judge for this
def evaluate_math(question, response, expected):
        # Just check if the answer is correct
        return extract_number(response) == expected
```

## Safety-Critical Evaluations

For content moderation, toxicity detection, or any evaluation where mistakes have serious consequences, do not rely solely on LLM judges. Their biases and inconsistencies make them unsuitable as the final arbiter for safety decisions. Use them as one signal among many, with human review for edge cases.

## Domain Expertise Requirements

LLM judges lack deep domain knowledge. For evaluating medical diagnoses, legal advice, or specialized technical content, they may confidently score incorrect responses highly. In these domains, expert human evaluation remains necessary at least for validation or you can finetune a model on the domain data to improve its performance and use it as a judge.

## When Explainability is Required

In regulated industries or high stakes decisions, you may need to explain exactly why a response was scored a certain way. LLM reasoning, while helpful, is not auditable in the same way as rule-based systems. If you need to defend your evaluation methodology in court or to regulators, LLM as a judge may not suffice.

## Low-Stakes, High-Volume Scenarios

If you are evaluating millions of responses and accuracy matters less than throughput, simpler heuristics might be more cost-effective. A well tuned classifier or embedding similarity score can handle high volume at a fraction of the cost.

## The Hybrid Approach

In practice, the best evaluation pipelines combine multiple methods:

1. Deterministic checks first: Validate format, structure, and any computable criteria
2. LLM as a judge for nuance: Use for subjective qualities like helpfulness and clarity
3. Human review for calibration: Sample 5-10% for expert evaluation to validate your LLM judge
4. Escalation for edge cases: Flag low-confidence or borderline scores for human review

This layered approach gives you the best of all worlds, speed and scale from automation, nuance from LLM judges, and reliability from human oversight.

# Optimizations

These are techniques I have found effective from running LLM as a judge in production across thousands of evaluations:

## 1. Ensemble Judging

Use multiple LLMs to evaluate each response and aggregate their scores. This reduces the impact of any single model's biases and improves consistency.

python

```
def ensemble_evaluate(question, response):
    scores = {
        'claude-opus': evaluate_with_claude_opus(question, response),
        'gpt5_2': evaluate_with_gpt5_2(question, response),
        'gemini': evaluate_with_gemini(question, response)
    }
    # Weighted average
    weights = {'claude-opus': 0.4, 'gpt5.2': 0.4, 'gemini': 0.2}
    return sum(scores[m] * weights[m] for m in scores)
```

Yes, this triples your cost, but for high-stakes evaluations, the improved reliability is worth it.

## 2. Down-Weight the Generator Model

If Claude generated the response, give Claude's judgment less weight in the ensemble. This mitigates self-enhancement bias.

python

```
def get_weights(generator_model):
    base_weights = {'claude-opus': 0.33, 'gpt5_2': 0.33, 'gemini': 0.33}
    if generator_model in base_weights:
        # Reduce generator's weight, redistribute to others
        base_weights[generator_model] = 0.15
        others = [m for m in base_weights if m != generator_model]
        for m in others:
            base_weights[m] = 0.425
    return base_weights
```

## 3. Use Reference Responses When Possible

Pointwise evaluation (scoring in isolation) is inherently less reliable than reference based evaluation. When you have ground truth or expected answers, always include them. This gives the judge a concrete standard to compare against rather than relying on its own interpretation of "good."

## 4. De-Bias Pairwise Comparisons

Position bias is real and measurable. To mitigate it, run each pairwise comparison twice with swapped positions:

python

```
def debiased_pairwise(question, response_a, response_b):
    # Run A vs B
    result_ab = judge(question, response_a, response_b)
    # Run B vs A (swapped)
    result_ba = judge(question, response_b, response_a)
    
    # Aggregate
    if result_ab == "A" and result_ba == "B":
        return "A"  # A wins in both positions
    elif result_ab == "B" and result_ba == "A":
        return "B"  # B wins in both positions
    else:
        return "Tie"  # Inconsistent results = no clear winner
```

This doubles your cost, but eliminates position bias entirely.

## 5. Require Reasoning Before Scores

Always structure your prompt to elicit reasoning before the final score. This is not just for transparency, it genuinely improves evaluation quality by forcing the model to think through the assessment systematically.

```
## Output Format
First, provide your detailed analysis of the response.
Then, based on your analysis, provide the final score.

Analysis: [Your reasoning here]
Score: [X/5]
```

## 6. Calibrate Against Human Judgments

Before trusting your LLM judge in production, validate it against human annotations. Take a sample of 100-200 responses, have humans score them, then compare to your LLM judge's scores.

Calculate metrics like:

- Correlation: Does the LLM's ranking match human ranking?
- Cohen's Kappa: How often does the LLM agree with humans beyond chance?
- Systematic Bias: Does the LLM consistently score higher or lower than humans?

If your LLM judge disagrees with humans more than 20-30% of the time, revisit your prompt or criteria.

# Conclusion

Building reliable evaluation pipelines for agentic systems requires understanding both the power and limitations of LLM as a judge. After running thousands of evaluations in production, here are the key lessons that will save you time and improve your results:

1. Never trust a single judge. Whether it is position bias, self-enhancement bias, or simple inconsistency, single-model evaluation is inherently unreliable. Ensemble across models, de-bias pairwise comparisons, and always validate against human judgments.
2. Reference responses are your anchor. Pointwise evaluation ("rate this response 1-5") is the least reliable mode. When you have ground truth, use it. Reference based evaluation grounds the LLM in something concrete rather than its own subjective interpretation of "good."
3. Design for transparency. Require reasoning before scores. Log everything. When an evaluation seems wrong, you need to understand why. Black-box scores are useless for debugging.
4. Build a hybrid pipeline. Deterministic checks first, LLM judges for nuance, human review for calibration and edge cases. No single method is sufficient.
5. Calibrate continuously. Your LLM judge will drift as models update and your use cases evolve. Regular comparison against human judgments is not optional, it is the only way to know if your evaluations still mean anything.

LLM as a judge will not replace human evaluation entirely. But implemented correctly, it gets you 80% of the accuracy at 1% of the cost, which is often exactly the tradeoff you need during rapid development.

The teams that struggle with LLM as a judge are the ones who treat it as a black box. The teams that succeed are the ones who understand its failure modes and design around them.

# References

1. Chip Huyen, \*AI Engineering: Building Applications with Foundation Models\*, O'Reilly Media, 2025.

2. Zheng, L., et al. "

[Judging LLM as a judge with MT-Bench and Chatbot Arena.](https://arxiv.org/abs/2306.05685)

" \*NeurIPS\*, 2023.

3. Wang, P., et al. "

[Large Language Models are not Fair Evaluators.](https://arxiv.org/abs/2305.17926)

" \*arXiv\*, 2023.

4. Dubois, Y., et al. "

[AlpacaFarm: A Simulation Framework for Methods that Learn from Human Feedback.](https://arxiv.org/abs/2305.14387)

" \*NeurIPS\*, 2023.

5. Liu, Y., et al. "

[G-Eval: NLG Evaluation using GPT-4 with Better Human Alignment.](https://arxiv.org/abs/2303.16634)

" \*EMNLP\*, 2023.

You can read more such pieces from me on 

<https://manthanguptaa.in/>

---

## Images

![Image](https://pbs.twimg.com/media/G9aihiDagAA9a8g?format=jpg&name=900x900)


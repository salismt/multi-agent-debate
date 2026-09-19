# Multi-Agent Debate — Research Basis

Every number below is copied from the cited paper's own reported results. They measure **the published techniques this protocol composes**, evaluated on benchmarks — not this skill, which has not been independently benchmarked. Treat them as the reason each step is in the protocol, not as a performance claim for the skill itself.

## Core: debate beats a single confident pass

**Du et al., 2023** — *Improving Factuality and Reasoning in Language Models through Multiagent Debate* — https://arxiv.org/abs/2305.14325

Multiple model instances propose and debate over rounds. 3 agents, 2 rounds, ChatGPT.

| Method | Arithmetic (%) | Grade School Math (%) | Chess (ΔPS) |
|---|---|---|---|
| Single Agent | 67.0 ±4.7 | 77.0 ±4.2 | 91.4 ±10.6 |
| Single Agent (Reflection) | 72.1 ±4.5 | 75.0 ±4.3 | 102.1 ±11.9 |
| Multi-Agent (Majority vote) | 69.0 ±4.6 | 81.0 ±3.9 | 102.2 ±6.2 |
| **Multi-Agent (Debate)** | **81.8 ±2.3** | **85.0 ±3.5** | **122.9 ±7.6** |

| Method | Biographies | MMLU | Chess Move Validity |
|---|---|---|---|
| Single Agent | 66.0 ±2.2 | 63.9 ±4.8 | 29.3 ±2.6 |
| Single Agent (Reflection) | 68.3 ±2.9 | **57.7 ±5.0** | 38.8 ±2.9 |
| **Multi-Agent (Debate)** | **73.8 ±2.3** | **71.1 ±4.6** | **45.2 ±2.9** |

Two results drive protocol design:
- Self-reflection *degraded* MMLU accuracy (63.9 → 57.7) while debate raised it to 71.1. A model critiquing itself in one context is not a substitute for an opposed second party.
- Majority vote trailed adjudicated debate on every task (e.g. 81.0 vs 85.0 on grade school math). This is why step 3 is **judge, don't average**.

**Liang et al., EMNLP 2024** — *Encouraging Divergent Thinking in LLMs through Multi-Agent Debate* — https://arxiv.org/abs/2305.19118

Names the **Degeneration-of-Thought** failure: once a model is confident, reflection cannot generate genuinely novel thoughts even when the initial stance is wrong. Fixed with "tit for tat" agents plus a managing judge. Also reports that *adaptive break* and a *modest* level of tit-for-tat are required — maximal adversarialism is not optimal, which is why the protocol bounds rounds and stops when no new objection appears. Finds **LLMs may not be a fair judge when different LLMs are used for agents** — the basis for the judge rule in Model Assignment.

## Verification and refinement

**Dhuliawala et al., 2023** — *Chain-of-Verification Reduces Hallucination* — https://arxiv.org/abs/2309.11495

Draft → plan verification questions → answer them independently → revise. On Llama 65B:
- Wikidata list task precision **0.17 → 0.36** (more than doubled)
- MultiSpanQA F1 **0.39 → 0.48** (+23%)
- Longform biographies FactScore **55.9 → 71.4** (+28%)
- Answering verification questions *independently* mattered: factored 63.7 → factor+revise 71.4

**Madaan et al., 2023** — *Self-Refine* — https://arxiv.org/abs/2303.17651
Iterative self-feedback then revision, no training required. Across 7 tasks on GPT-3.5 / ChatGPT / GPT-4, outputs improved **~20% absolute on average** over one-step generation.

**Huang et al., 2023** — *Large Language Models Cannot Self-Correct Reasoning Yet* — https://arxiv.org/abs/2310.01798
Intrinsic self-correction — no external feedback — fails on reasoning and at times **degrades** performance. This is the reason the protocol forces an evidence ledger and a never-invent-numbers rule instead of trusting the model to fix itself.

## Judging

**Li et al., 2025** — *Leveraging LLMs as Meta-Judges* — https://arxiv.org/abs/2504.17087
A rubric built with human experts, multiple scoring agents, then threshold filtering. On JudgeBench: **~15.55% improvement over raw judgments** and **~8.37% over a single-agent judge**. Motivates the six-criterion, evidence-citing rubric over a single holistic score.

**Chan et al., 2023** — *ChatEval* — https://arxiv.org/abs/2308.07201
Multi-agent referee teams outperform single-agent evaluation prompting on open-ended and NLG tasks.

## Protocol design and the honest limits

**Zargari Marandi, 2026** — *The impact of multi-agent debate protocols on debate quality: a controlled case study* — https://arxiv.org/abs/2603.28813
Holds model factors fixed and varies only the protocol: Within-Round (current-round visibility), Cross-Round (full prior-round context), Rank-Adaptive Cross-Round (judge reorders and silences one agent per round), against a No-Interaction baseline. Findings: RA-CR converges fastest, WR maximizes peer-referencing, and **No-Interaction maximizes Argument Diversity**. Establishes an explicit **interaction ↔ convergence trade-off**. Motivates (a) keeping round 1 isolated to protect diversity, (b) treating topology and convergence pressure as knobs, (c) converge-vs-explore as a stated objective.
*Caveat: single domain (macroeconomic forecasting), small local models, proxy/lexical metrics, no human eval. Design intuition, not a benchmark.*

**Smit et al., 2023** — *Should we be going MAD?* — https://arxiv.org/abs/2311.17371
The strongest published counterweight, and the reason this skill triages before it debates. Benchmarking debate strategies against cost and time, they find **multi-agent debate in its current form does not reliably outperform simpler strategies like self-consistency or ensembling multiple reasoning paths**. MAD is *more sensitive to hyperparameters and harder to optimize* — though tuned variants such as Multi-Persona can surpass every non-debate protocol they evaluated. Debate is not a default; it is a tool with a cost, for problems that earn it.

## Model heterogeneity

**Du et al., 2023** (same paper, §"Utilizing Different Language Models")
ChatGPT and Bard debating each other on 20 GSM8K problems: **Bard alone solved 11, ChatGPT alone 14, and the two debating solved 17**. Cross-model debate improved both participants. *n=20 — directional, not a benchmark.*

**Li et al., 2024** — *More Agents Is All You Need* — https://arxiv.org/abs/2402.05120
Performance scales with the number of sampled agents, and **gains are larger for smaller models and harder tasks**: Llama2-13B gained 28–200% within a task versus 8–16% for GPT-3.5-Turbo, and an ensembled Llama2-13B reached **59% on GSM8K, above Llama2-70B's 54%**. Supports running personas on cheaper or mixed models while reserving the strongest model for judging.

**Divergent personas / role-play** — assigning expert roles with different incentives raises reasoning quality and reduces hallucination (CAMEL; role-play survey) — https://aclanthology.org/2024.findings-emnlp.969.pdf

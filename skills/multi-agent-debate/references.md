# Multi-Agent Debate — Research Basis

The protocol composes techniques with published empirical support:

- **Multi-agent debate** — multiple agents argue across rounds and a judge adjudicates; improves factuality and reasoning over single-pass and self-reflection. The judge + "tit-for-tat" design counters *Degeneration-of-Thought* (a confident model stops generating new ideas).
  - Du et al., 2023 — https://arxiv.org/abs/2305.14325
  - Liang et al., EMNLP 2024 — https://arxiv.org/abs/2305.19118
- **Protocol design / interaction–convergence trade-off** — controlled mechanism study isolating the debate protocol: same-round visibility (Within-Round) maximizes peer uptake; Rank-Adaptive Cross-Round (a judge reorders agents and silences the weakest each round) maximizes consensus; and *any* debate reduces argument diversity versus independent answers. Motivates (a) keeping round 1 isolated to protect diversity, (b) treating round topology and convergence pressure as explicit knobs, and (c) complexity-triggered invocation rather than always-on debate (our triage step). Caveat: single-domain (macroeconomic forecasting), small local models, proxy/lexical metrics, no human eval — use for design intuition, not as a performance benchmark.
  - Zargari Marandi, 2026 — https://arxiv.org/abs/2603.28813
- **Divergent personas / role-play** — assigning distinct expert roles with different incentives raises reasoning quality and reduces hallucination (CAMEL; role-play survey).
  - https://aclanthology.org/2024.findings-emnlp.969.pdf
- **Self-Refine** — iterative self-feedback followed by revision improves output quality without fine-tuning.
  - Madaan et al., 2023
- **Chain-of-Verification (CoVe)** — draft → generate verification questions → answer from sources → rewrite; materially reduces hallucination on list and long-form generation.
  - Dhuliawala et al., 2023 — https://arxiv.org/abs/2309.11495
- **Caveat: self-correction needs external grounding** — models cannot reliably self-correct *reasoning* without outside evidence; this is why the protocol forces an evidence ledger and a "never invent numbers" rule rather than trusting the model to fix itself.
  - Huang et al., 2023 — https://arxiv.org/abs/2310.01798
- **LLM-as-judge / rubric design** — concrete, evidence-based criteria (citations, numeric checks, logical order) evaluate reports far more reliably than vague descriptors.
  - https://arxiv.org/html/2504.17087v1

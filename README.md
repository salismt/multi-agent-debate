# Multi-Agent Debate

**Your agent's most dangerous output isn't a wrong answer. It's a fluent one.**

Ask a hard, contested question — *should we rebuild or refactor, take the offer or wait, is this thesis real* — and you get back one voice, one pass, no dissent, and a number that was never in any source. It reads like analysis. It was a guess with good posture.

This skill replaces that with structured disagreement: opposed personas debating as parallel subagents, a judge that scores on cited evidence instead of prose quality, and a verification pass aimed at the claims your decision actually rests on.

> **Truth survives the strongest objection — so manufacture that objection on purpose.**

## Install

```bash
npx skills add salismt/multi-agent-debate
```

Works with [80+ agents](https://github.com/vercel-labs/skills#supported-agents) — Claude Code, Codex, Cursor, OpenCode, Amp. Or drop `skills/multi-agent-debate/` into `~/.claude/skills/`.

## Use it

Nothing to invoke. Ask a real question:

```
Should we move payment reconciliation off the monolith before Q4, or after?
Two offers on the table. Which do we take, and what would have to be true for the other to win?
Is this market thesis supported, or am I pattern-matching?
```

You get back a ranked recommendation with the strongest *surviving* objection attached, an evidence ledger where every unsourced figure is marked `OPEN` rather than invented, and a record the next session can resume from.

## What it does

| Phase | Output | Guardrail |
|-------|--------|-----------|
| **Frame & ground** | Question, objective, evidence ledger | Claims tagged `FACT` / `INFERENCE` / `ASSUMPTION`; no source → `OPEN`, never invented |
| **Debate** | Competing positions | Separate subagents, max reasoning effort, **round 1 isolated**, exchange from round 2 |
| **Judge** | Scored adjudication | Six-criterion rubric; a score is invalid unless it cites the evidence behind it |
| **Refine** | Revised positions | Stop when a round surfaces no new objection |
| **Verify** | Checked claims + residual risks | Chain-of-verification, answered only from sources |
| **Decide** | Ranked recommendation | Owned, measurable, sequenced |
| **Persist** | Resumable record | Next session continues instead of restarting |

Three design choices carry the weight:

- **Round 1 is isolated.** Let personas see each other immediately and their positions collapse into one. Independence first, exchange second, is what preserves real diversity.
- **Judge, don't average.** Majority-vote ensembling loses to adjudicated debate in the literature. Somebody has to say *why* one side won, and cite it.
- **Converge vs. explore is declared up front.** Debate *compresses* option diversity — a feature when you need one decision, a bug when you need a map of the alternatives.

## The evidence

This skill composes published techniques. The numbers below are **each paper's own reported results on benchmarks** — they are not a measurement of this skill, which has not been independently benchmarked. Saying so plainly is the same standard the skill enforces on your agent.

| What the research shows | Reported result | Source |
|---|---|---|
| Debate beats a single pass on reasoning | Grade school math **77.0 → 85.0**, arithmetic **67.0 → 81.8** (3 agents, 2 rounds) | [Du et al. 2023](https://arxiv.org/abs/2305.14325) |
| Self-reflection is *not* a substitute | MMLU: single 63.9, self-reflection **57.7** (worse), debate **71.1** | Du et al., Table 2 |
| Adjudication beats majority vote | Majority 81.0 vs debate **85.0** on grade school math | Du et al., Table 1 |
| Verification cuts hallucination | Biography FactScore **55.9 → 71.4**; list precision **0.17 → 0.36** | [CoVe 2023](https://arxiv.org/abs/2309.11495) |
| Iterative refinement pays | **~20% absolute** average gain across 7 tasks | [Self-Refine 2023](https://arxiv.org/abs/2303.17651) |
| Rubric-based multi-agent judging | **+15.6%** over raw judgments, **+8.4%** over a single-agent judge | [Li et al. 2025](https://arxiv.org/abs/2504.17087) |
| Confident models stop thinking | Degeneration-of-Thought: reflection can't produce novel thoughts once confident | [Liang et al. 2024](https://arxiv.org/abs/2305.19118) |
| Models can't self-correct reasoning alone | Without external feedback, performance sometimes **degrades** | [Huang et al. 2023](https://arxiv.org/abs/2310.01798) |
| **Debate is not a free lunch** | MAD **does not reliably beat** self-consistency; hyperparameter-sensitive and hard to tune | [Smit et al. 2023](https://arxiv.org/abs/2311.17371) |
| **Debate costs diversity** | The no-interaction baseline maximizes argument diversity | [Zargari Marandi 2026](https://arxiv.org/abs/2603.28813) |

The last two rows are why this skill **triages before it debates**. Full protocol only for stakes × uncertainty; lookups and well-specified tasks get a direct answer and one sanity check. A skill that fires on everything would be slower, dearer, and — per Smit et al. — not reliably better.

Full citations and tables: [`references.md`](skills/multi-agent-debate/references.md).

## Running it on different models

Debate tolerates heterogeneous models. Judging does not.

- **Mixing models helps.** ChatGPT and Bard on 20 GSM8K problems: Bard alone solved 11, ChatGPT alone 14, **the two debating solved 17** ([Du et al.](https://arxiv.org/abs/2305.14325); n=20, directional).
- **Weaker models gain the most.** Llama2-13B gained 28–200% within a task versus 8–16% for GPT-3.5-Turbo, and an ensembled Llama2-13B hit **59% on GSM8K, above Llama2-70B's 54%** ([More Agents Is All You Need](https://arxiv.org/abs/2402.05120)). Running personas on cheap models is a real strategy, not a compromise.
- **Keep the judge strong and fixed.** LLMs may not judge fairly when the debaters are different models — judges favour their own family ([Liang et al.](https://arxiv.org/abs/2305.19118)). Spend your best model on adjudication and verification, not on the personas.
- **On a weak base model,** cut the round count and tighten the rubric. Debate amplifies the reasoning a model already has; it does not manufacture reasoning that isn't there.

The skill encodes this as a **Model Assignment** rule, so your agent applies it without being told.

## License

MIT

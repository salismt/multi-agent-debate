# Multi-Agent Debate

An agent skill for questions where one confident answer is the risk: architecture trade-offs, build or buy, strategy calls, research synthesis, anything where the honest answer starts with "it depends."

Instead of a single pass from a single model, it runs opposed personas as parallel subagents, has a judge score their claims against cited evidence, and verifies the claims the decision rests on before it recommends anything. Figures with no source get marked `OPEN` rather than estimated.

> **Truth survives the strongest objection, so manufacture that objection on purpose.**

## Install

```bash
npx skills add salismt/multi-agent-debate
```

Works with [80+ agents](https://github.com/vercel-labs/skills#supported-agents) including Claude Code, Codex, Cursor, OpenCode and Amp. Or drop `skills/multi-agent-debate/` into `~/.claude/skills/`.

## Use it

Nothing to invoke. Ask a real question:

```
Should we move payment reconciliation off the monolith before Q4, or after?
Two offers on the table. Which do we take, and what would have to be true for the other to win?
Is this market thesis supported, or am I pattern-matching?
```

You get a ranked recommendation with the strongest surviving objection attached, an evidence ledger, and a record the next session can pick up from.

## What it does

| Phase | Output | Guardrail |
|-------|--------|-----------|
| Frame and ground | Question, objective, evidence ledger | Claims tagged `FACT` / `INFERENCE` / `ASSUMPTION`; no source means `OPEN`, never invented |
| Debate | Competing positions | Separate subagents at max reasoning effort, round 1 isolated, exchange from round 2 |
| Judge | Scored adjudication | Six-criterion rubric; a score is invalid unless it cites the evidence behind it |
| Refine | Revised positions | Stop when a round surfaces no new objection |
| Verify | Checked claims, residual risks | Chain-of-verification, answered only from sources |
| Decide | Ranked recommendation | Owned, measurable, sequenced |
| Persist | Resumable record | Next session continues instead of restarting |

Three choices do most of the work.

**Round 1 is isolated.** Personas that can see each other from the start collapse into one position. They propose independently, then exchange from round 2.

**The judge scores rather than averages.** Majority-vote ensembling loses to adjudicated debate in Du et al.'s results. The judge has to name which side won and cite the evidence it won on.

**You declare converge or explore up front.** Debate compresses the option set, which is what you want for a single decision and the opposite of what you want when you are mapping alternatives.

## The evidence

These are each paper's reported benchmark results for the techniques this skill composes. None of them measure the skill itself, which has not been benchmarked.

| Finding | Reported result | Source |
|---|---|---|
| Debate beats a single pass on reasoning | Grade school math 77.0 → 85.0, arithmetic 67.0 → 81.8 (3 agents, 2 rounds) | [Du et al. 2023](https://arxiv.org/abs/2305.14325) |
| Self-reflection is no substitute | MMLU: single 63.9, self-reflection 57.7, debate 71.1 | Du et al., Table 2 |
| Adjudication beats majority vote | Majority 81.0 against debate 85.0 on grade school math | Du et al., Table 1 |
| Verification cuts hallucination | Biography FactScore 55.9 → 71.4; list precision 0.17 → 0.36 | [CoVe 2023](https://arxiv.org/abs/2309.11495) |
| Iterative refinement pays | ~20% absolute average gain across 7 tasks | [Self-Refine 2023](https://arxiv.org/abs/2303.17651) |
| Rubric-based multi-agent judging | +15.6% over raw judgments, +8.4% over a single-agent judge | [Li et al. 2025](https://arxiv.org/abs/2504.17087) |
| Confident models stop generating new ideas | Degeneration-of-Thought: reflection cannot produce novel thoughts once the model is confident | [Liang et al. 2024](https://arxiv.org/abs/2305.19118) |
| Models cannot self-correct reasoning alone | Without external feedback, performance sometimes degrades | [Huang et al. 2023](https://arxiv.org/abs/2310.01798) |
| Debate does not always win | Multi-agent debate does not reliably beat self-consistency, and is hyperparameter-sensitive and hard to tune | [Smit et al. 2023](https://arxiv.org/abs/2311.17371) |
| Debate costs diversity | The no-interaction baseline produces the most diverse arguments | [Zargari Marandi 2026](https://arxiv.org/abs/2603.28813) |

The last two rows are why the skill triages before it debates. Full protocol only when stakes and uncertainty justify it; lookups and well-specified tasks get a direct answer and one sanity check.

Tables and full citations: [`references.md`](skills/multi-agent-debate/references.md).

## Running it on different models

Debate tolerates mixed models. Judging does not.

- **Mixing helps.** ChatGPT and Bard on 20 GSM8K problems: Bard solved 11 alone, ChatGPT 14 alone, the two debating solved 17 ([Du et al.](https://arxiv.org/abs/2305.14325)). At n=20 this is directional, not a benchmark.
- **Weaker models gain more.** Llama2-13B gained 28-200% within a task against 8-16% for GPT-3.5-Turbo, and an ensembled Llama2-13B reached 59% on GSM8K against Llama2-70B's 54% ([More Agents Is All You Need](https://arxiv.org/abs/2402.05120)). Personas can run on cheap models.
- **Keep the judge strong and fixed.** Judges favour their own model family, so a mixed-model debate judged by one of the debaters is biased ([Liang et al.](https://arxiv.org/abs/2305.19118)). Spend the best model on adjudication and verification.
- **On a weak base model,** cut the round count and tighten the rubric. Debate amplifies the reasoning a model already has.

The skill carries this as a Model Assignment rule, so the agent applies it without being asked.

## License

MIT

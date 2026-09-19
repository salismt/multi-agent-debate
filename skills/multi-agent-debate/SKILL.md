---
name: multi-agent-debate
description: Use when tackling a high-stakes, ambiguous, or contested question with no verifiable ground truth and a costly downside — strategy, architecture trade-offs, research synthesis, market or product decisions, investment calls, or any "what should we do about X" where a single confident answer is risky.
---

# Multi-Agent Debate

## Overview
On hard problems the failure mode is a single, fluent, confident answer. This skill replaces that with structured disagreement, evidence grounding, explicit verification, and a resumable record.

**Core principle: truth survives the strongest objection — so manufacture that objection on purpose.**

## When to Use
Use when the question has any of: high stakes, genuine ambiguity, contested expert opinion, no checkable ground truth, or load-bearing assumptions. Symptoms: the honest answer is "it depends"; strategy / architecture / research / market / product / investment calls; decisions you will be held to later.

**Calibrate effort to stakes × uncertainty — triage first:**

- **Tier 1 — simple or verifiable:** answer directly, cite sources, run one sanity check, stop.
- **Tier 2 — hard / contested / high-stakes:** run the full protocol below.

Do NOT run the full protocol for lookups, well-specified tasks, or anything with a clear right answer. Debate has real token/latency cost and *measurably reduces answer diversity*, so invoke it by complexity, not by default.

**Set the objective before debating** — it controls how hard you push for consensus:

- **Converge** → you need one decision. Push toward agreement; the rank-adaptive accelerator in step 4 is allowed.
- **Explore** → you need a map of strong, distinct options. Preserve disagreement; do *not* force consensus.

Debate compresses diversity, so converge only as much as the objective demands.

## The Protocol (Tier 2)

1. **Frame & ground.** Restate the real question, the decision it informs, success criteria, constraints, and the **objective (converge vs. explore)**. Build an **evidence ledger**: tag every claim `FACT` / `INFERENCE` / `ASSUMPTION` with its source. Missing data → mark `OPEN`. Never invent numbers.
2. **Debate as parallel subagents — independent first, then exchange.** Dispatch each persona as a *separate subagent* at the **highest reasoning effort available** (Claude Code: Task/subagent tool; Codex / OpenCode: parallel agents; reasoning "extra-high" / "high"). Give each a *conflicting incentive* (e.g. domain operator vs. venture strategist; builder vs. red-team skeptic).
   - **Round 1 — isolated:** each proposes independently with **no visibility of the others** (this is what preserves genuine diversity — same-round visibility collapses it).
   - **Round 2+ — cross-round exchange:** each now sees the others' prior-round positions and attacks the strongest opposing claim with sourced objections. Agreement counts only if it survives the attack.
   - *Optional, high-stakes turns:* generate 2–N candidate responses and let the judge keep the best (**best-of-N**) to cut sampling noise.
3. **Judge, don't average.** A synthesizer subagent (also maximum effort) scores each contested point against the LLM-as-judge rubric below, records *why* one side won, and cites the evidence behind each score. Preserve unresolved disputes as `OPEN` — never force false consensus.
4. **Refine.** Revise positions in light of the critique. Bound to 2–3 rounds; stop when a round surfaces no new objection.
   - *Optional — converge mode only — rank-adaptive acceleration:* each round the judge ranks the positions and prunes or merges the weakest, putting the strongest first. Speeds convergence but trades away diversity; never use it in explore mode.
5. **Verify (chain-of-verification).** Generate 5–8 verification questions targeting the load-bearing claims and the top recommendation. Answer each *only from sources*. Fix every inconsistency. List residual risks.
6. **Decide.** Output a ranked recommendation scored on an explicit framework (value × effort × risk, or RICE/ICE), with the winning argument and the strongest surviving objection. Decision-useful: owned, measurable, sequenced.
7. **Persist for resumption.** Write a durable record — decisions + rationale, open questions + what evidence closes each, assumptions ledger, changelog — so the next session resumes the analysis instead of restarting it.

## Model Assignment
Debate tolerates heterogeneous models; judging does not.

- **Personas** — vary the model where you can. Cross-model debate beat both participants debating alone (Du et al.), and weaker models gain proportionally more from the protocol than frontier ones.
- **Judge + verification** — use the strongest model available and keep it fixed. Judges favor their own model family, so drawing the judge from one debater's family biases the verdict (Liang et al.).
- On a weak base model, cut the round count and tighten the rubric. Debate amplifies the reasoning a model already has; it does not substitute for it.

## Judge Rubric (evidence-based LLM-as-judge)
Score every contested claim and the final recommendation 1–5 on each criterion. A score is **valid only if the judge cites the specific evidence** — an evidence-ledger row, a source, or a numeric check — that justifies it; bare numbers and vague justifications are themselves a fail. Anything scoring **<4** goes back for another refine round (step 4) or is marked `OPEN`.

| Criterion | Scores 5 when… |
|-----------|----------------|
| Grounding | every factual claim traces to a source in the evidence ledger; no unsourced numbers |
| Completeness | covers the decision's material options and both personas' strongest points |
| Decision-usefulness | recommendation is ranked, owned, measurable, and sequenced |
| Specificity | concrete to the actual context, not generic boilerplate |
| Internal consistency | no contradiction with other accepted claims or stated constraints |
| Robustness | survives its strongest stated objection and the verification questions (step 5) |

## Quick Reference

| Phase | Output | Guardrail |
|-------|--------|-----------|
| Frame & ground | Question + objective + evidence ledger | No claim without a source tag; pick converge vs. explore |
| Debate | Competing positions | Parallel subagents, max effort, isolated round 1 then exchange |
| Judge | Scored, adjudicated synthesis | Score on the evidence-based rubric; cite evidence per score |
| Refine | Revised positions | Stop when no new objection; rank-adaptive prune only in converge mode |
| Verify | Checked claims + risks | Answer only from sources |
| Decide | Ranked, scored recommendation | Owned + measurable |
| Persist | Resumable record | Decisions, open Qs, assumptions, changelog |

## Common Mistakes

| Rationalization | Reality |
|-----------------|---------|
| "The two views agreed fast" | They aren't divergent. Give them conflicting incentives and a red-team mandate. |
| "One agent can play both sides" | Same context bleeds the positions together. Run each persona as a separate subagent at max effort. |
| "Let them see each other from round 1" | Same-round visibility collapses independence. Keep round 1 isolated; exchange from round 2. |
| "I'll just pick the better-written side" | Score both on the evidence-based rubric and cite evidence per score, or the verdict is vibes. |
| "Converge to a clean answer" (goal was explore) | You destroy the option diversity you needed. Match consensus pressure to the objective. |
| "No source, so I'll estimate" | Mark it `OPEN`. An invented number poisons every downstream conclusion. |
| "Verification is overkill" | The load-bearing claim is the one most likely wrong — verify it first. |
| "I'll skip the record" | Then it can't be resumed and the next agent repeats the work. |

## Red Flags — STOP
- Round 1 wasn't isolated (peers visible too early) → diversity already lost.
- Personas converging the moment they exchange → not adversarial enough.
- Forcing consensus when the objective was explore.
- Any unsourced figure in the output → ground it or mark `OPEN`.
- A recommendation with no owner, metric, or surviving objection → not decision-useful.
- About to conclude with no verification pass on the top claim.

## Why This Works
Composes multi-agent debate (counters Degeneration-of-Thought), divergent-persona role-play, Self-Refine, Chain-of-Verification, and evidence-grounded LLM-as-judge evaluation. The protocol knobs — round-1 isolation, cross-round exchange topology, optional rank-adaptive scheduling, and complexity-triggered invocation — follow controlled MAD-protocol findings that **interaction and convergence trade off**, and that debate reduces diversity. See `references.md`.

# Multi-Agent Debate

**An agent skill that stops your AI from confidently guessing on decisions you'll be held to.**

On hard, contested questions the failure mode isn't a wrong answer — it's a *fluent* one. One pass, one voice, no dissent, invented numbers rounded into something that reads like analysis. This skill replaces that with structured disagreement: adversarial personas debating in parallel, a judge that scores on evidence instead of prose quality, and a verification pass on the claims the decision actually rests on.

> **Core principle: truth survives the strongest objection — so manufacture that objection on purpose.**

## Install

```bash
npx skills add salismt/multi-agent-debate
```

Global install, Claude Code only:

```bash
npx skills add salismt/multi-agent-debate -g -a claude-code
```

Works with [80+ agents](https://github.com/vercel-labs/skills#supported-agents) — Claude Code, Codex, Cursor, OpenCode, Amp, and more. Or just drop `skills/multi-agent-debate/` into `~/.claude/skills/`.

## Use it

Nothing to invoke. Ask your agent a real question and it picks the skill up:

```
Should we move our payment reconciliation off the monolith before Q4, or after?
```
```
We have two offers. Which do we take, and what would have to be true for the other to win?
```
```
Is this market thesis actually supported, or am I pattern-matching?
```

Your agent will frame the decision, run opposed personas as parallel subagents, adjudicate on a rubric, verify the load-bearing claims, and hand you a ranked recommendation with the strongest surviving objection attached.

## What it actually does

| Phase | Output | Guardrail |
|-------|--------|-----------|
| **Frame & ground** | Question, objective, evidence ledger | Every claim tagged `FACT` / `INFERENCE` / `ASSUMPTION`; no source → `OPEN`, never invented |
| **Debate** | Competing positions | Separate subagents, max reasoning effort, **round 1 isolated**, exchange from round 2 |
| **Judge** | Scored adjudication | Six-criterion rubric; a score is invalid unless it cites the evidence behind it |
| **Refine** | Revised positions | Stop when a round surfaces no new objection |
| **Verify** | Checked claims + residual risks | Chain-of-verification, answered only from sources |
| **Decide** | Ranked recommendation | Owned, measurable, sequenced |
| **Persist** | Resumable record | So the next session continues instead of restarting |

Two design choices do most of the work:

- **Round 1 is isolated.** Let personas see each other immediately and their positions collapse into one. Independence first, exchange second, is what preserves genuine diversity.
- **Converge vs. explore is set up front.** Debate *compresses* option diversity — that's a feature when you need one decision, a bug when you need a map of the real alternatives. The skill makes you pick, and only applies consensus pressure when you asked for it.

## When *not* to use it

Built-in triage, and it matters: full debate costs tokens, costs latency, and measurably reduces answer diversity. Lookups, well-specified tasks, and anything with a checkable right answer get a direct answer and one sanity check. The protocol fires on stakes × uncertainty, not by default.

## Why it works

Composes techniques with published empirical support — multi-agent debate (counters Degeneration-of-Thought), divergent-persona role-play, Self-Refine, Chain-of-Verification, and evidence-grounded LLM-as-judge scoring. The protocol knobs — round-1 isolation, cross-round exchange topology, optional rank-adaptive scheduling, complexity-triggered invocation — follow controlled MAD-protocol findings that interaction and convergence trade off against each other.

Full citations: [`references.md`](skills/multi-agent-debate/references.md).

## License

MIT

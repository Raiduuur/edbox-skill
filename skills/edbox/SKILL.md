---
name: edbox
description: Edbox, the gearbox — which model and effort carries a piece of work (Opus 5.5, Fable 5.2, Sol 6.1, Haiku 5.5). Use when picking a model or effort for a subagent or delegate, when work circles without landing, when the options on the table are all weak, or when the user says "edbox", "gearbox", "gear", or "which model".
---

# Edbox

Edbox is the gearbox for models.

Opus 5.5 drives. Fable 5.2 is the shift. You shift for **temperament**, not
for speed or raw power: both run at about 110 tokens/s, and Opus completes
almost everything Fable can.

Fable is the scarce gear. It may use at most half the weekly limit, and that
half buys under a quarter of what the same plan buys in Opus. Spend it on the
moments below, not on bulk work.

## Temperaments

- **Opus, the meticulous one.** Reads everything, double-checks, finishes,
  rarely misses a detail. Its cost: it obeys. It will not challenge the task,
  it applies every piece of feedback it is given, and it keeps finding more to
  do, so a 30-line change grows to 1000.
- **Fable, the senior who ships.** Smartest in the room, creative, questions
  the premise, sees the real problem at a glance, and says "this is fine,
  ship it". Its cost: it skims. It misses small visible details and lets minor
  defects through.

## Gears

| Gear | Model, effort | Carries |
| --- | --- | --- |
| Drive | Opus 5.5 `high` | The default: implementing, debugging, migrations, building repros and test environments, leading subagents. `medium` for small, well-scoped jobs. |
| Stone-turner | Opus 5.5 `xhigh` | Turn every stone: audits, checking finished work, Windows work, replaying large logs, exploration branches you will not merge. |
| Shift | Fable 5.2 `high` | The shift-up moments below. |
| Review | Sol 6.1 `high` | Strict second-provider review of work from either Claude gear: the `sol-loop` skill. It flags everything, so judge each finding before acting. The main coding agent may also hand Sol other work (an investigation, a second opinion) when it judges that helps; that call is the main agent's. |
| Bulk | Haiku 5.5 | Classify, extract, or pattern-scan many files. Haiku reads; it does not write code. |

`max` and `low` stay unused on every model: `max` forces overthinking and
scores worse than `xhigh`. Past `high`, shift the model up at a lower effort
before raising effort again. Sonnet is not a gear: without a clear path it
costs about what Opus does for worse work.

## Shift up to Fable when

- **Unprovable.** No test or reference can say which answer is better:
  design, visuals, architecture direction, a creative or unusual approach.
- **Weak options.** Opus's proposals are samey or none convinces.
- **Circling.** A thread or PR keeps looping: more fixes, more second
  guessing, nothing lands. Hand Fable the work with: "Audit the work done
  here and tell us what we should actually do next." It either finds the real
  blocker or rules the work good enough to ship.
- **Hidden cause.** Opus built the repro and improved things, but the root
  cause is still unnamed. Opus builds, Fable diagnoses: "Verify the work Opus
  did here. Then play with it, find the edges that actually cause problems,
  and propose solutions."
- **Opus `high` fell short** on a hard problem.
- **Premise check.** Before a large build, ask Fable whether the plan is the
  right one at all.

## Stay in Opus, or shift down to it, when

- **Provable.** Tests, a reference implementation, or a metric to climb
  decide correctness. Opus is more than enough.
- **Read-it-all work.** The task rewards knowing every detail before
  starting.
- **It must be perfect.** High-risk changes (auth, permissions, data loss,
  money) get an Opus or Sol review. A Fable approval alone does not clear
  these.
- **Fable missed.** Rerun the same prompt on Opus before trying anything
  else.
- **Fable decided, now build.** Hand implementation and verification to Opus.

## Driving each gear

- **Opus briefs** end with: "If you think this is a good idea, do it.
  Otherwise, suggest something else." State the scope and the stop condition.
  Filter review findings yourself before handing them to Opus; it will
  implement all of them.
- **Fable output** gets a detail pass: check the small visible things (layout,
  overlap, edge values) yourself or with an Opus subagent.
- **If Opus `medium` handles everything you ask**, ask for bolder work.

## How to shift

- **Subagent (Claude Code):** `Agent` with `model: "opus" | "fable" | "haiku"`
  and the `effort` from the table. This skill is the instruction that
  authorises setting `effort`.
- **Sol, or any other provider:** the `sol-loop` skill, or in T3 Code
  `orchestrator_capabilities` then `delegate_task`. From Codex, reach the
  Claude gears the same way.
- **Whole thread:** you cannot change your own gear. Tell the user in one
  line: "Gear: move this thread to Fable 5.2 high, because <reason>." They
  switch with the model picker or `/model`.
- **You decide.** Shift on your own judgement and say it in one line: the
  gear and the reason. When the call is close, or the shift would spend a lot
  of Fable, ask the user first.

Source: Theo (t3.gg), "I think I figured it out", 2026-11-12, after two days
on Fable 5.2. Revise the table when a new model lands or a gear keeps
disappointing.

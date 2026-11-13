# Gearbox

An agent skill that tells coding agents **which model and effort should carry
a piece of work**, and when to change gear between Claude Opus 5.5 and Claude
Fable 5.2, with Sol 6.1 as the reviewer.

Works with Claude Code, Codex, and other agents that support
[Agent Skills](https://skills.sh).

```bash
npx skills add Raiduuur/gearbox-skill
```

## The idea

Opus drives. Fable is the shift. You change gear for **temperament**, not for
speed or raw power: both models run at about the same speed, and Opus finishes
almost everything Fable can.

- **Opus is the meticulous one.** It reads everything, double-checks, and
  rarely misses a detail. But it obeys: it will not challenge the task, it
  applies every piece of feedback, and it keeps finding more to do.
- **Fable is the senior who ships.** It is smarter and more creative, questions
  the premise, and says "this is fine, ship it". But it skims and lets small
  defects through.

Fable is also the scarce gear: on a Claude subscription it can use at most half
of the weekly limit, so the skill spends it on specific moments instead of bulk
work.

## The gears

| Gear | Model, effort | Carries |
| --- | --- | --- |
| Drive | Opus 5.5 `high` | The default: implementing, debugging, migrations, repros, leading subagents |
| Stone-turner | Opus 5.5 `xhigh` | Audits, checking finished work, large logs, exploration branches |
| Shift | Fable 5.2 `high` | Unprovable or creative calls, weak options, work that circles, hidden root causes |
| Review | Sol 6.1 `high` | Strict second-provider review of either Claude gear |
| Bulk | Haiku 5.5 | Classify, extract, pattern-scan many files (no code writing) |

**Shift up to Fable** when no test can say which answer is better, when Opus's
options are all weak, when a thread keeps looping without landing, or when
Opus built the repro but cannot name the root cause.

**Stay in Opus** when tests or a reference decide correctness, when the task
rewards reading everything first, when a high-risk change must be perfect, or
when Fable's answer missed.

The full rules, including the exact prompts to use for each shift, are in
[`skills/gearbox/SKILL.md`](skills/gearbox/SKILL.md).

## How it works

**Automatically.** The skill's description is always in the agent's context.
The agent reads the full skill on its own when it picks a model for a
subagent, when work is going in circles, or when its options are weak. It
decides, says in one line which gear it took and why, and asks you first when
the call is close or would spend a lot of Fable.

**By hand.** Type `/gearbox`, or say "gearbox" or "which gear" in a message.

**How an agent actually shifts:**

- It spawns the other model as a subagent for one job (in Claude Code, the
  `Agent` tool with `model` and `effort`), then carries on.
- For another provider such as Sol, it uses whatever delegation your harness
  offers.
- It cannot change the model of its own thread. If the whole thread should
  move, it tells you in one line and you switch with the model picker or
  `/model`.

## Install

With the skills CLI:

```bash
npx skills add Raiduuur/gearbox-skill
```

Or by hand:

```bash
git clone https://github.com/Raiduuur/gearbox-skill.git
mkdir -p ~/.agents/skills ~/.claude/skills
cp -r gearbox-skill/skills/gearbox ~/.agents/skills/gearbox      # Codex and others
ln -s ~/.agents/skills/gearbox ~/.claude/skills/gearbox          # Claude Code
```

### Optional: make it stick

A skill only fires when the agent thinks of it. To make the default gear part
of every session, add this line to your global `~/.claude/CLAUDE.md` and
`~/.codex/AGENTS.md`:

```markdown
- Gearbox (model choice): Opus 5.5 `high` drives all work by default. Shift to Fable 5.2 `high` for unprovable or creative calls, weak options, and work that circles without landing; hand the building and verifying back to Opus. Sol 6.1 `high` reviews the work of both, and takes other work when the main coding agent decides it helps. Before picking a model or effort for a subagent or delegate, read the `gearbox` skill.
```

## Notes

- The skill mentions a `sol-loop` skill and T3 Code's `delegate_task` as the
  routes to Sol. Both are optional. Without them, point the Review gear at
  whatever second-provider review you have, or drop that row.
- Model names age fast. When a new model lands, edit the gear table; the
  temperament logic is the part worth keeping.

## Credit

The rules are distilled from Theo's (t3.gg) video
["I think I figured it out"](https://youtu.be/XtykeVoSmP8), 12 November 2026,
recorded after two days on Fable 5.2. The opinions are his; the packaging as a
skill is this repo's.

## License

MIT

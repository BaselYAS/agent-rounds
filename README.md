# agent-rounds

**Build with one AI agent, verify with another, ship only what passes.**

`agent-rounds` runs a software change as a *round*: a fresh [Claude Code](https://code.claude.com) agent builds it,
a second, independent agent reviews it against the spec and answers **READY** or **NOT READY**, and only READY
work is promoted. The reviewer never sees the builder's conversation, only the work and the evidence it left.

I use this pattern every day to run a personal multi-agent system: it has carried about 180 rounds so far,
across a mobile web app, an Android game, a rule-enforced paper-trading system and server tooling.

```
            task.md                     review.md
               │                            │
               ▼                            ▼
   ┌───────────────────────┐   ┌──────────────────────────┐
   │ BUILDER (fresh agent) │──▶│ REVIEWER (fresh agent)   │
   │ works in the project, │   │ own temp dir, no edits,  │
   │ builds to STAGING,    │   │ checks evidence vs spec  │
   │ leaves proof          │   │ first line: READY / NOT  │
   └───────────────────────┘   └────────────┬─────────────┘
                                            │
                          READY ────────────┼──────────── NOT READY
                            │                               │
                   --on-ready (promote)            nothing changes;
                            │                      issues → next round
                            ▼
                 summary.md → --notify (human)
```

## Why two agents

An agent that checks its own work tends to agree with itself. A fresh reviewer with a checklist, its own
directory and no stake in the result catches a different class of mistakes: wrong numbers, broken
neighbours, missing tests, "works on my screenshot". The human stays in the loop for what only a human can
judge, and decides what ships.

## Quick start

Requires the Claude Code CLI (`claude`), signed in, plus `bash` and `python3`.

```bash
git clone https://github.com/BaselYAS/agent-rounds && cd agent-rounds
cp -r examples/hello/project /tmp/hello
scripts/run_round.sh --workdir /tmp/hello \
  --task examples/hello/task.md --review examples/hello/review.md \
  --label HELLO1 --builder-model haiku --reviewer-model haiku
```

The example round costs well under one cent on Haiku. Exit code: `0` READY, `1` NOT READY, `2` failed.

## Options

| Option | Default | Meaning |
|---|---|---|
| `--workdir` | required | project the builder works in |
| `--task`, `--review` | required | the two prompts (see `templates/`) |
| `--label` | `round` | name used in logs and report files |
| `--builder-model`, `--reviewer-model` | `opus` | any Claude Code model alias or id |
| `--builder-timeout`, `--reviewer-timeout` | `1800`, `900` | seconds |
| `--out` | `./rounds-out` | reports and log |
| `--review-dir` | fresh temp dir | where the reviewer runs |
| `--on-ready` | none | command run only on READY (e.g. promote staging) |
| `--notify` | none | command called with the summary file path |

## Writing good rounds

Templates are in [`templates/`](templates); the same guidance is packaged as a Claude Code skill in
[`skills/run-round/SKILL.md`](skills/run-round/SKILL.md), so an agent can run rounds itself.

- **One outcome per round**, with the numbers in a spec file the builder must treat as the source of truth.
- **Say what must not change**, and have the reviewer diff it.
- **Hard limits** in every task: time, folders, "staging only", "kill only your own processes".
- **Proof, not claims**: the builder leaves evidence (tests, logs, screenshots); the reviewer checks it.
- **A verdict on the first line**: anything other than READY counts as NOT READY.

## Lessons from real use

- **Prove the behaviour, not a moment near it.** A reviewer once passed an animation because the screenshot was
  taken *before* the animation started. Ask for evidence captured while the behaviour happens.
- **Some calls belong to a human.** A sprite's left/right facing was "verified" twice by reading pixels the
  wrong way; the artist settled it in seconds. Reviewers now report taste and direction; they don't decide them.
- **Specs carry bugs too.** When a spec's labels were wrong, the builder followed them faithfully and the
  reviewer agreed. Independent review checks the work against the spec, so the spec itself needs a human read.
- **Fresh sessions, every time.** Long-lived agent conversations get expensive and drift; a round starts clean.
- **NOT READY is a feature.** A failed review cost one cheap round; a bad release costs trust.

## Background

I came up with this independently while running my own multi-agent system: agents that checked their own
work kept agreeing with themselves, so I gave every change a second, independent reviewer and a gate. I later
found it matches a known pattern, which Anthropic calls *evaluator–optimizer* in
[Building effective agents](https://www.anthropic.com/engineering/building-effective-agents), also known as
"LLM-as-judge" and used by reviewer or critic agents in multi-agent frameworks. This repository is not a new
idea; it is a small, practical version of one, tied to a real release gate, plus what running it for real taught me.

## How this was made

Designed and operated by Basel Yasar Abdel Fattah; built with AI coding agents (Claude Code), including,
fittingly, rounds like these.

## License

MIT, see [LICENSE](LICENSE).

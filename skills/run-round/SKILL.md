---
name: run-round
description: Run a change as a build-and-review "round" with two independent Claude Code agents — one builds, a fresh one verifies against the spec and says READY or NOT READY, and only READY is promoted. Use when a change to a project should be built autonomously and checked before it goes live.
---

# Run a round

A round is one well-scoped change, built by one agent and verified by another that never saw the
builder's conversation. Use `scripts/run_round.sh` from this repository.

## 1. Scope the change
- One outcome per round. If you need the word "and" twice, split it into two rounds.
- Write the numbers down: a spec file the builder must treat as the source of truth.
- Decide what must NOT change (data, behaviour, files) and say so explicitly.

## 2. Write the task (templates/task.md)
- A hard time limit, the folders it may touch, and "build to staging only".
- "Don't ask, decide and note it": rounds run unattended.
- Ask for **proof** the reviewer can check: screenshots taken *while* the behaviour happens, logs, test
  output.

## 3. Write the review (templates/review.md)
- The reviewer must not edit. It works on copies, in its own directory.
- Every check names its evidence. "Looks fine" is not a check.
- Taste and direction are the human's call: the reviewer reports them, it doesn't decide them.
- The first line of its answer is READY or NOT READY. Anything else counts as NOT READY.

## 4. Run it
```bash
scripts/run_round.sh --workdir ./my-project --task task.md --review review.md \
  --label FEATURE1 --on-ready './promote.sh' --notify './notify.sh'
```

## 5. After the round
- READY: promote (or let `--on-ready` do it), then tell the human what changed and what the reviewer
  could not verify.
- NOT READY: nothing went live. Turn the blocking issues into the next round's task.
- A human checks the real thing. When they find something the reviewer missed, add that check to the
  review template so it is caught next time.

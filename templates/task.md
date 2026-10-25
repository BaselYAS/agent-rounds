[ROUND <NAME> — <one-line goal>. Autonomous: don't ask questions, make the reasonable choice and note it.
HARD LIMIT <N> min. Work ONLY in <paths>. No git, no sudo. Build to STAGING only, never to the live location.
Kill only processes you started, never by name pattern. Don't change <things that are off-limits, e.g. balance numbers>.
REPORT in at most 10 lines: what changed, tests run, anything you're unsure about.]

READ <spec file> — it is the source of truth for every number.

1. <The change, stated as an outcome, with exact values.>
2. <Constraints: what must NOT change. Reuse existing code instead of copying it.>
3. <Tests: which ones must pass, which new ones to add.>
4. <Proof: what evidence to leave for the reviewer, e.g. screenshots taken at the moment the behaviour
   happens (not before), saved to <folder>.>
VERSION "<NAME>".

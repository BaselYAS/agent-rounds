[REVIEWER for round <NAME>. You did not build this. Don't ask questions. DO NOT edit anything.
Time limit <N> min. Read the task: <task file>, and the spec: <spec file>.]

Run the tests first. Work on COPIES (e.g. serve a copy of the staging build on a random local port).

Check, each with evidence:
- <The required behaviour, with the exact values from the spec.>
- <What must be unchanged (diff the relevant data or files against the live version).>
- <The builder's proof (screenshots, logs) actually shows the behaviour, not a moment before or after it.>
- <No errors in logs or console; version string is <NAME>.>

Only judge what can be checked. Things that are a human's call (taste, direction, wording) you report,
you don't decide.

Kill only processes you started. Clean up your temp directory at the end.

REPORT (max 8 lines): first line READY or NOT READY. Then blocking issues, then minor ones, then
anything you could not verify.

# Review checklist

A worker's "done" is a claim. Judge "whether it achieved the correct final state" [5],
by reading the end state yourself.

## Claim to check

| Worker claims | Overseer checks | Not enough |
|---|---|---|
| Edited a file | `git diff` of the named files, read in full for a change about to ship | The worker's summary of its diff |
| Created a file | `ls -l` and `wc -l`, then the content if it ships | "Created X" in the report |
| Sent a message | The sent row: recipient, subject, body, time | "Sent" in the report, or the dry-run output |
| Deployed | Fetch the live URL: status code and the changed text on the page | The deploy command's exit code |
| Wrote data | Read the row back from the store by its id | The write function's return value |
| Deleted something | List the location; confirm the target is gone and nothing else is | "Removed" in the report |
| Nothing changed | Hash or `git status --short` before and after | "No changes made" |
| Tests pass | Rerun the test command | A pasted test log |
| Service is up | `pgrep -af <name>` or the health URL | "Restarted" in the report |
| Fact about the world | Open the cited file:line or URL for one of the facts | The citation alone |

## Every report

1. Read the final message only. Never the transcript file.
2. Match it against the brief's final-message contract. A missing item is a question back
   to the same worker by its agent id, not a guess.
3. Spot-check one cited fact. Workers get small facts wrong in confident prose: a path,
   "the newest", a count, a date.
4. Run the check from the table above for each claim the brief said you would check.
5. If a check fails, verify in the worker's environment (same host, path, network,
   account) before blaming its code.
6. Log the outcome as one event-log line, timestamp from `date` in the same command.

## Second opinions

- Use a fresh reviewer that has not seen the reasoning. A reviewer that "never saw your
  conversation" is not "swayed by the reasoning that produced the code" [8].
- Handing the same task to another subagent does not by itself make the answer better [8].
- Use a `verifier` agent, or a fresh worker told to refute, only when a mistake is
  expensive and not immediately visible: security, credentials, deletions, money,
  production data, or a finding about to be reported as confirmed. Give it the claim and
  the evidence, not the reasoning. One is enough; three only when the mistake would be
  irreversible.
- If the check runs in under a minute, run it instead.

## Pattern 1: draft, stop, dictate, resume (the default for anything a human receives)

1. Brief the worker to verify facts, draft, run the checks, run a dry run, and stop.
   The brief says "do not send" in the hard rules.
2. The worker returns the exact text, the recipient, the check output and the dry-run
   output.
3. The overseer reads the exact text itself. Never delegate this read.
4. The overseer decides the exact final text, editing it in its own message if needed.
5. The overseer messages the same worker (SendMessage to its agent id) with the exact
   final text and the instruction to rerun the dry run, then send once.
6. The worker sends with a separate command from the dry run. If the send errors, it does
   not rerun; it reports the output.
7. The worker returns the sent row.
8. The overseer reads the sent row and the recipient, then logs the send.

## Pattern 2: acceptance conditions (routine, low-stakes, repeated sends only)

Use only when the operator has approved the message as a template, a wrong send is cheap
to correct, and the recipient is one the template covers.

1. The overseer has read the template text once and keeps it in a file.
2. The brief lists acceptance conditions, each one checkable by the worker, for example:
   (a) the recipient matches the expected address;
   (b) the body equals the template with only the named fields filled;
   (c) the style check prints no findings;
   (d) no send with the same key exists in today's log.
3. The worker runs the checks and the dry run as separate commands.
4. If every condition holds, the worker sends once. If any fails, it stops and reports
   which one, with the output.
5. The worker returns the sent row or the failed condition.
6. The overseer reads the sent row afterward and logs it.

Anything new in kind, costly to correct, or outside the template goes back to Pattern 1.

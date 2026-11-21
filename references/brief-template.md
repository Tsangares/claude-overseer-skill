# Brief template

Copy the block, fill every angle-bracket field, and delete nothing. A section that does
not apply says "none", so the worker knows it was considered.

Why the shape matters: each subagent "needs an objective, an output format, guidance on
the tools and sources to use, and clear task boundaries", and without them agents
"duplicate work, leave gaps, or fail to find necessary information" [5]. A worker starts
from a fresh context and sees only this prompt [6].

```text
You are an <model class> worker <doing what> in <absolute path or system>, so that
<why the lane exists>.

Hard rules:
- Do not change <a person's wording / facts on file / files outside the lane>.
- No commits, pushes, sends, deploys or deletions unless a work item below names them.
- Keep every Bash output under 4 KB.
- Do not guess. Cite evidence as file:line or URL.

Sensitive files and credentials:
- Never read <paths>. Never print, copy or quote a credential value.
- <any project rule about secrets, private data or restricted directories>

Scratch: <absolute project path>/scratchpad/<slug>/ (create it). Long output goes here;
return the path, not the content.

You may edit only:
- <absolute path>
- <absolute path>
Everything else is read-only.

Expected state (verify first, do not assume):
- <file> has <N> lines / <service> reports <status> / <row> reads <value>.
If any item does not hold, stop and report the mismatch before doing anything else.

Decisions already made (do not revisit):
- <decision>, because <reason>.

Work:
1. <task>. Verify: <check the worker runs itself, and what output proves it>.
2. <task>. Verify: <check>.

Stop and report instead of continuing if:
- an expected-state item does not hold;
- a permission is denied (do not retry it reworded or by another route);
- an irreversible command errors (it runs once; do not rerun it, report the output);
- the work needs a file, system or action outside this brief.

Claims I will check myself: <diff path, HTTP status, sent row, row id>.

Your final message must contain, in under <1000 to 2000> tokens: <X>, <Y>, <Z>, and
anything you could not do with the reason.
```

Dispatch it with an explicit model, for example `model: opus` (or `haiku` plus
`effort: low` for a mechanical pass). Record the returned agent id beside the scratch path
in the event log.

## Example 1: edit lane

```text
You are an Opus worker repairing broken local links on the static site in /srv/site/,
so that the next deploy ships with no dead links.

Hard rules:
- Do not change any visible wording. Only href values may change.
- No commits, pushes or deploys.
- Keep every Bash output under 4 KB.
- Do not guess. Cite file:line for every claim.

Sensitive files and credentials:
- Never read /srv/site/.env or anything under /srv/site/private/.

Scratch: /srv/site/scratchpad/link-fix/ (create it).

You may edit only:
- /srv/site/index.html
- /srv/site/demos/index.html
Everything else is read-only.

Expected state (verify first, do not assume):
- /srv/site/index.html has 214 lines.
- `git -C /srv/site status --short` is empty.
If either does not hold, stop and report.

Decisions already made (do not revisit):
- A link whose target was renamed points to the new name. A link whose target is gone is
  removed with its anchor text left as plain text.

Work:
1. List every local href in the two files and test that each target exists.
   Verify: a table of file:line, href, result.
2. Fix only the failing paths, following the decisions above.
   Verify: rerun step 1 and show zero failures.
3. Save the diff to scratchpad/link-fix/links.diff.
   Verify: `wc -l` on the file.

Stop and report instead of continuing if: an expected-state item fails, a permission is
denied, or a fix would need a file not listed above.

Claims I will check myself: links.diff and the rerun table.

Your final message must contain, in under 1000 tokens: the files changed, each fix as old
path and new path, the rerun table, and anything you could not fix with the reason.
```

## Example 2: draft-then-stop send lane (Pattern 1)

Script names are placeholders; use the project's own send tool.

```text
You are an Opus worker drafting the reply to support ticket 4812 in the helpdesk at
/srv/helpdesk/, so the customer gets an accurate answer today.

Hard rules:
- Draft only. Do not send anything in this run. The dry run is the last action.
- Do not change the ticket's status, tags or assignee.
- Every fact in the reply must come from the ticket thread or the orders database
  (read-only). If a fact is missing, name the gap instead of filling it.
- Keep every Bash output under 4 KB. Do not guess. Cite file:line, ticket id or row id.

Sensitive files and credentials:
- Never read /srv/helpdesk/.env or print the SMTP password. Never paste a customer's
  payment details into the scratch directory.

Scratch: /srv/helpdesk/scratchpad/ticket-4812/ (create it).

You may write only inside the scratch directory. Everything else is read-only.

Expected state (verify first, do not assume):
- Ticket 4812 is open and its newest message is from the customer, dated 2026-03-02.
- Order 99031 exists and has status "shipped".
If either does not hold, stop and report.

Decisions already made (do not revisit):
- Offer a replacement, not a refund. The operator decided this on the ticket.
- Plain text, no links, signed with the team name.

Work:
1. Read the thread and order 99031. Verify: list each fact you will use with its source.
2. Draft the reply to scratchpad/ticket-4812/reply.txt.
   Verify: every sentence traces to a fact from step 1.
3. Run the style check and the dry run, as two separate commands:
   `scripts/style_check.py scratchpad/ticket-4812/reply.txt`
   `scripts/send_reply.py --ticket 4812 --body-file scratchpad/ticket-4812/reply.txt --dry-run`
   Verify: the style check prints no findings; the dry run shows the recipient and body.
4. Stop. Do not send.

Stop and report instead of continuing if: an expected-state item fails, a fact is
missing, a permission is denied, or the dry run errors.

Claims I will check myself: reply.txt and the dry-run output.

Your final message must contain, in under 1000 tokens: the exact reply text, the
recipient the dry run showed, the style-check output, and any gap you named.
```

After reading the draft, the overseer messages the same agent id with the exact final
text and one instruction: "Write this text to reply.txt, rerun the dry run, then send it
once with the same command without `--dry-run`. If the send errors, do not rerun it.
Return the sent row." Then the overseer reads the sent row and the recipient.

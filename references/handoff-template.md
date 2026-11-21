# Handoff template

Path: `docs/journal/<YYYY-MM-DD>-handoff.md` unless the project names another. The
overseer writes it, because only the overseer holds the plan. Write one before any planned
stop and whenever the context runs long. After any decision that changes the backlog,
append a dated addendum in the same turn, since compaction keeps a summary, not the
detail.

The handoff is a pointer. When it disagrees with a data file, the data file wins.

Take every time from `date` in the same command that writes the line, never typed.

```markdown
# Handoff <YYYY-MM-DD HH:MM TZ from date>

## State of the world
- Where the work stands in three to six lines.
- Data files that hold the truth, by path: <tracker, database, log>.
- Last event-log line at the time of writing: <quote it>.

## Restart order
1. Read this file end to end.
2. Run `date`.
3. <project's restart steps, as exact commands>

## Live backlog (exact commands, in order)
1. <what> : `<exact command>` . Done when: <check>.
2. ...

## Lanes in flight
| Lane | Agent id | Model | Scratch path | Waiting for | Next step when it reports |
|---|---|---|---|---|---|
| <slug> | <id> | opus | scratchpad/<slug>/ | <its final report> | <review step> |

## Schedules and monitors
| Purpose | Id | Cron | Created | Expires | Prompt file |
|---|---|---|---|---|---|
| <daily check> | <8-char id> | <expr> | <date> | <date + 7 days> | <path to verbatim prompt> |
- Background commands and monitors to start on restart: <exact commands>.
- Stopped by the operator, do not restart: <name, date, operator's words>.

## Waiting on the operator
- <decision or permission>, staged as: `<exact command>`. Recommendation: <one line>.

## Never do without the operator's word
- <action class>, because <reason>.
- Any action the operator denied this session: <list, with date>.

## Addenda
### <YYYY-MM-DD HH:MM from date>
- <decision that changed the backlog, and what changed>.
```

## Event-log line

One line per event (dispatch, decision, send, deploy, refusal), newest last. Workers
append lines for actions they take; the overseer appends its decisions. Example shape:

```bash
echo "$(date '+%Y-%m-%d %H:%M') dispatch link-fix agent=<id> scratch=scratchpad/link-fix/" >> <event log path>
```

## Before editing any state file

```bash
cp <file> "<file>.bak-$(date +%Y%m%d%H%M%S)-<why>"
```

Change the smallest thing, then read the write back.

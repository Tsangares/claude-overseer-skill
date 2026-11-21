# overseer

A Claude Code skill for running a session as the coordinating seat of long or multi-agent
work. The overseer decides, briefs subagents, reviews their claims against the end state,
and does the final read of anything a human receives. Workers do the reading and the
changes. The skill gives a context budget, a brief template, two shipping patterns, review
rules, and a handoff format.

## Files

- `SKILL.md`: the rules. Kept under 5,000 tokens so it survives compaction whole.
- `references/brief-template.md`: fill-in brief and two complete example briefs.
- `references/review-checklist.md`: claim-to-check table and the two shipping patterns as
  numbered steps.
- `references/handoff-template.md`: handoff sections, event-log line, state-file backup.
- `references/sources.md`: every cited source, what it supports, and the fetch date.

## Install

Clone this repository, or copy this folder, to one of:

- `~/.claude/skills/overseer/` for every project on the machine.
- `<project>/.claude/skills/overseer/` for one project.

## Invoke

Type `/overseer`, or say "be the overseer", "drive subagents", or "delegate this and
review". Claude also loads it on its own when the description matches the task.

## Make it the default for a session

Add one line to the project's or the user's CLAUDE.md:

```text
When the session model is any claude-fable-* model, or the operator says "overseer", load the overseer skill and follow it for the rest of the session.
```

## Changelog

- 2026-11-21: first release as a skill, from an earlier rules file (2026-11-01). Changed:
  the role is tied to the seat, not the model; Fable 5.2 ids and prices replace Fable 5.1
  (Fable now costs twice Opus per token, down from 2.5 times); one unconfirmed quote
  dropped; personal and project details removed. Added: expected state and stop
  conditions in briefs, run-once irreversible commands, credential rules in every brief,
  the two shipping patterns, resuming lanes by agent id, reloading deferred tools after
  compaction, an explicit model on every Agent call, permissions and trust rules,
  scheduled-turn rules, handoff addenda, operator reporting rules, and anti-patterns.

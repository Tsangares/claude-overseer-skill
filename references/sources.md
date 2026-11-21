# Sources

Every source below was fetched on 2026-11-21 unless marked otherwise. Quotes in the skill
were checked against the page on that date. Recheck model ids and prices before quoting
them; they change with each release.

1. https://www.anthropic.com/claude/fable
   Supports: the current Fable model and its id (`claude-fable-5-2` on 2026-11-21) and its
   price ($8 in, $40 out per million tokens, "down from $10 and $50 for Fable 5.1").
   Not found on 2026-11-21: the earlier quote "take hours and span many applications". The
   skill no longer uses it. The nearest wording on the page is "Tackle days-long, complex,
   and asynchronous tasks previous models couldn't sustain."

2. https://code.claude.com/docs/en/model-config
   Supports: the aliases (`opus`, `sonnet`, `haiku`, `fable`, `best`); on the Anthropic API
   `opus`, `sonnet` and `haiku` resolve to Opus 5.5, Sonnet 5.5 and Haiku 5.5, and `fable`
   resolves to Fable 5.2 (Fable 5 in Claude apps gateway sessions); Fable models are
   "suited to tasks larger than a single sitting"; safety fallback reruns flagged requests
   on an Opus model, and "After a fallback, the session continues on the fallback model";
   effort levels `low` to `max`.

3. https://platform.claude.com/docs/en/about-claude/models/overview
   Supports: current ids and base prices per million tokens on 2026-11-21:
   `claude-fable-5-2` $8 / $40, `claude-opus-5-5` $4 / $20, `claude-sonnet-5-5` $2 / $10,
   `claude-haiku-5-5` from $0.10 / from $0.50. Fable 5.1 is still listed at $10 / $50.
   A Fable 5.2 token costs twice an Opus 5.5 token; under Fable 5.1 it was 2.5 times.

4. https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents
   Supports: "the smallest set of high-signal tokens that maximize the likelihood of your
   desired outcome"; context rot (recall drops as context grows); subagents return "a
   condensed, distilled summary of its work (often 1,000-2,000 tokens)"; keeping
   "lightweight identifiers (file paths, stored queries, web links, etc.)"; notes kept
   outside the context window, such as a NOTES.md file; compaction restarts from a summary.

5. https://www.anthropic.com/engineering/multi-agent-research-system
   Supports: each subagent "needs an objective, an output format, guidance on the tools and
   sources to use, and clear task boundaries"; without them agents "duplicate work, leave
   gaps, or fail to find necessary information"; parallel subagents "cut research time by
   up to 90% for complex queries"; multi-agent systems use about 15 times the tokens of
   chats; agent count by complexity (1 agent for fact-finding, 2 to 4 for comparisons,
   more than 10 for complex research); the lead agent saves its plan to memory because a
   long context gets truncated (stated in a diagram caption); subagents store work in
   external systems and "pass lightweight references back"; "evaluate whether it achieved
   the correct final state".

6. https://code.claude.com/docs/en/sub-agents
   Supports: each subagent "starts with a fresh, isolated context window"; "On an Agent
   tool call, the parameter takes a model alias, not a full model ID or `inherit`" (a
   definition file's `model` field does accept a full id); with no model set, a subagent
   falls back to the main conversation's model; "a subagent that Claude spawns from your
   main conversation starts in the background"; a fork "inherits the entire conversation"
   and runs on the parent's model (it also shares the parent's prompt cache); a completed
   subagent resumes "with the `SendMessage` tool" and keeps "full conversation history";
   after a restart, message by agent id, since a name given before the last compaction
   does not survive; an `effort` parameter on the Agent call.

7. https://cognition.com/blog/dont-build-multi-agents
   Supports: "Actions carry implicit decisions, and conflicting decisions carry bad
   results", so every decision goes into the brief and lanes that share one are sequenced.

8. https://code.claude.com/docs/en/best-practices
   Supports: a fresh subagent "can end up slower than doing the task where you are";
   subagents cost mostly tokens; a reviewer subagent "that never saw your conversation"
   "isn't swayed by the reasoning that produced the code"; "Handing the same task to a
   subagent doesn't make the answer any better. A fresh reviewer does help". Also the
   counterpoint the skill now reflects: running out of context "is rare" on current
   models, but "a focused context gets you better answers and costs less", and long output
   "would otherwise ride along for the rest of the session". The same page drops the old
   advice to use capitals for emphasis.

9. https://www.anthropic.com/engineering/building-effective-agents
   Supports: "finding the simplest solution possible, and only increasing complexity when
   needed"; orchestrator-workers suits "complex tasks where you can't predict the subtasks
   needed".

10. https://code.claude.com/docs/en/scheduled-tasks
    Supports: session schedules (CronCreate, CronList, CronDelete, `/loop`) fire only while
    the session runs; recurring tasks "expire 7 days after creation"; no catch-up for
    missed fires; recurring runs start up to 30 minutes late; each task has an
    8-character id; background commands and monitors are not restarted on resume.

11. https://code.claude.com/docs/en/agent-sdk/tool-search
    Supports: loaded tool definitions "stay available for subsequent turns until the SDK
    compacts the messages where the agent discovered them. After that compaction, the agent
    searches for those tools again when it next needs them."

12. https://code.claude.com/docs/en/context-window
    Supports: what survives compaction. Invoked skill bodies are re-injected "capped at
    5,000 tokens per skill", and truncation "keeps the start of the file, so put the most
    important instructions near the top of SKILL.md". Background subagents keep running.

Sources 1 to 9 were first cited on 2026-11-01 and refetched on 2026-11-21. Sources 10 to
12 are new on 2026-11-21.

---
name: bridgeapp-investigation
description: Use when the user asks what is going on with a bug, a regression, or a surprising behaviour and the trail runs through BridgeApp — a reported task, a chat thread, a comment — as well as the code. Produces an evidence-backed diagnosis, not a fix.
---

# Investigating with BridgeApp as a source

Your job is to find out what is going on and explain it with evidence. Not to
fix it.

**Do not change code, tasks, or documents while investigating.** An
investigation returns knowledge, not side effects. A fix is a separate step the
user decides on after reading your report.

## What you have here that a server-side agent does not

You are running on the user's machine: the repository, its history, the build,
the tests are all in front of you. BridgeApp adds the half that is missing from
the code — who reported it, when it last worked, what was decided and by whom.

Use both. A regression usually shows its symptom in a thread and its cause in a
diff, and neither source alone explains it.

## Principles

**Evidence over theory.** Every claim traces to something you actually saw: a
line of code, a commit, a task comment, a chat message, a CI log. Anything you
did not verify is labelled a hypothesis.

**Reproduce, then localize, then explain.** Establish what actually happens
against what was expected, narrow down where it comes from, and only then say
why. Jumping to "why" produces confident wrong answers.

**Think in timelines.** Most problems are regressions. "When did it last work?"
and "what changed between then and now?" is usually the shortest path to the
cause — and BridgeApp holds the first half of that answer.

**Follow the trail across systems.** A task references a thread, the thread
references a task key, the key names a feature, the feature lives in a file.
Walk the links rather than guessing which is relevant; see `bridgeapp-context`
for how far to walk.

**Do not stop at the first plausible cause.** Check that it explains every
symptom. If one does not fit, keep going — a fix built on a half-right diagnosis
costs more than the bug.

## Gathering from BridgeApp

Start where the user pointed you and read it fully, including comments. Then
widen: `tasks_search` for the same feature or error text, `list_task_comments`
on anything that looks related, `get_task_plan_context` when the work has a plan
document, and the thread around the reporting message.

Prior discussion often contains the diagnosis already. Search before you reason.

## Reporting

```markdown
## Investigation: [one-line problem statement]

**TL;DR** — what is wrong and why, in one to three sentences, with a
confidence level: confirmed, likely, or hypothesis.

### What happens
Expected against actual, who is affected, since when.

### Root cause
The mechanism, step by step. Name exact files and lines, commits, tasks,
messages.

### Evidence
- `path/file.ts:123` — the check that rejects X
- commit `abc123` — introduced it in v1.2.3
- BridgeApp task or thread — where it was reported or decided

### Ruled out
What you checked and why it does not fit. This saves the next person from
re-walking your dead ends.

### Suggestions
Prioritized options with effort and risk. Recommend one. If a hotfix and a
proper fix differ, give both.
```

Keep it as short as the problem allows. Link BridgeApp objects as URLs, not
mention tokens — you are writing into a terminal, not into the app. See
`bridgeapp-links`.

## Anti-patterns

- Stating a theory as fact without a confidence label.
- Diagnosing from a function name or a PR title instead of reading the code.
- Stopping at the first plausible cause.
- Dumping raw search results instead of a synthesized report.
- Fixing things along the way. Investigation is read-only.
- Asking the user what the task history already answers.

If you cannot reach a confident diagnosis, report the strongest hypothesis, what
evidence is missing, and exactly what would settle it. A precise "here is what
would confirm this" is a real result. A confident guess is not.

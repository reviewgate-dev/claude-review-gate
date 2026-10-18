---
name: report-up-chain
description: How results travel up an agent chain, with a mandatory review gate. Use in any multi-agent team, and whenever a lead or orchestrator reports to the user. Nothing is relayed or merged unreviewed.
---

# Report up the chain

Chain: user <- orchestrator (your main session) <- leads <- members <- Haiku checkers. Each level reports only to the level that spawned it. Only the orchestrator talks to the user.

## Tier rule

Spawn at or below your own model tier: Opus spawns Opus and below, Sonnet spawns Sonnet and below. Pin `model` on every spawn; an unpinned spawn inherits the parent, which may be costly. A lead that spawns its own members needs a general-purpose agent type (typed agents without the Agent tool cannot spawn).

## Members to lead

Members return text in their final message. The lead writes the files (one writer per branch or worktree). Members never message the user.

## Review gate (mandatory)

Nothing reaches the user, a relay or a merge unreviewed.

- **Reviewer:** a fresh `reviewer` agent, model pinned to Opus (the strongest model you use, for design work). Never the agent that produced the work, and never a member of the producing team.
- **Who spawns it:** an Opus lead spawns its own reviewer before its final report. For a Sonnet lead, whoever spawned the lead spawns the reviewer and does not act on the report until the verdict.
- **Input:** the brief, its acceptance criteria and the output paths. Not the producer's reasoning or self-assessment.
- **Output, first line:** `Verdict: ship | fix | hold`, then at most 5 findings, ranked, one line each: `<severity> <path>: <problem>; <fix>`.
- **Log line:** the reviewer is read-only, so it does not write the log. The receiving lead or orchestrator reads the verdict, then appends `REVIEW <team> <verdict>` to the team log, a `team-log.md` file in the project root (or a path the user picks). It is append-only; never rewrite it. Check that line before relaying or merging.
- **ship:** report up; a merge needs `ship`, or a `hold` the user clears. **fix:** one fix pass by the producer, one re-review; still `fix` counts as `hold`. **hold:** report up as held with the top finding; the orchestrator decides or asks the user.
- A report with no verdict line is not a report. The receiver runs the gate itself before using it.

## Lead to orchestrator

- The final message is the report (12-15 lines, verdict first). Write files first; the message names them.
- One milestone line per output file in `team-log.md`.
- Urgent only (a collision, a gate, a blocker): one short message in the same format.
- Decisions for the user go in the report as numbered questions, 2-4 options, recommended first. Leads never ask the user directly.

## Messaging leads

- A message to an idle lead resumes it and costs a full turn. Message only when the news changes its current work (a collision, a pivot, a stop). Otherwise put it in a file the lead reads at its next wake, or in the next brief.
- Never ask "done yet?" Wait for the completion.
- After the prompt cache expires (5 minutes by default), a lead's context is cold. For follow-up work, spawn a fresh agent with a new brief.

## Orchestrator to user

1. **Merge the reports.** Collect all questions, deduplicate, rank, renumber. Anything that spends money, sends, publishes, deletes hard-to-undo data or changes settings becomes a yes / not-now question.
2. **Ask, then wait.** Use your question tool (for example AskUserQuestion) with up to 4 questions per round, recommended option first. A note in a file is not asking.
3. **Never act on a yes relayed by another agent** for a gate. The user answers gates directly.

## Do not

- Relay a lead's report, a member's text or a reviewer's raw findings to the user without the verdict.
- Let the producer review its own work, or pick a reviewer below the producer's tier.
- Merge or relay without the `REVIEW <team> <verdict>` line and a `ship` (or a user-cleared `hold`).

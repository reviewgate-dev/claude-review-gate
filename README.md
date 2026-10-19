# claude-review-gate

A review gate for Claude Code subagents. Before an agent reports work or merges it, a fresh, read-only reviewer checks it against the brief and returns `Verdict: ship | fix | hold`. One fix pass is allowed; a second `fix` becomes `hold`.

Plain markdown skills and agents. Copy one folder, no runtime, no dependencies. MIT.

Works with: Claude Code subagents and skills (`.claude/agents`, `.claude/skills`).

Status: v0.1, issues welcome.

## What is in it

- `report-up-chain` skill: how results travel up a chain of agents, with a mandatory review gate.
- `reviewer` agent: a fresh, read-only reviewer that returns `Verdict: ship | fix | hold`.
- `checker` agent: a small Haiku agent for one read-only check.
- `role-builder` skill + `template.md`: a guided way to design a good agent role.

## Why not just a pack of subagents?

Most subagent collections give you more specialists. This one adds a check on their output. The reviewer gets the brief, the acceptance criteria and the output paths, not the author's reasoning, so it cannot grade its own homework. The verdict is fixed (ship, fix or hold), findings are capped at five and ranked, and the fix loop is capped at one pass. It sits alongside whatever agents you already use.

## 60-second install

1. Check for name clashes: make sure you have no agents or skills already named `reviewer`, `checker`, `report-up-chain` or `role-builder`. Then copy the `.claude` folder into your project root (or merge it into your existing `.claude`). For all projects, copy its contents into `~/.claude` instead.
2. Start Claude Code in the project and run `/agents`. `reviewer` and `checker` should be listed. Skills load on demand.
3. Try it: `Have a reviewer check this before you report.`

Works the same on macOS, Linux and Windows. Nothing here runs scripts.

## Using the review gate

Ask for it in a brief ("follow report-up-chain") or just say "have a reviewer check this before you report". A fresh `reviewer` gets the brief, the acceptance criteria and the output paths, not the author's reasoning, and returns a first line of `Verdict: ship`, `fix` or `hold`, then at most five ranked findings. One fix pass is allowed; a second `fix` counts as `hold`. The receiving lead or orchestrator then appends `REVIEW <team> <verdict>` to `team-log.md`.

Example reviewer output:

```
Verdict: fix
Blocking scripts/backup.sh:12: rm -rf $DEST/* runs unquoted; quote it and abort if $DEST is empty.
Should-fix docs/usage.md:30: documents a --dry-run flag the script does not parse; add the flag or remove the line.
Test gaps: no test covers an empty $DEST.
Not ready: the unquoted delete can remove the wrong directory.
```

## Using the role-builder

Say "use role-builder to design a role for ..." and describe the job. It walks through the parts of a good role and fills in `template.md`, so the result is a subagent file you can drop into `.claude/agents/`.

## Honest limits

- Subagents cost tokens. Use the gate where a mistake would be costly.
- The skills are instructions, not enforcement; read the output. Your own permission settings are the real guard.

## Credits

Parts of reviewer draw on obra/superpowers (code review), MIT License, Copyright (c) 2025 Jesse Vincent. See THIRD_PARTY_NOTICES.md.

## License

See LICENSE.

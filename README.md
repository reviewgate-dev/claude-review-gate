# Agent Review Gate (lite) for Claude Code

A small, free set of plain-markdown skills and subagents for Claude Code: a review gate so nothing is reported or merged until a fresh reviewer has checked it, and a role-builder to design new agent roles. No install, no runtime, no dependencies.

## What is in it

- `report-up-chain` skill: how results travel up a chain of agents, with a mandatory review gate.
- `reviewer` agent: a fresh, read-only reviewer that returns `Verdict: ship | fix | hold`.
- `checker` agent: a small Haiku agent for one read-only check.
- `role-builder` skill + `template.md`: a guided way to design a good agent role.

## 60-second install

1. Check for name clashes: make sure you have no agents or skills already named `reviewer`, `checker`, `report-up-chain` or `role-builder`. Then copy the `.claude` folder into your project root (or merge it into your existing `.claude`). For all projects, copy its contents into `~/.claude` instead.
2. Start Claude Code in the project and run `/agents`. `reviewer` and `checker` should be listed. Skills load on demand.
3. Try it: `Have a reviewer check this before you report.`

Works the same on macOS, Linux and Windows. Nothing here runs scripts.

## Using the review gate

Ask for it in a brief ("follow report-up-chain") or just say "have a reviewer check this before you report". A fresh `reviewer` gets the brief, the acceptance criteria and the output paths, not the author's reasoning, and returns a first line of `Verdict: ship`, `fix` or `hold`, then at most five ranked findings. One fix pass is allowed; a second `fix` counts as `hold`. The receiving lead or orchestrator then appends `REVIEW <team> <verdict>` to `team-log.md`.

## Using the role-builder

Say "use role-builder to design a role for ..." and describe the job. It walks through the parts of a good role and fills in `template.md`, so the result is a subagent file you can drop into `.claude/agents/`.

## Honest limits

- Subagents cost tokens. Use the gate where a mistake would be costly.
- The skills are instructions, not enforcement; read the output. Your own permission settings are the real guard.

## Credits

Parts of reviewer draw on obra/superpowers (code review), MIT License, Copyright (c) 2025 Jesse Vincent. See THIRD_PARTY_NOTICES.md.

## License

See LICENSE.

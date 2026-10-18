---
name: role-builder
description: Guide for building a good role (agent file). Use when the user says "build a role", "new agent", "add someone to the team", or a lead proposes a new member.
---

# Role builder

Build one agent file in `.claude/agents/<id>.md` that slots into a team without new rules. Fill-in skeleton: [template.md](template.md). Read one existing neighbour in `.claude/agents/` first and match its shape.

If an existing agent already does the job, reuse it. A reusable method (not a persona) belongs in a skill, not a role.

## Checklist (every role has all nine)

1. **Purpose.** One sentence tied to the team's goal.
2. **Team map.** A `## Your team` table: Who | Ask them for | Reports to. List the boss, teammates and members it may call. Names only of agents that exist.
3. **Reporting line.** Reports to its boss only, never the user directly (see the report-up-chain skill). Say what the boss gives it: brief, paths, stop time.
4. **Peers.** Members of one team may ask each other directly but still report to their boss. Leads talk to their boss and their own members only.
5. **Model tier and ceiling.** Haiku for small read-only checks (keep context small); Sonnet as the default builder; Opus only for leads or judgment. State the ceiling and "never self-escalate; stuck means report". Spawn at or below own tier and pin `model` on every spawn.
6. **Tools.** The least needed. Read-only roles get no Edit or Write. Members rarely need Bash. A role that spawns others needs the Agent tool.
7. **Write scope.** Name the folder or branch it may write. Never shared logs by rewrite (append only), never other teams' folders, never settings.
8. **Gates and stop points.** Name the role's last safe step: one step before publish, send, delete or spend. Leads add the review gate from the report-up-chain skill.
9. **Handoff.** Final report format and length (leads: numbered decision questions).

Frontmatter: `name`, third-person `description` (what, when, boss), `model`, `tools`. Body: team line, personality line, job, your team, rules, report.

## Steps

1. Draft from [template.md](template.md). Keep the body under about 40 lines.
2. Update your team chart (a README or team doc) with the new role and its boss.
3. Have a fresh `reviewer` check the file against the nine points above, given the path only. Merge on `Verdict: ship`.
4. A new role is a proposal until the user accepts it. No role grants itself spend, send, publish or delete rights.

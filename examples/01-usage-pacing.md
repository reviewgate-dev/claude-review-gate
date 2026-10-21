# Case 01: weekly usage pacing card

A lead asks an author for a card on a local dashboard that shows weekly token budget use and projects whether it lasts until the reset. Team name in the log: `pacing`.

Adapted from a real run. The reviewer's text was not saved, so the round-1 findings are rebuilt from the fix commit and the round-2 nits are illustrative. Round 1 shows 1 Blocking and 4 Should-fix; the real run had 1 blocker and 5 should-fix (two should-fix items are merged here to keep the 5-finding cap). Line numbers are plausible, not exact.

## 1. Brief (lead to author)

Goal: add a "weekly pacing" card to the Costs tab. Read `usage.json` (written by a status-line hook: `seven_day.used_percentage` and `resets_at`). Show percent used, percent left, a straight-line projection to the reset, and a per-day dollar estimate.

Acceptance criteria:
- Card shows used %, left %, projection, and when the limit resets.
- A reading older than 60 min is marked STALE. A reading from before the latest reset is not shown as current.
- The dashboard sync never waits on the slow per-day scan (about 45 s).
- Unit tests for the math and the cache. Screenshot at 1920x930, light and dark.

Output paths: `scripts/pacing.py`, `dashboard/index.html`, `tests/test_pacing.py`.

## 2. Author summary

Built `pacing.py` (`pace()`, `build()`, cached per-day estimate), the card in `index.html`, one new snapshot key, and 11 tests. Projection starts after 1 hour of the week. All tests pass. Screenshots attached for light, dark and stale.

## 3. Round 1: reviewer (fresh, Opus, got only the brief and paths)

```
Verdict: fix
Blocking scripts/pacing.py:155: reset check compares the reading's own timestamp to resets_at, so an old reading still shows as this week's after the reset has passed; compare against the real current time and return "no reading this week".
Should-fix scripts/pacing.py:212: a stale cache runs the 45 s scan inline, so the sync blocks; return the old values marked stale and refresh in a detached process with a lock.
Should-fix dashboard/index.html:640: at 100% used it still says "0% left" and "per day lasts until reset"; add a limitReached state and drop the run-out line.
Should-fix scripts/pacing.py:48: a projection after 1 hour of the week swings from 0% to 400%; raise the floor to 24 h and say "too early to project".
Should-fix dashboard/index.html:652: the reset shows only a time of day, which is ambiguous; show the weekday and date.
Test gaps: nothing covers a reading from before the reset, the cache states (missing, stale, fresh, corrupt) or limit reached.
Not ready: the reset check shows a dead week's number as current.
```

## 4. Fix pass (author, one pass)

| Finding | Change |
|---|---|
| Reset check | `build()` now uses the real current time; a reading from before the reset returns `ok: false` with a reason. |
| Sync blocks on scan | Added a lock with expiry, a 5 min back-off after a failed scan and an atomic cache write. The sync never scans; a stale cache returns old values with `stale: true`. |
| Limit reached | `pace()` sets `limitReached`; the card shows "limit reached" and no run-out time. |
| Early projection | Floor raised to 24 h; the card says "too early to project (N h into the week)". |
| Dated reset | New date helper: weekday, date and time on every reset line. |

Added 6 tests: limit reached, first-day note, reading after reset, missing file, cache states, back-off and expired lock. All pass. Screenshots retaken.

## 5. Round 2: reviewer (fresh, Opus, same brief plus the fix commit)

```
Verdict: ship
Nit tests/test_pacing.py:88: one assertion is weak (it passes on a failed refresh too); assert the cache file exists.
Nit scripts/pacing.py:230: no test of a successful refresh_ledger; add one with a temp projects folder.
Nit scripts/pacing.py:241: if the cache folder is not writable the card says "computing" forever; show a reason after the back-off.
Nit dashboard/index.html:671: a stale reading can show a run-out time already in the past; mark it "as of the reading".
Nit scripts/pacing.py:178: nothing warns when daily dollars pass the daily guard; add a warning row.
Test gaps: none that block.
Ready: every round-1 finding is fixed and tested; the nits are left open.
```

## 6. Log lines (lead appends to `team-log.md`)

```
REVIEW pacing fix
REVIEW pacing ship
```

The merge waited for the second line. Had round 2 said `fix` again, it would have counted as `hold`.

# Manual check: roster amendment 2, pablo-escobar takes the Bridge and Elite seat

Commit: abaf254 (the change; this checklist is the next commit)
Date: 2026-09-21
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen changed (the fixture id renamed; the previews under roster-seat-lock stand)

**What Clemenza asked for, in his words:** "Replace tom-hagen with
pablo-escobar (name "Pablo Escobar", role boss, families bridge and elite)
everywhere: roster, seat route /mcp/pablo-escobar, Stripe and Airtable hook
wakes, approve app leaders, instruction file (rename tom-hagen.md to
pablo-escobar.md), tests, and docs. Do not add tom-hagen to the roster; it is
reserved, not built. Same branch, same stop-before-merge rule."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Grep the repo for tom-hagen, Tom Hagen, TOM_HAGEN, kickup-tom outside family/agents | nothing in code, tests, docs, or fixtures | only Dave's instruction files still mention Tom Hagen (michael-corleone.md, and the retired rocco-lampone.md), untouched on purpose; the historical checklist for 0666419 keeps its text | yes |
| 2 | Roster | pablo-escobar, boss, bridge and elite; tom-hagen on neither list | roster.test.js pins the active and retired lists | yes |
| 3 | Seat route | /mcp/pablo-escobar binds a login and refuses other seats | connector.test.js seat tests, renamed | yes |
| 4 | Stripe and Airtable wakes | pablo-escobar | triggers.test.js over HTTP; hooks export the ids and roster.test.js checks them | yes |
| 5 | Leaders and kick ups | michael, tony, pablo, paulie; kickup-pablo | schedules LEADERS and table; approve status returns LEADERS | yes |
| 6 | Instruction file name | pablo-escobar.md; the file itself is not written | agents.CONFIG; runner refuses the seat before a run record until the file lands | yes |
| 7 | The suite and the gate | green | 88 tests; gate green on abaf254 | yes |
| 8 | Merge | none | the branch is pushed, main untouched, per the stop-before-merge rule | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people, in any draft, email, or
      record.
- [x] Minors appear by name and role only, including in fixtures and
      screenshots. None appear.
- [x] Dave's own input in JARVIS still wins over anything an agent changed.
- [x] No em dashes anywhere, including code comments and strings.
- [x] Nothing JARVIS related ran without Dave's go. Nothing deployed.

## What I would tell Dave in one line

Nothing is live from this yet; it waits on the merge.

## Notes

The deployed backend (main at 9396e80) still carries tom-hagen as the Bridge
seat until this merges. A Stripe or Airtable event in the meantime logs
event_skipped against tom-hagen, because tom-hagen.md does not exist.

**Result: pass**

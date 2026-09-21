# Manual check: an open row means an open verdict

Commit: e288a5c (the commit this change is built on; e288a5c itself shipped with a missing helper and crashed at the verdict, fixed here)
Date: 2026-09-21
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen

**What Clemenza asked for, in his words:** "manual.result must read open or
provisional while any checklist row is open, in all three repos."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Read every Steps row of the covering checklist | a Pass cell that is not a yes makes the verdict `open`, naming the rows | `openRows` parses the table; `device`, `pending`, `no`, empty all count as open; `yes, by proof` counts as closed | yes |
| 2 | Dry run against every existing checklist in three repos | the device rows show up, the finished ones do not | vapid-push row 9, web-push rows 11 to 14, bridge checklist row 4 open; qa-layer closed; two stale `pending merge` rows found and closed with what actually happened | yes |
| 3 | Open rows on OTHER checklists | still visible in every later report | `manual.openElsewhere` lists them by file and row | yes |
| 4 | Gate on the clean commit | green, manual pass, openElsewhere names vapid-push row 9 | The first cut, e288a5c, crashed at the verdict with `checklists is not defined` and was merged anyway because the shell chain did not stop on the gate's exit code; this commit adds the helper and the chain now stops. Result recorded in the published report | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins. No runtime file touched.
- [x] No em dashes.
- [x] Nothing JARVIS related ran without Dave's go.

## What I would tell Dave in one line

The report cannot say pass any more while a row is still waiting on your
phone.

**Result: pass**

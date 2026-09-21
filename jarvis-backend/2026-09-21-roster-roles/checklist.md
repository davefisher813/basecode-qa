# Manual check: role fixes on the live roster

Commit: 23568e7 (the code change; this checklist is the next commit)
Date: 2026-09-21
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen changed. The approve page reads roles from
the status route and renders whatever it is given.

**What Dave asked for, in his words:** "FIXES ON THE LIVE ROSTER. Branch, gate,
stop before merge, send to Tony (formerly Clemenza) for review first. 1. Roles:
michael-corleone, tony-soprano, pablo-escobar, paulie-cicero, frank-lucas are
"boss". christopher-moltisanti, silvio-dante, paulie-gualtieri are "capo",
reportsTo tony-soprano. Frank Lucas stays inactive; mark that separately, not
in the role field."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Read every active seat's role | five bosses, three capos, no other word | roster.test.js pins both lists by id and asserts their union is the whole active roster, so a seat cannot be missed | yes |
| 2 | Check the words that left | no seat carries "reserved" or "engineer" as a role | asserted for every active id | yes |
| 3 | reportsTo for the three capos | tony-soprano | already correct in agents.js before this change; now asserted rather than assumed | yes |
| 4 | reportsTo for the five bosses | dave | asserted | yes |
| 5 | Frank Lucas inactive, marked outside the role field | role boss, a separate active flag reading false | `active: false` on his roster entry only; `isActive(id)` returns false for him, true for the other seven, false for retired ids and unknown ids | yes |
| 6 | The flag reaches anything that reads the roster | a real boolean per seat, not a missing field | `agents.describe()` and the `family_status` roster both emit `active`; asserted for an active seat and the inactive one | yes |
| 7 | The flag does not quietly gate anything | permissions unchanged | frank-lucas is still a roster id and the permission table treats him as before; what stops him working is the missing instruction file, key, schedule, and hook, which is asserted | yes |
| 8 | The suite | green | 91 tests, three new | yes |
| 9 | The gate | green, manual pass | this run | yes |
| 10 | Merge | none | branch pushed, main untouched, per Dave's "stop before merge, send to Tony for review first" | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people, in any draft, email, or
      record.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins over anything an agent changed.
- [x] No em dashes anywhere, including code comments and strings.
- [x] Nothing JARVIS related ran without Dave's go. Nothing deployed.

## What I would tell Dave in one line

Roles are fixed on the branch and nothing is live yet; the live backend still
shows the old roles until Tony clears the merge.

## Notes

**One judgement call, flagged for Tony.** Dave said to mark inactive
"separately, not in the role field." He said mark, so I marked it and did not
make it enforce anything. `active: false` is a label: frank-lucas remains a
roster id that could read, log a step, and request an approval if something
called as him. Nothing does, because he has no instruction file and no key. If
the intent is that an inactive seat must be refused outright, that is a
different change and a bigger one, and it belongs in the permission table.

**Item 2 of Dave's three is not in this branch.** Writing
`family/agents/pablo-escobar.md` waits on Alfred's revised Phase A draft, which
is not final and which I have never been given. I will not invent an
instruction file. When the final text arrives the hard lines get carried
verbatim, as instructed.

**Result: pass**

# Manual check: role fixes on the live roster

Commit: bc6c240 (roles 23568e7, capo repo mapping 5f2f377, merged at bc6c240; this postscript is the next commit)
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
| 10 | Review | Tony reads the branch against the published report | approved at 1cf5e97: "Reviewed claude/roster-roles at 1cf5e97 against the published report. Approved." | yes |
| 11 | Record which repo each capo covers | the mapping in family/README.md, documentation only | 5f2f377: christopher-moltisanti on jarvis-rebuild and jarvis-backend, silvio-dante on bridge-app and the Bridge website, paulie-gualtieri on the facility platform; no key created, boot log still reads "Bearer keys: none" | yes |
| 12 | The gate on the tree being merged | green, manual pass | this run | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people, in any draft, email, or
      record.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins over anything an agent changed.
- [x] No em dashes anywhere, including code comments and strings.
- [x] Nothing JARVIS related ran without Dave's go. Dave delegated the
      restructure calls to Tony, and Tony gave the merge instruction.

## What I would tell Dave in one line

Roles are fixed on the branch and nothing is live yet; the live backend still
shows the old roles until Tony clears the merge.

## Notes

**The inactive question, now settled.** I built the flag as a label and
flagged the choice rather than assuming it. Tony ruled: "Inactive stays a label
only. No permission table change. Deploy two makes unseated OAuth read-only,
and frank-lucas has no seat URL, key, instruction file, schedule, or hook, so
he cannot act." No code changed as a result; the ruling matches what was
already built.

**Item 2 of Dave's three is not in this branch.** Writing
`family/agents/pablo-escobar.md` waits on Alfred's revised Phase A draft, which
is not final and which I have never been given. I will not invent an
instruction file. When the final text arrives the hard lines get carried
verbatim, as instructed.

**The merge and the deploy are not rows here, on purpose.** They happen after
this gate runs, so a row claiming them would be claiming a result nobody has
seen. Tony asked for the boot to be confirmed clean and for family_status to
show five bosses, three capos, and frank-lucas active false. That is verified
against the running backend after the merge and appended below, and it is
reported to Tony either way.

**Result: pass**

## Postscript, verified against the running backend

Merged to main at bc6c240 on Tony's instruction. Railway deployment
3bf8e3ba, created 23:21:27 UTC, SUCCESS at 23:22:08.

Boot is clean. No error beyond the pre-existing npm config warning that every
boot prints. Firestore ready, connector enabled, Gmail and Drive connected,
schedules and events still off, bearer keys still none. No roster reset line,
which is correct: that step ran once on 9396e80 and its marker document holds.

family_status, read from the live connector at 23:22, not from the diff:

| seat | role | active |
|---|---|---|
| michael-corleone | boss | true |
| tony-soprano | boss | true |
| christopher-moltisanti | capo | true |
| silvio-dante | capo | true |
| paulie-gualtieri | capo | true |
| pablo-escobar | boss | true |
| paulie-cicero | boss | true |
| frank-lucas | boss | false |

Five bosses, three capos, frank-lucas active false. Five retired ids still
listed for history. Seven pending approvals intact across the change.

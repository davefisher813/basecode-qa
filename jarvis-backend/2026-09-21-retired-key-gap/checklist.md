# Manual check: the retired app's stale key, logged, and a gate fix

Commit: 158a342 (row 4 below was left empty when 26a8a29 shipped; this checklist is re-pointed at the commit that closes it)
Date: 2026-09-21
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen

**What Clemenza asked for, in his words:** "Leave the old app's stale
hardcoded key alone. It is retired and Dave has deferred that whole codebase.
Log it in GAPS.md, do not fix it."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Read `index.html` line 35802 | a VAPID public key literal, the old one | present, differs from the pair set in Railway on 2026-09-20 | yes |
| 2 | Log it in `qa/GAPS.md`, Low, not fixed | one paragraph naming file, line, why it is stale, why it stays | added | yes |
| 3 | The gate's touched-file rule | a file main changed that this branch lacks must NOT count as touched | `changedFiles` now diffs `origin/main...HEAD`; found in jarvis-rebuild where a push to main made an untouched baselined law file lose its grandfathering | yes |
| 4 | Gate on the clean commit | green, manual pass | 26a8a29: tests, build, lint PASS, manual pass, published `jarvis-backend/2026-09-21-retired-key-gap/`. The cell was left empty at the time and the new open-row rule surfaced it in `openElsewhere`, which is what it is for | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. A
      public key is public by design; the private one is not mentioned.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins. No runtime file touched.
- [x] No em dashes.
- [x] Nothing JARVIS related ran without Dave's go.

## What I would tell Dave in one line

Nothing to do; the old app's push toggle would fail, and the old app is
retired.

**Result: pass**

# Manual check: roster restructure and seat lock, deploy one

Commit: 0666419 (the change; this checklist is the next commit)
Date: 2026-09-21
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no new screen. The approve page gained a retired name lookup only.

**What Dave asked for, in his words (through Clemenza):** "Dave approved both
items. You have the go." Items: the seat bound connector design as specified
in the Part 2 plan, and the state reset for tony-soprano and paulie-cicero
with a backup first. "Proceed with the build per your plan: full test list,
then deploy one."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Read the roster | eight active ids, five retired, work added, elite and facility kept | roster.test.js pins the exact lists; reused ids carry the new family, retired ids the old | yes |
| 2 | add_task and add_prospect as every id | only michael-corleone passes; every other active and every retired id refused | permissions.test.js, derived from the roster; connector.test.js over HTTP with keys and with a seat token | yes |
| 3 | Every write tool and run_agent as a retired id | refused; nothing written except the refusal log | connector.test.js: the enum refuses the argument for every caller including Dave; run_agent refuses vito-corleone | yes |
| 4 | read_log and read_approvals filtered by a retired id | the old rows come back | connector.test.js seeds al-neri and bobby-baccalieri rows and reads them back; approve.test.js reads bobby's pending approval and Dave rejects it | yes |
| 5 | resolve_approval as every agent | refused; Dave passes | permissions.test.js loops active and retired; mcp-tools.test.js proves the refusal is not a write | yes |
| 6 | Old tony-soprano row with family bridge, old paulie-cicero approval with family elite | unchanged after the restructure | connector.test.js and roster.test.js; nothing in the code rewrites a stored row | yes |
| 7 | Stripe and Airtable hooks, Gmail poll | wake tom-hagen, tom-hagen, michael-corleone | triggers.test.js over HTTP with a fake runner | yes |
| 8 | Add a connector on /mcp/tom-hagen and log in | the page says "Connecting seat: Tom Hagen", the token carries seat tom-hagen, refresh keeps it | connector.test.js: full OAuth dance with the resource, claims read from the JWT, refresh checked | yes |
| 9 | Use that token on /mcp/michael-corleone | 401 naming both seats, no secret in the body | connector.test.js asserts the body names the seats and contains neither the signing secret nor the token | yes |
| 10 | Log in with no seat in the URL | the picker; no seat picked is a 400; a retired id is a 400 | connector.test.js | yes |
| 11 | A token from before the lock | writes on /mcp while the cutover allows, refused on every seat path, read only after the flip | connector.test.js signs a legacy JWT and flips family/cutover.js in the test | yes |
| 12 | Engineer key on its own seat path and on another's | own passes and logs key:silvio-dante; other 401; Dave's key passes every seat | connector.test.js | yes |
| 13 | The state reset | backup to family_config/roster_reset_2026-09-21, both seats at default, other seats untouched, runs once | migrations.test.js; runs at boot only with Firestore, so the live reset happens on the first boot after deploy | yes |
| 14 | A seat whose instruction file has not landed | start refused with the file named, no run record | runner.test.js for silvio-dante; the six missing files are listed in GAPS.md | yes |
| 15 | The gate on the committed tree | green, manual pass | 0666419: tests, build, lint pass; 88 tests | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      seat page and the 401 body are asserted secret free; keys in tests are
      repeated letters.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people, in any draft, email, or
      record.
- [x] Minors appear by name and role only, including in fixtures and
      screenshots. None appear.
- [x] Dave's own input in JARVIS still wins over anything an agent changed.
      No memory route touched; the no-full-replace test still passes.
- [x] No em dashes anywhere, including code comments and strings.
- [x] Nothing JARVIS related ran without Dave's go. Dave's go came through
      Clemenza for both items. The reset runs on the first boot after deploy.

## What I would tell Dave in one line

After this deploys, every connector added before today still works as it
did; the lock only bites when you move a Project to its seat URL, and unseated
writes stay open until deploy two.

## Notes

**What is not in this deploy.** Six instruction files (tony-soprano,
tom-hagen, christopher-moltisanti, silvio-dante, paulie-gualtieri,
paulie-cicero). The runner and the hooks fail cleanly without them. Tony's and
Paulie Cicero's are with Dave (Phase A). michael-corleone.md still names Al
Neri and Rocco as his crew; Dave's file.

**kickup-tom.** Dave's decision said remove kickup-vito and nothing about
Tom. Without a Tom kick up, Bridge has no kick up and the Friday agenda reads
three leaders instead of four, so kickup-tom was added. It is off, like every
schedule, until FAMILY_SCHEDULES lists it. Say the word and it goes.

**The live reset.** It runs once at the first boot after this deploys, only
with Firestore, guarded by the marker document. The boot log line
"[FAMILY] Roster reset ran for tony-soprano, paulie-cicero" is the proof.

**Result: pass**

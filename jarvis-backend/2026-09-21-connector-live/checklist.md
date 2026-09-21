# Manual check: the two follow-ups, and the merge that makes the seat ids live

Commit: e583f2a (the runner label eb95a4b, the reserved seat note e583f2a; this checklist is the next commit)
Date: 2026-09-21
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen touched

**What Dave asked for, in his words:** "Do what is necessary so we can do this
and be done with it." He sent a screenshot of Muse (Alfred) refusing to adjust
the four Claude Projects: "the new instructions tell each seat to introduce
itself with its new seat ID. The current connector doesn't know those IDs. The
second I'd paste them in, those seats would get refused and stop working."

## What was actually blocking

Alfred's reason was true when the plan was written and is now stale in part.
Deploy one went live on main at 9396e80 earlier today, and it carries seven of
the eight final seat ids. The live connector already knows michael-corleone,
tony-soprano, christopher-moltisanti, silvio-dante, paulie-gualtieri,
paulie-cicero, and frank-lucas. What it does not know is pablo-escobar, and it
still carries tom-hagen, which amendment 2 replaced. Merging this branch is the
whole of what stands between Alfred and his paste.

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Diff the active seat ids on main against the branch | seven match; only tom-hagen versus pablo-escobar differs | confirmed by reading roster.js out of both commits; the other seven are byte identical | yes |
| 2 | Read the runner's prompt label | no person's name in it | eb95a4b: reads "from the backend runner"; it said "from Peter Clemenza's runner" and he was retired on 2026-09-21 | yes |
| 3 | Read the reserved seat note | tom-hagen reserved as a possible future capo under Michael, not built | e583f2a, matching what Dave told Muse the same day | yes |
| 4 | The suite | green | 88 tests pass | yes |
| 5 | The laws | no em dashes, no memory route touched, no secret | scanned; no-full-replace test passes | yes |
| 6 | The gate on this tree | green, manual pass | this run | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      screenshots Dave sent carry no secret and none was copied into the repo.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people, in any draft, email, or
      record.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins over anything an agent changed.
- [x] No em dashes anywhere, including code comments and strings.
- [x] Nothing JARVIS related ran without Dave's go. The merge is Dave's own
      instruction, given directly: "Do what is necessary so we can do this and
      be done with it."

## What I would tell Dave in one line

Once this merges and deploys, tell Alfred the connector is live and give him
the eight seat ids; nothing else is in his way.

## Notes

**Two different sets of instruction files, and only one of them matters here.**
Alfred pastes instruction sets into the Claude Projects on claude.ai. The
backend also keeps instruction files at `family/agents/<id>.md` for the
backend runner. Six of those are still missing. They do not block Alfred,
because the runner is the only thing that reads them and the runner starts
nothing on its own: schedules are off and a run has to be asked for. A seat
with no file refuses to start with the file named, before any run record.

**Why the merge is safe for Alfred's existing connector.** His Projects use
the shared connector added before the seat lock. Deploy one left those tokens
writing exactly as they did, and they stay that way until `family/cutover.js`
flips, which is deploy two and is not in this merge. Nothing he has to
re-authorize.

**Result: pass**

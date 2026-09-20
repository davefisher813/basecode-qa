# Manual check: the evidence trail matches what shipped

Commit: a4b6262 (the commit this change is built on; the change itself is the next commit)
Date: 2026-09-20
Checked by: Claude Code, in the repo
QA report: published to basecode-qa, not committed here
Preview: not applicable, no screen

**What Clemenza asked for, in his words, forwarded by Dave:** "The publisher
must refuse to publish when the working tree is dirty, and must record the
merge commit, not a branch head. Evidence that does not match what shipped is
worse than no evidence. ... pending is not a pass. Either fill the checklist
for the merged change or have the report state plainly that the gate verdict
is provisional. Set preview.applicable false for backend runs, not null."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Run the gate on a dirty tree, then `node qa/publish.js` | Report says `dirty: true`; publisher refuses and says why, nothing pushed | "PUBLISH REFUSED: the report is from a dirty tree at a4b6262. Nothing was pushed." exit 4 | yes |
| 2 | Read `manual` in that report | Names the checklist it looked at, its commit, its result line, and `provisional` with a reason | `provisional`, "2026-09-20-clemenza-review.md is for commit 90f03c1, not this one" | yes |
| 3 | Read `preview` in that report | `applicable: false` with a reason, not null | `applicable: false`, "no html or css touched" | yes |
| 4 | Commit the change, run the gate on the clean tree | `dirty: false`, `commit` is HEAD, `manual.result` is `pass` because this checklist's Commit line is HEAD's parent | `dirty: false`, `onMain: false`, manual `pass`, "2026-09-20-evidence-trail.md covers this commit and says pass", checklistCommit a4b6262 | yes |
| 5 | Check `qa/reports/` is gitignored and no report is tracked | `git ls-files qa/reports` prints nothing | five tracked reports removed with `git rm --cached`; `qa/reports/` in `.gitignore` | yes |
| 6 | Rerun the same commit on `main` after a fast forward merge | Only `onMain` and `branch` change | Not yet merged: Clemenza reviews first. The field is proven: it read `true` for a4b6262 (already on main) and `false` for this commit on the branch | pending merge |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      publisher's scan is unchanged and still runs before every push.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people, in any draft, email, or
      record. Nothing is sent to anyone.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins. No runtime file touched.
- [x] No em dashes. The gate's lint stage passed on the touched files.
- [x] Nothing JARVIS related ran without Dave's go. Nothing JARVIS ran.

## What I would tell Dave in one line

The report now says out loud whether a person has checked the commit it
describes, and it will not publish a report for a state that no commit has.

## Notes

**Why the checklist names the parent commit.** A checklist is committed with
its change, so it cannot name the commit it is part of. Its Commit line names
the commit the change was built on. The gate treats a checklist as covering
HEAD when its Commit line is HEAD or HEAD's parent; anything else is some
other change's checklist, and the verdict is provisional with the reason
stated.

**Why reports left the source repo.** Committing the report meant the report
always trailed its own commit by one and `dirty` was always true, which is
exactly the mismatch Clemenza called out. Reports now live only in
basecode-qa. The sequence is commit, run clean, publish, fast forward merge,
and `main` is then the commit the report names.

**Selection by time was wrong once.** The newest checklist by date and mtime
picked `clemenza-review` after an unrelated edit bumped its mtime. The gate now
picks the checklist whose Commit line covers HEAD, and only falls back to the
newest when none does, saying so.

**Result: pass**

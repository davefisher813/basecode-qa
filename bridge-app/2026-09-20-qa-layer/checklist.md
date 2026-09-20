# Manual check: the QA layer and the evidence trail

Commit: 8831012 (the commit this change is built on; the change itself is the next commit)
Date: 2026-09-20
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen

**What Clemenza asked for, in his words, forwarded by Dave:** "Wire
qa/publish.js into it and publish under commit-app/ in basecode-qa. That repo
is the Bridge app and I currently cannot see it at all. Once artifacts land
there I can work it without Dave relaying anything."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Baseline the repo's own commands before writing the gate | Numbers, not guesses | vitest 731 tests in 46 files in 7s, `tsc --noEmit` clean in 12s, `next build` clean in 27s with 61 routes | yes |
| 2 | `QA_PUBLISH=0 npm run qa:check` on the untouched tree | All four stages pass, under a minute | PASS tests 6.7s, types 11.2s, build 7.2s (cached), lint; 25s total; manual `provisional`, "no filled checklist" | yes |
| 3 | Plant a test file that throws on import, rerun | Tests stage fails and names the file | "1 failing", "1 test file(s) ran no tests: src/laws/zz-throws.test.ts", and the failed-as-a-whole-file line with the import error. 3 stages not run. Removed, green again | yes |
| 4 | Plant a secret shaped line in a doc, rerun | Lint fails, names the file and rule, prints no value | `no-secret-in-source:env-assignment docs/zz-planted.md: matched a secret shaped literal; the value is not printed`. Removed, green again | yes |
| 5 | Grep the whole repo for em dashes | Only the law's own two, held in baseline.json | `src/laws/laws.test.ts` 2, nothing else in ts, tsx, js, mjs, md, json, css, sql, sh, py | yes |
| 6 | Confirm no env file is tracked and no secret literal exists | Nothing | `git ls-files` has no `.env*`; the only key-like text is docs/SETUP_CHECKLIST.md telling Dave where to paste one | yes |
| 7 | Commit, run clean, publish | `dirty: false`, manual `pass`, folder `bridge-app/2026-09-20-qa-layer/` in basecode-qa | `dirty: false`, `onMain: false`, manual `pass` "covers this commit and says pass", scan clean, published `bridge-app/2026-09-20-qa-layer/` with report.json, checklist.md, gaps.md. 15.5s end to end | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      publisher's refusals print rule names and entry numbers only.
- [x] No real person in any fixture, preview, test or screenshot. This change
      adds no fixture and no preview. The deny list mechanism is in place for
      real names as a backstop; Dave fills it locally.
- [x] Multi-tenant: no table, no migration touched.
- [x] Kit and Title Case laws: the tests stage ran all 46 law and unit files, 731
      passing.
- [x] No em dashes anywhere. Zero in every new file, checked by the gate.
- [x] Nothing pushed unless Dave said "push" or "go". The branch is local until
      he does. Publishing artifacts to basecode-qa is not a push of this repo.
- [x] No new screen, so no preview owed.

## What I would tell Dave in one line

The Bridge app has the same gate as the backend now, it runs in under a
minute, and Clemenza can read its evidence without you.

## Notes

**The folder is `bridge-app/`, not `commit-app/`.** The GitHub repo is
`davefisher813/bridge-app` and package.json still says `recruiting-platform`,
a placeholder. The artifacts folder is named after the repo a reviewer can
open. If Dave renames the repo, the report's `repo` field follows.

**No boot stage, on purpose.** The app cannot serve a page without a Supabase
project. `next build` is the honest build and the same one Vercel runs. What
runs at runtime is the live driver's job, by hand, and is listed in GAPS.md
as High because RLS is the tenant boundary.

**The repo's own preview pipeline is not run by the gate.** It needs
Chromium, Python and esbuild. `preview.applicable` turns true when a screen
is touched and the shots are owed from that pipeline.

**Result: pass**

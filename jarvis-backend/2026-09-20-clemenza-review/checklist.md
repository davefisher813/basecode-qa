# Manual check: Clemenza review conditions

Commit: 90f03c1 (the change under review is uncommitted at check time)
Date: 2026-09-20
Checked by: Claude Code, in the repo
QA report: qa/reports/latest.json
Preview: qa/previews/qa-layer/

**What Clemenza asked for, in his words:** "the baseline is a committed file
listing exact files and exact counts. It may never increase. Any new or
modified file must be zero. Add a burn-down line to GAPS.md." Plus: "confirm
the fallback is dev only and cannot reach Railway, same bar as Playwright, and
say how you verified it." Plus: "do not change runtime code to make anything
testable. The app never bends to fit the harness."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | `npm run qa:check` clean | Four stages, pass | PASS tests, PASS build, PASS lint, N/A types, 19.4s | yes |
| 2 | Touch a baselined file without cleaning it | Lint fails and says the grandfathering is gone | Appended one comment to `server.js`: FAIL, "24 em dashes in a file this change touched. A baselined file loses its grandfathering the moment you edit it: clean all 24 or leave the file alone." | yes |
| 3 | Restore it, rerun | Green, and `git diff` on the file empty | PASS, diff empty | yes |
| 4 | Confirm untouched baselined files still hold | `server.js` 24 and `sw.js` 8 recorded, neither touched by this branch, so both pass | baselineTotal 32, findings [], touchedFiles 19 | yes |
| 5 | Confirm the burn-down is recorded | A dated table in GAPS.md | Three rows: 52 across 4 files, 32 across 2 after the deletions, 32 held today | yes |
| 6 | Confirm no runtime code changed for testability | Nothing exported to suit a test | `git diff main...HEAD` touches no file outside `qa/`, `test/`, `package*`, and the two dead files Dave ordered deleted | yes |
| 7 | Confirm the browser fallback cannot reach Railway | Confined to the preview, absent from the server | Paths appear only in `qa/preview.mjs`; loading `server.js` pulls 1587 modules, none under `qa/` and none Playwright; Railway runs `node server.js`; a clean production install has no Playwright | yes |
| 8 | Confirm the Google refresh gap is recorded with a severity | High, with the file and function named | GAPS.md High, `server.js`, `getGoogleAuth` ~313 and `getGmailClient` ~333, with why it stays untested | yes |
| 9 | `npm run qa:preview -- qa-layer` | Six PNGs at true 390 by 844 | Six written, each asserting its own rendered width | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      QA boot uses fake values from `env.qa.example`. The lint stage also
      scans committed reports for an auth header value. Checked all six PNGs.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with the protected people. This change sends nothing outward.
      Their names are deliberately NOT written into the repo as a grep; that
      stays this human check, and GAPS.md says so.
- [x] Minors appear by name and role only. No minor appears in the fixtures.
- [x] Dave's own input in JARVIS still wins. No route, response shape or
      config changed. The only runtime touch is booting the server read only
      against fake values and asking `/api/health`.
- [x] No em dashes, now enforced as a ratchet rather than a floor.
- [x] Nothing JARVIS related ran without Dave's go.

## Previews

| File | Look at |
|---|---|
| `approvals-light.png` / `approvals-dark.png` | Two pending cards, ids visible, the three decision buttons reachable with a thumb |
| `agents-light.png` / `agents-dark.png` | Four agents, working light on Tony, paused tag and Resume on Clemenza |
| `today-light.png` / `today-dark.png` | Three log entries with agent, action, result |

## What I would tell Dave in one line

Clemenza's three conditions are in, the baseline now bites anyone who touches
a grandfathered file, and nothing merged.

## Notes

**The net deletion Clemenza flagged was 69 percent one thing.** Of 1387
deleted lines, 954 are `index.js` and `token-persistence.js`, the dead v2.0
backend Dave ordered removed in a separate commit. The rest are rewrites of QA
files authored hours earlier in the same session, not removals of coverage.

**No prior QA layer was removed.** `check.js` and `preview.mjs` were rewritten
to the Build pass-off's own corrections and both got stricter.
`qa/boot-allow.txt` was replaced by `qa/boot-allowlist.json` per pass-off item
3, and its two regex entries are gone because deprecations are now judged by
stack frame origin instead of being allowlisted, which is the stronger check.
Nothing the old files caught is uncaught now.

**Result: pass**

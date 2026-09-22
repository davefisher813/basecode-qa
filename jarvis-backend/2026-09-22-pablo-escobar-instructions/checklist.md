# Manual check: the pablo-escobar instruction file

Commit: db426b6 (the change; this checklist is the next commit)
Date: 2026-09-22
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen

**What Dave asked for, in his words:** "Bro I'm so tired of this. WHAT DO YOU
NEED TO FINISH THIS SHIT JUST ASK ME IF YOU NEED TO." He was right that I was
blocked on nothing. Tony's plan stood: land Alfred's text verbatim, apply the
two hard line dedupes only if the old pairs are present.

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Compare the source against Tony's decision tree | both tightened lines present once each, old pairs absent, so no edits | both present once, all four superseded wordings absent, branch one of the tree | yes |
| 2 | Write the file | byte for byte from the source, zero edits | 175 lines, 17403 characters, no dedupe applied because none was needed | yes |
| 3 | Completeness of the source | no truncation at the end or in the middle | 24 headings in order, last heading TEST TASKS, last line a complete bullet ending in a period, skeleton matches the sibling files | yes |
| 4 | HARD LINES survived the merge of two source files | five rules, the union of the Bridge five and the Elite four | five, including "Dave's input in JARVIS always wins" which existed only in the Bridge file and was the one at risk | yes |
| 5 | Em dashes | none | zero em dashes and zero en dashes, scanned mechanically | yes |
| 6 | The config finds it and it loads verbatim | hasInstructions true, loadInstructions returns the file unchanged | seat-files.test.js asserts the loaded string equals the file on disk | yes |
| 7 | The runner stops refusing the seat | no "instruction file missing" error, and no run record from a refused start | asserted: the only remaining blocker on a start is the absent API key | yes |
| 8 | Preamble agrees with the file about the seat | names Pablo Escobar, roster id pablo-escobar, family tag bridge or elite | asserted, plus the locked Michael Corleone only write rule | yes |
| 9 | The file is never served and never published | 404 from the running server; not a publish candidate | qa-exposure.test.js extended to assert 404 on both seat files; publish.js sends a fixed four artifact allowlist and nothing under family/ is a candidate | yes |
| 10 | Minors rule, against the file's own content | no minor named anywhere | none. The file names board members, coaches and accountants only, and states the rule itself in PEOPLE | yes |
| 11 | The suite | green | 97 tests, six new in seat-files.test.js | yes |
| 12 | The gate | green, manual pass | this run | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      file carries one Airtable base identifier, which is an identifier and not
      a credential, and no tokens or keys of any kind.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people. The file names them in three
      places, all as prohibitions, and it never leaves the private repo.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins. No memory route touched.
- [x] No em dashes anywhere, including code comments and strings.
- [x] Nothing JARVIS related ran without Dave's go. Dave gave it directly.

## What I would tell Dave in one line

Pablo's file is in, word for word as Alfred wrote it, and the seat can run now.

## Notes

**Zero edits.** Alfred had already applied both dedupes in the live Project, so
the diff against the source has no hunks. The two lines are present once each
and none of the four superseded wordings survive anywhere in the file.

**Two conflicts carried in unchanged, both logged in GAPS.md for Tony.** The
file grants Airtable and Google Sheets Write and post trial Stripe actions,
while the seat's runtime tools are read only and the preamble says so. And it
tells the agent to use the multi-choice widget and never ask in prose, which
does not exist in a backend run. Neither is mine to change: the text carries
verbatim and Tony rules on the Project side.

**Two coverage holes closed while here.** The house em dash test read only the
top level of family/ and test/, so nothing under family/agents had ever been
scanned. It is recursive now and asserts it reached the subfolders. The
exposure test now also asserts 404 on both seat instruction files, which are
the most sensitive plain text in the repo.

**One plan item withdrawn.** I had flagged that `preamble` needed exporting
from runner.js for the dry run test. It was already exported. No production
file was touched by this change.

**Result: pass**

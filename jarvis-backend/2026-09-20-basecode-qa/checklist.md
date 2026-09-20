# Manual check: the artifacts publisher (basecode-qa)

Commit: 06ab22e (the change under review is uncommitted at check time)
Date: 2026-09-20
Checked by: Claude Code, in the repo
QA report: qa/reports/latest.json
Preview: not applicable, no screen

**What Clemenza asked for, in his words, forwarded by Dave:** "At the end of
every qa:check run in any repo, push the artifacts there. Gate the push on the
lint stage secret scan passing. If the scan fails, do not push, and say so."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | `QA_PUBLISH=0 npm run qa:check` | Gate still green with the publisher wired in but skipped | PASS tests, build, lint; N/A types; 19.8s | yes |
| 2 | Grep everything under `qa/` for the protected names and the protected topic | Nothing | Nothing. Before this change: GAPS.md named all four people, TEMPLATE.md named all four, and six files said the topic by name | yes |
| 3 | Put a term that appears in the checklist into `qa/publish-deny.txt`, run `npm run qa:publish` | Refused, entry number printed, term not printed, nothing pushed | "PUBLISH REFUSED: the secret scan hit 2 time(s)": `denied-term-1-of-1 in checklist.md` and `in gaps.md`. No network call was made | yes |
| 4 | Set an env var `FAKE_API_TOKEN` to a string that appears in the report, run `npm run qa:publish` | Refused, variable NAME printed, value not printed | "value-of-env-FAKE_API_TOKEN in report.json". Value absent from output | yes |
| 5 | `npm run qa:check` with the push enabled and no artifacts repo existing yet | Scan passes, push fails loudly as delivery, gate verdict unchanged | Not run here: this session's permission layer stopped it as a new public surface, which is the step that waits on Dave's go. Run once the repo exists | no, pending |
| 6 | Confirm the publisher ships no source | `collect()` lists only report.json, checklist.md, gaps.md and PNGs | Read the function: those four, nothing else, no glob over the repo | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      publisher's refusals print rule names and entry numbers only; verified in
      steps 3 and 4. `qa/publish-deny.txt` is gitignored.
- [x] Nothing about the protected topic left this machine. Its name was removed
      from every file under `qa/` because that folder is about to be public.
- [x] No contact with any of the protected people, in any draft, email, or
      record. Their names were removed from `qa/GAPS.md` and
      `qa/checklists/TEMPLATE.md` for the same reason. Nothing is sent to anyone.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins. No runtime file touched.
- [x] No em dashes. The gate's lint stage passed on the touched files.
- [x] Nothing JARVIS related ran without Dave's go. The public repo is not
      created and nothing has been pushed; that is the decision in front of him.

## What I would tell Dave in one line

The publisher is built and proven to refuse; the only thing it cannot do
without you is create the public repo.

## Notes

**Deviation from the spec, on purpose.** Clemenza said to gate the push on the
lint stage's secret scan. The lint stage only runs when tests and build have
passed, so gating on it literally would mean a failing run never reaches him,
and the failing report is the evidence he most needs. So the publisher scans
the exact bytes it is about to send, every run, pass or fail, with nine
patterns, every env value that looks like a secret, and the local deny list.
It also refuses if the lint stage did run and recorded a secret finding. Both,
not either.

**The deny list is not in the repo.** It cannot be: the repo is what gets
published. `qa/publish-deny.example` says what to put in it. Until Dave fills
`qa/publish-deny.txt` on the machine that runs qa:check, the publisher prints a
note saying no protected term was checked. It still refuses on every other
rule.

**The gate's exit code does not include the push.** A push that could not
reach GitHub is a delivery failure and is printed as one; the verdict on the
code stands on its own.

**Result: pass, with step 5 pending Dave's go**

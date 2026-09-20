# basecode-qa

**This repository is PUBLIC.** It holds QA artifacts only, so that a reviewer
who cannot read a private repo can still read its evidence.

What is here, per repo and per change, at `/<repo-name>/<YYYY-MM-DD>-<task>/`:

- `report.json`, the machine gate's report for that run
- `checklist.md`, the filled manual walkthrough
- `gaps.md`, what that repo's suite does not protect, at that point in time
- `previews/`, phone width screenshots when the change had a screen

What is never here: source code, configuration, environment files, keys,
tokens, or any secret. The publisher scans every byte before it pushes and
refuses on a hit. **Anything secret found in this repo is a defect. Report it,
do not commit around it, and rotate what leaked.**

Pushes come from `qa/publish.js` in each repo at the end of its `qa:check`
run. Nothing here is edited by hand.

# Gaps

What the suite does NOT protect. A green `qa:check` means the gate found
nothing wrong in what it can see; this file is the list of what it cannot see,
so nobody mistakes green for covered.

Severity is about what breaks for Dave if the code is wrong, not about how
hard the test would be to write.

| Severity | What it means |
|---|---|
| High | It fails silently, or it fails in a way Dave notices before we do. |
| Medium | It fails loudly and locally, and someone can tell what happened. |
| Low | Contained, or already guarded from another direction. |

## High

**The emailed code login path.** `family/oauth.js` issues the code, counts
attempts, and swaps it for a token. The refresh half of that file is covered
as of this change (`test/oauth-refresh.test.js`: rotation, expiry, wrong
client, unknown token, signature). The LOGIN half is not: nothing tests that a
wrong code is rejected, that attempts are capped, or that a code expires. If
this breaks, Dave cannot sign into the approve page, and he finds out when he
tries to approve something.

**Google token refresh. Severity: High.** It lives in `server.js`, not in
`family/oauth.js`, and that distinction is why the Plan asked for the wrong
test:

| | |
|---|---|
| File | `server.js` |
| Functions | `getGoogleAuth` (around line 313) and `getGmailClient` (around line 333) |
| Trigger | either one refreshes when `tokens.expiry_date` is inside 5 minutes |
| Call | `oauth2.refreshAccessToken()`, then `oauthTokens.save(email, credentials)` |
| Failure handling | `catch (e) { console.warn(...) }`, then the STALE client is returned anyway |
| Why it is untested | both are private functions in a file that ends in `app.listen` and exports nothing |

It stays untested on purpose. Testing it means exporting it, and the app does
not bend to fit the harness (Clemenza, 2026-09-20). The honest options are a
later refactor Dave approves on its own merits, or an HTTP level test with a
fake Google, and neither is worth a runtime change today.

The reason it is High: the failure is silent. A dead refresh token does not
raise, it logs a warning nobody reads and hands back a client that will return
nothing. Gmail and Drive look empty rather than broken.

`family/oauth.js` is a different thing entirely, the connector's own login and
refresh, and its refresh path IS covered now by
`test/oauth-refresh.test.js`: rotation, single use, expiry, wrong client,
unknown token, and signature.

**The spend cap, end to end.** `family/spend.js` is imported by the runner
tests, but nothing proves a run is actually refused at the cap, or that the
80 percent warning fires once rather than every run. The failure mode is money.

## Medium

**The Drive API enable path.** Added Sep 19: `checkDriveApi` in `server.js`
probes Drive and, on a disabled API, tries to enable it through Service Usage
and polls the operation. One smoke assertion covers the field in
`/api/health`. Nothing covers the enable branch, the polling, or the failure
logging. It already cost a wasted agent run once.

**The connector's tool handlers beyond the ones now covered.**
`test/mcp-tools.test.js` covers `callerFrom`, the read tools, the no-identity
refusal, and the rule that `family_resolve_approval` refuses an agent and
leaves the approval pending. Not covered: `family_add_task`,
`family_add_prospect`, `family_request_approval`, `family_log_step`, and
`family_run_agent`, including their permission edges.

**Roughly 30 `server.js` routes.** Chat, chat stream, vision, Gmail send and
draft, push subscribe and send, sheets read and write, Alexa, deploy, memory
bank. The smoke test hits `/api/health`, the approve page, and one 401. The
outward-sending ones matter most: a broken Gmail send is an email Dave thinks
went out.

**Scheduler delivery.** `test/triggers.test.js` covers cron shapes, catch up,
locks, and Stripe signatures. It does not cover `_deliver` writing the log
entry and sending the email, or what happens when the runner dies mid run on a
schedule.

**The approve page sign in flow.** The smoke test checks the HTML is served
and carries no secret. `test/qa-exposure.test.js` checks the page is reachable
while the repo is not. Nothing exercises signing in, the approve, reject and
redirect calls from the page, or the token surviving a reload. Covered by hand
on the manual checklist for now, which is a person, not a guarantee.

**Instruction files for the restructured seats (2026-09-21).** The roster
now names tony-soprano (basecode), tom-hagen, christopher-moltisanti,
silvio-dante, paulie-gualtieri, and paulie-cicero (vyzn) with files the runner
expects at `family/agents/<id>.md`. None of those six files exist yet:
Tony's and Paulie Cicero's rewrites are drafted and with Dave, the others were
never written, and frank-lucas has none by decision. What the code does about
it: `runner.start` refuses with the file named before any run record exists,
a hook wake to tom-hagen logs `event_skipped` with that message, and the
runner tests exercise the seats with stub files in a temp folder
(`FAMILY_AGENTS_DIR`, tests only). What the gate cannot see: a run of Tony
with the wrong brief once his file lands, and `michael-corleone.md` still
naming Al Neri and Rocco as his crew (lines 6, 7, and 49). Both are Dave's
files to change; nothing here rewrites them. The superseded files are under
`family/agents/retired`.

**The seat lock's two unverifiable edges.** `family/oauth.js` binds a seat
from the OAuth `resource` parameter when the client sends one for
`/mcp/<seat>`, and from a picker on the code page otherwise. Whether claude.ai
sends the parameter is not visible from the repo or the tests; the picker
covers both cases, so a wrong pick is the only way to bind the wrong seat.
The second edge is outside the backend entirely: which seat connectors a
given chat has enabled. Two enabled in one chat means that chat can act as
both seats. Every write logs the token's seat (`oauth:<seat>`), so it shows in
the log; it is not prevented.

**Claude Code environment egress for the engineers' keys.** The plan gives
christopher-moltisanti, silvio-dante, and paulie-gualtieri bearer keys through
a committed `.mcp.json` in each repo. From this sandbox, requests to
railway.app hosts were blocked by the environment's network policy earlier in
the session. Until one repo proves the call goes through, the engineers have
no tested path to `family_log_step` after the cutover flips unseated OAuth to
read only.

## Low

**The retired app's hardcoded VAPID key.** `index.html` (the March single
file app on Netlify, not what Dave opens) carries a VAPID public key as a
literal at line 35802. It is the OLD key, stale against the pair set in
Railway on 2026-09-20, so if anyone opened that app and tapped its push
toggle, `subscribe()` would fail with an applicationServerKey error. Logged,
not fixed: the codebase is retired and deferred by Dave (Clemenza,
2026-09-20). The live app fetches the key at runtime for exactly this reason.


**The contact rule and the protected topic.** "No agent contacts any of the
protected people" and "nothing about the protected topic is ever sent" exist
only as prose in the agent instruction files, where the people are named. They
are deliberately NOT greps in this repo, and as of 2026-09-20 they are not
named anywhere under `qa/` either: this folder is published to a public
artifacts repo, so a name here would be the leak the rule exists to prevent.
The publisher checks outgoing text against a local, gitignored deny list
(`qa/publish-deny.txt`) that Dave holds. The rule itself stays a human check on
the manual checklist, which is the honest place for it.

**`family/keys.js`, `family/login.js`, `family/ids.js`.**
Small, and reached through their importers, which are covered. `roster.js`
has its own test since the 2026-09-21 restructure (`test/roster.test.js`).

## Rule debt burn-down

The em dash baseline, which `qa:check` enforces as a ratchet with teeth: a
file this change touched must be ZERO whether or not it is baselined, an
untouched baselined file must match its count exactly, and everything else
must be zero. Touch a grandfathered file and you clean it.

| Date | Files | Em dashes | What moved |
|---|---|---|---|
| 2026-09-19 | 4 | 52 | Baseline created: `server.js` 24, `index.js` 18, `sw.js` 8, `token-persistence.js` 2 |
| 2026-09-19 | 2 | 32 | `index.js` and `token-persistence.js` deleted as dead code, taking 20 with them |
| 2026-09-20 | 2 | 32 | Ratchet teeth added. No change to the count: neither remaining file was touched |
| 2026-09-20 | 1 | 8 | `server.js` touched for the VAPID fix, so the ratchet made it clean all 24: text only, log strings, comments and prompt copy |

Remaining, and what it would take:

| File | Count | To reach zero |
|---|---|---|
| `sw.js` | 8 | The live Netlify service worker. Cleaning it means a redeploy of the old app, so it waits for a reason to redeploy. |

The number may never increase. It went 52, then 32, then 8, and the next
move is downward or nothing.

## Suggested order

By blast radius, not by ease:

1. Google token refresh, because it fails silently.
2. The spend cap, because it costs money.
3. The emailed code login, because it locks Dave out of his own approvals.
4. The outward-sending `server.js` routes, starting with Gmail send and push.

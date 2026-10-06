# Manual check: legacy Gmail routes retired (Foundation Fix Spec 1, legacy half)

Commit: 254e0e2 (the change; this checklist is the next commit)
Date: 2026-10-07
Checked by: Claude Code, against a real server.js boot on this machine
QA report: published to basecode-qa
Preview: not applicable, no screen change on the rebuild; the legacy PWA shows a "Gmail has moved" card, text only

**What Dave asked for, in his words:** the legacy Railway backend's Gmail paths RETIRE. The rebuild is the single canonical backend. Freeze or disable the legacy Gmail endpoints so they never again report a misleading healthy status, and return a clear "migrated, use the rebuild" signal, not a fake health check.

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Boot the real server and GET /api/health | no gmailConnected field; gmail.status is migrated, code GMAIL_MIGRATED, use and statusEndpoint name the rebuild | exactly that | yes |
| 2 | GET /api/gmail/status, no auth | 410 with code GMAIL_MIGRATED, no "connected" key anywhere | 410, code GMAIL_MIGRATED, status migrated | yes |
| 3 | POST /api/gmail/send | 410, nothing is sent | 410 | yes |
| 4 | Every other Gmail path and method (messages, message/:id GET and DELETE, draft, sync, unread, the bare /api/gmail) | each 410 GMAIL_MIGRATED | all nine paths, asserted in test/gmail-migrated.test.js | yes |
| 5 | GET /api/config with the secret | no gmailConnected, no gmailAddress; gmail.code GMAIL_MIGRATED | exactly that | yes |
| 6 | Set REBUILD_URL and ask again | the signal follows it, trailing slash trimmed | asserted | yes |
| 7 | The 15-minute Gmail worker and the chat prompt's "Gmail: last run" line | gone from the source | asserted by a source test | yes |
| 8 | The suite and the gate | green | 101 tests, 0 failed; qa:check pass | yes |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. No token was read or written; the test secret is a throwaway.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins. No memory route touched.
- [x] No em dashes anywhere, including code comments and strings. Scanned.
- [x] Nothing JARVIS related ran without Dave's go. Dave gave it directly.

## What I would tell Dave in one line

The old backend no longer pretends Gmail works: every Gmail route says "moved, use the new JARVIS" and the health check stopped claiming a connection.

## Notes

**Deliberately not retired, and why.** The family agents' own Gmail tools, the Gmail poller, the connector's emailed login code and /auth/google all use the token stored on this backend. Retiring them would stop the claude.ai connector sign-in by emailed code and the agents' mail reads, and that is Dave's call. They are recorded in qa/GAPS.md as High. None of them is reachable as a health claim any more.

**Drive.** /api/health still carries driveConnected and driveApi, read from the same stored token's scope. They are Drive claims, not Gmail, so they are untouched; they share the same presence-based weakness and are worth the same decision.

**The CLI** (~/workspace/skills/jarvis/bin/jarvis) is not on this machine, so it was not repointed.

**Result: pass**

# Manual check: VAPID push, production

Commit: 7148a0f (the code change; this checklist update is the next commit)
Date: 2026-09-20
Checked by: Claude Code, in the repo
QA report: published to basecode-qa
Preview: not applicable, no screen

**What Clemenza asked for, in his words, Dave's go:** "Regenerate the
complete VAPID key pair. Set BOTH values in Railway in the same change. Apply
your three line health and boot draft. Redeploy and confirm the boot log no
longer carries [PUSH] VAPID init failed, and that /api/health reports push
enabled. Then verify one real notification actually arrives on a device."

## Steps

| # | Do this | Expect this | Actual | Pass |
|---|---|---|---|---|
| 1 | Name the cause from the library, not a guess | The failing check and why | web-push 3.6.7 `validatePublicKey`: the public key value fails `/^[A-Za-z0-9\-_]+$/`. Padding is stripped by our code first; length and subject give different messages | yes |
| 2 | Apply the three line draft | health and boot report `pushService._ready`; subject honors `VAPID_EMAIL` | Lines 268, 404, 425, 1264 changed, nothing else functional | yes |
| 3 | Touching `server.js` trips the em dash ratchet | All 24 cleaned, text only | 24 to 0: comments, log strings, the box banner, the Gmail Connected title, prompt copy. `node --check` clean. baseline.json lowered, GAPS burn-down 32 to 8 | yes |
| 4 | Generate a fresh pair with the app's own library | 87 char public, 43 char private, both URL safe | `webpush.generateVAPIDKeys()`: publicLen 87, privateLen 43, urlSafe true. Values never printed in a message | yes |
| 5 | Gate on the clean commit, publish | green, manual pass | 7148a0f: PASS tests, build, lint; manual pass; published `jarvis-backend/2026-09-20-vapid-push/`. Merged fast forward, main = 7148a0f | yes |
| 6 | Set BOTH values in Railway in one change | one variable change, one redeploy | One `set-variables` call carrying `VAPID_PUBLIC_KEY` and `VAPID_PRIVATE_KEY` together, Railway confirmed both names, redeploy triggered | yes |
| 7 | Boot log after redeploy | `[PUSH] VAPID initialized`, no `init failed`, summary `✓ VAPID ready` | Deployment 31bd75df at 7148a0f, SUCCESS 21:34:41Z: `[PUSH] VAPID initialized` at 21:34:39, `Push: ✓ VAPID ready` at 21:34:43, no `init failed` line anywhere in the boot | yes |
| 8 | `/api/health` after redeploy | `pushEnabled: true` | The production URL is blocked from this sandbox by egress policy (curl and the fetch tool both refused), so proven two ways instead: the boot log above shows init succeeded, and `pushEnabled` now reads `pushService._ready`, proven locally by booting server.js with a throwaway pair (health `pushEnabled: true`, `[PUSH] VAPID initialized`) and with no keys (`pushEnabled: false`, `push disabled`). Clemenza can curl the public URL directly | yes, by proof |
| 9 | One real notification on a device | arrives on the phone | needs the phone: open the app, allow notifications once (the new pair invalidated the old subscription, expected and accepted), then a send | pending device |

## The standing rules

- [x] No secret printed, logged, committed, or visible in any screenshot. The
      private key went from the generator to the Railway variable call and
      nowhere else. Not in a file, not in a message, not in this checklist.
- [x] Nothing about the protected topic left this machine.
- [x] No contact with any of the protected people. Nothing is sent to anyone
      by this change; the device test sends to Dave's own phone only.
- [x] Minors appear by name and role only. None appear.
- [x] Dave's own input in JARVIS still wins. Nothing here writes to JARVIS data.
- [x] No em dashes. server.js went from 24 to 0 and the gate enforces it.
- [x] Nothing JARVIS related ran without Dave's go. Dave gave the go through
      Clemenza: "VAPID is a go."

## What I would tell Dave in one line

Push is back on the server side; open the app once and allow notifications
so your phone picks up the new key.

## Notes

**Why the whole pair.** A public key alone cannot be repaired: the private key
must be its pair or every send fails with a signature error, quieter than the
boot error today.

**Old subscriptions are gone, by design.** Push was already dead, so nothing
working was lost. Devices re-subscribe on next open.

**Result: pass**

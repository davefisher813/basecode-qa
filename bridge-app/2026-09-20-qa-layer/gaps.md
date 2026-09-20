# Gaps

What the gate does NOT protect. A green `qa:check` means tests, types, build
and the house rules passed on that commit; this file is what it cannot see.

Severity is about what breaks for Dave, or for a Bridge family, if the code is
wrong, not about how hard the test would be to write.

| Severity | What it means |
|---|---|
| High | It fails silently, or someone outside the org sees data they must not. |
| Medium | It fails loudly and locally, and someone can tell what happened. |
| Low | Contained, or already guarded from another direction. |

## High

**Row level security, end to end.** The multi-tenant boundary is RLS keyed
off `org_members`. `scripts/run_rls_test.sh` proves it as a real non
superuser against a real Postgres, and it caught an infinite policy
recursion the schema tests missed. The gate does NOT run it: it needs a
Postgres on the machine. Every migration change must run it by hand and say
so on the checklist. The failure mode is one org reading another's athletes.

**Anything that needs Supabase at runtime.** There is no boot stage. The app
cannot serve a page without a Supabase project, and booting against a fake
would test the fake. Auth, the org switch, every server action that writes:
none of it runs in the gate. `scripts/live/drive.mjs` after a
`FIXTURE_MODE=1` build is the check that counts for what Dave sees on his
phone, and it is run by hand.

## Medium

**The preview, the bench and the audit.** `scripts/build_previews.sh`
renders every page in `src/testing/pages.ts`, bundles the real fit and Doc
AI modules into a bench, and audits computed styles in both themes. The gate
does not run it: it needs Chromium, Python and esbuild. `preview.applicable`
in the report turns true when a screen or stylesheet is touched, and the
shots are owed under `qa/previews/<task>/` from that pipeline, by hand.

**Doc AI against a real model.** `src/lib/docai/` is tested with injected
callers. Nothing in the gate sends a document to a model. Extraction quality
is a human check.

**Build time environment.** `next build` passes today with no env at all
because nothing reads env at build time. The day something does, the build
stage fails for want of a value it should never have, and this note is where
to look first.

## Low

**Title Case, the kit, em dashes in `src/`.** All enforced by `src/laws/`,
which the tests stage runs. The gate's own em dash rule covers the rest of
the repo (docs, scripts, sql) that the law does not walk.

## Rule debt burn-down

| Date | Files | Em dashes | What moved |
|---|---|---|---|
| 2026-09-20 | 1 | 2 | Baseline created: `src/laws/laws.test.ts` 2, the law's own comment and search string |

The number may never increase.

## Suggested order

1. RLS, because it is the tenant boundary and it fails silently.
2. A runtime smoke against a throwaway Supabase project, because everything
   a user touches goes through it.
3. Wiring `build_previews.sh` into the gate on a machine that has the tools.

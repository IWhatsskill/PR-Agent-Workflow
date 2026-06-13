# PR Body Template

Use this file as the default PR body structure unless the operator or repository provides a stricter template.
Fill the sections with real answers before publishing. Remove empty placeholders, but keep the section order.

---

## Summary

- Problem:
- Solution:
- What changed:
- What did NOT change (scope boundary):

## Motivation

-

## Change Type

- [ ] Bug fix
- [ ] Feature
- [ ] Refactor required for the fix
- [ ] Docs
- [ ] Security hardening
- [ ] Chore/infra

## Scope

- [ ] Runtime / orchestration
- [ ] Tools / execution
- [ ] Auth / permissions
- [ ] Data / storage
- [ ] Integrations
- [ ] API / contracts
- [ ] UI / UX
- [ ] CI/CD / infra

## Linked Issue/PR

- Related #
- [ ] This PR fixes a bug or regression

## Real Behavior Proof

- Behavior or issue addressed:
- Real environment tested:
- Exact steps or command run after this patch:

```bash
```

- Evidence after fix:

```text
```

- Observed result after fix:
- What was not tested:
- Before evidence:

```text
```

## Root Cause

- Root cause:
- Missing detection / guardrail:
- Contributing context (if known):

## Regression Test Plan

- Coverage level that should have caught this:
  - [ ] Unit test
  - [ ] Integration test
  - [ ] End-to-end test
  - [ ] Existing coverage already sufficient
- Target test or file:
- Scenario the test should lock in:
- Why this is the smallest reliable guardrail:
- Existing test that already covers this (if any):
- If no new test is added, why not:

## User-visible / Behavior Changes

-

## Security Impact

- New permissions/capabilities?
- Secrets/tokens handling changed?
- New/changed network calls?
- Command/tool execution surface changed?
- Data access scope changed?
- If any `Yes`, explain risk + mitigation:

## Repro + Verification

### Environment

- OS:
- Runtime/container:
- Provider/integration (if any):
- Relevant config (redacted):

### Steps

1.

### Expected

-

### Actual

-

## Evidence

- [ ] Failing test/log before + passing after
- [ ] Trace/log snippets
- [ ] Screenshot/recording
- [ ] Perf numbers (if relevant)

```text
```

## Human Verification

- Verified scenarios:
- Edge cases checked:
- What you did **not** verify:

## Review Conversations

- [ ] I replied to or resolved every bot review conversation I addressed in this PR.
- [ ] I left unresolved only the conversations that still need reviewer or maintainer judgment.

## Compatibility / Migration

- Backward compatible?
- Config/env changes?
- Migration needed?
- If yes, exact upgrade steps:

## Risks and Mitigations

- Risk:
  - Mitigation:

---

## Publishing Rules

- Keep the section order unless the operator or repository provides a stricter template.
- Do not claim "all tests pass" unless the full suite actually passed on the patched branch.
- If baseline tests fail, state patched and baseline results honestly in `Evidence` or `Human Verification`.
- Dependency changes must be called out in `Security Impact`, `Compatibility / Migration`, and `Risks and Mitigations`.
- No secrets, tokens, credentials, private paths, private endpoints, emails, phone numbers, or private IDs.
- Remove empty placeholders and helper-only text before publishing.
- Run a leak/template check on the final body before any tracker/GitHub/GitLab write.

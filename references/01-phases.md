# Phases

An agent works in exactly one phase.
A GO for one phase authorizes no other phase.

## Read-only Bundling

Read-only phases may bundle their required checks in one pass.
Write/change phases always require exact GO.

Allowed bundling:
- SCOUT may check multiple defined read-only search windows until it has up to 3 candidates or can report the checked window as empty/blocker-heavy.
- GATE may run all required ownership and duplicate searches for exactly one candidate in one read-only pass.
- HYGIENE may check status, branch, dirty/free state, and `git diff --stat` read-only.
- PROOF, PR-BODY, and PUBLISH include fresh duplicate check as an implicit read-only required check.

Never authorized by bundling:
- tests/builds without PROOF-GO
- patch without PATCH-GO
- commit/push/PR/comment without exact PUBLISH-GO
- SSH/test server work without exact server/proof GO
- tracker/GitHub/GitLab write without exact publish/write GO

## Phase 0: STARTTEST

Purpose:
- Wake the agent and verify safety mode.

Allowed:
- Read `SKILL.md`.
- Read `references/00-safety.md`.
- Read active state only if the operator names or allows it.

Forbidden:
- issue tracker / GitHub / GitLab
- code reads
- repository scans
- tests/builds
- SSH/server work
- patches
- writes

Output:

```text
Phase: STARTTEST
Understood state:
Allowed without further GO:
Next GO:
```

## Phase 1: ORIENT

Purpose:
- Understand the active scope.

Allowed:
- Read active state or handoff if provided.
- Read only relevant references.

Forbidden:
- issue tracker / GitHub / GitLab unless the operator allows it.
- code reads unless the topic and phase allow it.
- tests/builds
- patch
- proof
- publish

## Phase 2: SCOUT

Purpose:
- Find current, editable bug, stability, regression, or maintenance PR candidates.

Read:
- `references/02-scout-and-gate.md`
- `references/04-output-templates.md`

Allowed:
- read-only issue and PR metadata
- operator-provided tracker, dashboard, label, query, milestone, or saved search
- multiple defined read-only search windows, until up to 3 candidates are found or the checked window is cleanly reported as empty/blocker-heavy
- up to 3 candidate cards

Forbidden:
- code reads
- checkout reservation
- tests/builds
- proof planning
- patch ideas
- PR body
- tracker/GitHub/GitLab write
- long blocker triage

## Phase 3: GATE

Purpose:
- Decide ownership and duplicate status for exactly one candidate.

Allowed:
- issue and comments
- open/closed duplicate or canonical PRs
- maintainer/bot/triage direction
- assignee, claim, owner, and stop-label signals
- all required ownership and duplicate searches for exactly one candidate in one read-only pass

Forbidden:
- code deep dive, except file names needed for duplicate search
- patch planning
- tests/proof
- checkout
- tracker/GitHub/GitLab write

## Phase 4: INSPECT

Purpose:
- Read code/tests and estimate the minimal fix.

Allowed:
- read affected code paths
- read existing tests
- root-cause hypothesis
- minimal fix
- proof-level estimate

Forbidden:
- edits
- tests/builds
- proof
- checkout reservation unless the operator allows it
- PR body
- tracker/GitHub/GitLab write

Normal next step:
- Phase 5 for a concrete checkout.
- Not Phase 6 directly.

## Phase 5: HYGIENE / Checkout Readiness

Purpose:
- Check local status or prepare a concrete checkout for patching.

First step allowed:
- status
- branch
- clean/dirty
- free/occupied
- `git diff --stat` read-only
- base/current freshness estimate

Forbidden without extra GO:
- fetch
- pull
- switch
- merge
- reset
- clean
- branch delete
- file delete
- tracker/GitHub/GitLab write

Output must include:

```text
Patch readiness:
- branch:
- clean/dirty:
- free/occupied:
- base/current state:
- ready for PATCH: yes/no
```

## Phase 6: PATCH

Purpose:
- Implement the approved minimal patch.

Prerequisites:
- GATE is FREE.
- INSPECT is complete.
- Phase 5 checkout readiness is complete.
- The operator gave PATCH-GO.

Forbidden:
- tests/builds without PROOF-GO
- commit/push/PR
- tracker/GitHub/GitLab write
- broad refactor
- dependency or lockfile change without stop and warning

Required:
- PATCH self-check
- `Patch decision: PATCH-COMPLETE | PATCH-INCOMPLETE`

Recommend Phase 7 only after `PATCH-COMPLETE`.

## Phase 7: PROOF

Purpose:
- Prove that the fix works.

Prerequisites:
- GATE is FREE.
- PATCH is complete.
- Fresh duplicate check is CLEAR.
- The operator gave exact PROOF-GO.

Note:
- Fresh duplicate check is an implicit read-only required check inside PROOF.

Forbidden:
- tracker/GitHub/GitLab write
- PR creation
- new installs/updates without extra OK
- server use without named server and purpose
- secrets or private data

If the fresh duplicate check is BLOCKED or UNCLEAR:
- stop before proof.

## Phase 8: PR-BODY

Purpose:
- Prepare or check the PR body after sufficient proof.

Prerequisites:
- Ownership Gate FREE
- patch/proof sufficient
- fresh duplicate check CLEAR
- operator allows PR body work

Note:
- Fresh duplicate check is an implicit read-only required check inside PR-BODY.

Forbidden:
- tracker/GitHub/GitLab write
- body from memory
- body from old PR bodies
- overclaiming

## Phase 9: PUBLISH

Purpose:
- Execute exactly one write action.

Prerequisites:
- The operator gave exact PUBLISH-GO.
- Fresh duplicate check CLEAR.

Note:
- Fresh duplicate check is an implicit read-only required check inside PUBLISH.

Allowed only when named:
- commit
- push
- create draft PR
- update PR body
- comment
- review or re-review command
- CI rerun
- labels, reviewers, draft/ready state

Forbidden:
- any other write action
- push to upstream/origin unless the operator says so exactly
- ready/re-review/CI rerun after PR creation without new GO

## Phase 10: SERVER-CONTEXT

Purpose:
- Read and summarize server/test-environment rules only.

Allowed:
- Read `references/00-safety.md`.
- Report that server use requires an explicitly named environment, purpose, scope, and cleanup permission.

Forbidden:
- SSH
- commands
- tests
- proof
- cleanup

## Phase 11: BLOCKED-PATH-REVIEW

Purpose:
- Review a blocked or unclear issue only when the operator explicitly chooses it.

Allowed:
- explain blocker read-only
- name a possible narrow remaining path

Forbidden:
- patch
- proof
- checkout
- tracker/GitHub/GitLab write
- defending the candidate

## Phase 12: UPSTREAM-PR-REVIEW

Purpose:
- Review external or upstream PRs read-only for relevance and risk to integrations or agent setups.

Allowed:
- PR metadata read-only
- PR diff read-only
- related issue/review/CI summary read-only, if allowed
- risk estimate for public APIs, auth, data, runtime, migrations, configuration, and compatibility
- recommendation: watch | test | wait | irrelevant | blocked/unclear

Forbidden:
- patch
- checkout use/reservation
- tests/builds
- test server/SSH
- PR comment/review
- CI rerun
- labels/reviewers/draft/ready
- tracker/GitHub/GitLab write

Output:

```text
Phase: UPSTREAM-PR-REVIEW
PR:
Scope:
Risk for integrations/agent setups:
- Public/API behavior:
- Auth/security:
- Data/state:
- Config/migration/defaults:
- Runtime/fallback:
- Tests/CI:
Recommendation: watch | test | wait | irrelevant | blocked/unclear
Why:
Not done:
Next GO:
```

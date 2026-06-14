# Phases

An agent works in exactly one phase.
A GO for one phase authorizes no other phase.

## Current Proof And Publish Guardrails

These rules override any shorter examples later in this file.
Read this section as global guardrails, not as the current phase. After reading it, continue with the requested Phase 0-14 section.

Fresh duplicate check minimum before PROOF, PR-BODY, and every PUBLISH action:
- issue number
- issue title and reported symptom
- open PRs for the issue
- new `Fixes #...` / `Closes #...`
- affected files and functions, when known
- relevant closed, duplicate, superseding, or canonical PRs
- new maintainer, reviewer, bot, or triage bot comments
- stop labels, ownership claims, or assignee changes

Phase 7 PROOF must do both:
- Run relevant project tooling when present and feasible: package manager/script inspection, static checks, typecheck, lint, and focused tests selected for the changed files.
- Prove the reported real behavior with Before/After evidence. Static checks and tests never replace real behavior proof.

Runtime, package-manager, build, and test proof should use the operator-approved project proof environment for that run. Do not spend time probing unrelated local machine tooling unless local proof is explicitly allowed. If no suitable test-environment GO exists, stop and ask for the exact PROOF-GO.

Artifact hygiene:
- Name artifacts clearly: `before-*`, `after-*`, `test-*`, `log-*`, or `timeline-*`.
- Check screenshots, videos, and logs for IPs, URLs, local paths, setup screens, login screens, tokens, and private data before PR use.
- Redact or trim artifacts when needed.
- Local artifact paths are not public PR evidence.
- A PR body may claim visual artifacts only with real `github.com/user-attachments/assets/...` URLs or other public artifact links.
- If upload is needed, the operator uploads the artifact and the agent inserts the final URLs afterward.

Phase 8 PR-BODY:
- Do not dump the complete body into chat unless asked.
- Report body status and changed sections.
- Write `Remaining proof gap: None for the reported behavior.` only when no gap remains.

Phase 9 PUBLISH:
- Commit messages follow repository convention. If unclear, read recent history and propose the message. Fallback: `<area>: <short fix>` plus `Fixes #<issue>`.
- Push fork branches with `git push fork HEAD:<branch>`; do not use `git push -u ...` unless local tracking is explicitly requested.
- After a successful push, stop and recommend `PUBLISH-GO: create draft PR`. Do not create the PR automatically.
- `PUBLISH-CHECK-GO for PR #<PR>` is read-only and should follow PR creation, PR body updates, pushes to existing PRs, and bot/CI signals.
- PUBLISH-CHECK checks draft/ready status, head SHA/current commit, PR status, checks/CI, failed-job logs, bot comments, review threads, PR-body artifacts/URLs, and mergeability when available.
- PUBLISH-CHECK must not rerun CI, comment, label, ready/draft, push, update the body, resolve, or reply.

If a reviewer, maintainer, bot, or CI requests changes:
- PR-body/formality only: exact `PUBLISH-GO: update PR body #<PR>`.
- Code/test problem: `INSPECT -> HYGIENE -> PATCH -> PROOF -> PUBLISH -> PUBLISH-CHECK`.
- Re-review only: exact `PUBLISH-GO: post re-review comment #<PR>`.

Rollback/abort is not automatic. Use exact `PATCH-ABORT-GO` or `ROLLBACK-GO`; show status/diff first, touch only the agent's own local uncommitted changes, and never use `git reset --hard` or `git clean` without exact GO.


## Bundling And Checkout Readiness

Read-only checks may be bundled in one pass. HYGIENE checkout readiness is the explicit local-git freshness exception for a named clean/free checkout before PATCH.
Write/change phases always require exact GO.

Allowed bundling:
- SCOUT may check multiple defined read-only search windows until it has up to 3 candidates or can report the checked window as empty/blocker-heavy.
- GATE may run all required ownership and duplicate searches for exactly one candidate in one read-only pass.
- HYGIENE may check status, branch, dirty/free state, and `git diff --stat`; when a concrete clean/free checkout is named before PATCH, it may update `main` with `git fetch origin`, `git switch main`, and `git merge --ff-only origin/main`.
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
- Make the exact local checkout clean, current, and ready for patching.

Allowed for the named checkout:
- status
- branch
- clean/dirty
- free/occupied
- `git diff --stat`
- `git fetch origin`
- `git switch main`
- `git merge --ff-only origin/main`
- final branch and distance check against `origin/main`

Stop and report if:
- checkout is dirty, occupied, or unclear
- `git switch main` fails
- `git merge --ff-only origin/main` fails
- `main` is still not current after the fast-forward attempt

Forbidden in HYGIENE:
- `git pull`
- non-fast-forward merge
- rebase
- reset
- clean
- branch delete
- file delete
- patching
- tests/builds
- tracker/GitHub/GitLab write

Output must include:

```text
Patch readiness:
- branch:
- clean/dirty:
- free/occupied:
- fetch origin:
- switch main:
- ff-only main<-origin/main:
- current commit:
- distance main...origin/main:
- main current:
- ready for PATCH: yes/no
```

## Phase 6: PATCH

Purpose:
- Implement the approved minimal patch.

Prerequisites:
- GATE is FREE.
- INSPECT is complete.
- Phase 5 says the checkout is clean, free, and current on `main`.
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
- Prove the reported real behavior before and after the fix.

Prerequisites:
- GATE is FREE.
- PATCH is complete.
- Fresh duplicate check is CLEAR.
- The operator gave exact PROOF-GO.

Note:
- Fresh duplicate check is an implicit read-only required check inside PROOF.

Required:
- Real behavior proof for the reported issue.
- Before evidence and After evidence.
- `Real environment tested` names the actual affected runtime or layer, not just "unit test".
- Exact steps or command run after this patch.
- Observed result after the fix.
- What was not tested.
- Remaining proof gap, if any.
- Before/after screenshots or videos for UI/visual changes when possible; add them for other bug types when useful.

Unit or mock tests count only when they directly reproduce the reported flow before/after and no closer real path is allowed or available in that phase.
Tests alone are not publish proof unless they contain a real before/after repro of the reported behavior or the operator explicitly accepts the remaining proof gap.

Test environment and disposable run directory rules:
- Server or filesystem proof may use only the environment explicitly named by the operator for that run.
- If proof creates files on a server or shared filesystem, the operator must name or approve a disposable run root before work starts.
- Use one fresh unique directory under that root: `<approved-run-root>/<date>-<issue-or-pr>-<short-slug>`.
- Never work directly in the run root or its parent directory.
- Never reuse an existing run directory unless the operator explicitly names that exact directory.
- Before work, report/check current working directory and target directory.
- If the path is wrong or unclear: STOP.
- Cleanup is never part of PROOF; recommend Phase 12 / CLEANUP-GO later.
Artifact guidance:
- UI/visual: screenshot or video.
- Runtime/queue/transport/channel: before/after timeline, logs, trace, real run output, or reproducible test run; add screenshots/video when useful.
- CLI/config/parser: before/after command output or focused test.
- Docs-only: render, link, or lint proof; screenshot only when relevant.

If real behavior proof or visual media is not possible:
- mark proof as incomplete
- explain the proof gap
- provide the strongest replacement evidence
- name the missing environment or next GO

Forbidden:
- tracker/GitHub/GitLab write
- PR creation
- new installs/updates without extra OK
- server use without named environment, purpose, scope, and disposable run root
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
- PUBLISH-CHECK-GO for PR #<PR> as read-only post-publish check


After a successful push PUBLISH-GO (`push to fork branch <branch>` or `push branch <branch> to <remote>`):
- stop
- do not create a PR automatically
- recommend the next GO: `PUBLISH-GO: create draft PR`
- PR creation is always draft unless the operator explicitly says `ready` / `non-draft`.

`PUBLISH-CHECK-GO for PR #<PR>`:
- read-only post-publish check inside Phase 9
- allowed: PR status, checks/CI status, CI logs, bot comments, and review threads read-only
- forbidden: rerun, comment, label, ready, push, PR body update, resolve/reply
- only recommend the next exact GO; do not react automatically

After a successful `PUBLISH-GO: update PR body #<PR>`:
- stop
- do not rerun CI, comment, mark ready, label, review, or re-review automatically
- optionally recommend the next GO: `PUBLISH-CHECK-GO for PR #<PR>`
Forbidden:
- any other write action
- push to upstream/origin unless the operator says so exactly
- ready/re-review/CI rerun after PR creation without new GO

## Phase 10: SERVER-CONTEXT

Purpose:
- Read and summarize server/test-environment rules only.

Allowed:
- Read `references/00-safety.md`.
- Report that server use requires an explicitly named environment, purpose, scope, approved disposable run root, and separate CLEANUP-GO.

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

## Phase 12: CLEANUP

Purpose:
- Remove exactly one approved disposable run directory after the operator has reviewed proof artifacts.

Prerequisites:
- The operator gave exact CLEANUP-GO.
- The target directory is explicitly named.
- The target belongs to a completed proof or PR context.
- The target is inside the operator-approved disposable run root.

Allowed:
- inspect only the exact named disposable run directory
- show current working directory
- show size, for example `du -sh <target>` on Unix-like systems
- resolve the path, for example `realpath <target>` when available
- delete only that one directory after path checks pass
- confirm it is gone

Forbidden:
- cleanup without exact CLEANUP-GO
- wildcard paths
- deleting the disposable run root itself
- deleting the parent of the run root
- deleting outside the approved run root
- deleting multiple directories without separate approval

Output:

```text
Phase: CLEANUP
Environment:
Issue/PR:
Target directory:
Current working directory:
Resolved path:
Size:
Path checks:
Deleted: yes/no
Post-check:
Not done:
Next GO:
```
## Phase 13: HANDOFF / STATE

Purpose:
- Update approved local task state/current state/handoff files after PR work.
- Make resume after compaction or a new agent safe without re-reading old archives.

Allowed:
- Update only operator-approved local state or handoff files.
- Write a short handoff summary.
- Mark the next safe step.

Required fields:
- Issue:
- PR:
- Checkout:
- Branch:
- Latest commit:
- Work done:
- Proof:
- Publish:
- Cleanup:
- Open items:
- Next safe step:

Forbidden:
- Code changes.
- Tests or builds.
- SSH, server, device, or testserver work.
- Tracker/GitHub/GitLab write.
- Commit, push, PR creation, labels, comments, reruns, ready/draft changes.
- Reconstructing old archives or adding long history to state files.

Stop if:
- The requested state target is unclear.
- The handoff would require reading archives or old proof notes that were not explicitly named.

Output:
```text
Phase: HANDOFF
Issue:
PR:
Checkout:
Branch:
Latest commit:
Work done:
Proof:
Publish:
Cleanup:
Open items:
Next safe step:
Not done:
```
## Phase 14: UPSTREAM-PR-REVIEW

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





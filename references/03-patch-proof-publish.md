# Patch Proof Publish

This file applies only after `Ownership Gate: FREE`.

Order:

```text
GATE FREE -> INSPECT -> HYGIENE -> PATCH -> PROOF -> PR-BODY -> PUBLISH
```

Every phase needs its own GO.

## Current Proof And Publish Guardrails

These rules override any shorter examples later in this file.

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


## INSPECT

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
- checkout reservation unless allowed
- PR body
- tracker/GitHub/GitLab write

Output:

```text
Phase: INSPECT
Candidate:
Root-cause hypothesis:
Affected paths:
Minimal fix:
Do not touch:
Risk:
Expected proof level:
Next GO:
Recommended:
- Phase 5 <Checkout>
Why:
- The concrete checkout must be readiness-ok before PATCH.
```

## HYGIENE Before PATCH

Before PATCH, the concrete checkout must be checked and brought to current `origin/main`.

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

## PATCH

Allowed:
- only the named minimal code/test/doc change
- existing patterns

Forbidden:
- tests/builds
- tracker/GitHub/GitLab write
- commit/push
- broad refactor
- dependency or lockfile change without stop and OK
- changelog/release notes unless the operator or maintainer requests it

Patch rules:
- keep it small
- be honest
- use the right layer
- do not patch the wrong path
- preserve intended existing behavior
- follow the project's language and style conventions

## PATCH Self-Check

Before recommending PROOF, compare the patch against:
- issue title/body
- relevant maintainer or triage direction
- INSPECT minimal fix and do-not-touch list
- actual diff

Required questions:
- Which issue claims are covered?
- Which maintainer/triage acceptance points are covered?
- Which variants, inputs, or edge cases does the issue name?
- Which intended existing behavior remains preserved?
- Is there a mirror/counter-direction that must not break?
- What is not covered? Why is it correctly out of scope?

Decision:

```text
Patch decision: PATCH-COMPLETE | PATCH-INCOMPLETE
```

On PATCH-INCOMPLETE:
- do not recommend PROOF
- no PR body
- no commit/push/PR
- refine the patch or let the operator decide

PATCH output:

```text
Phase: PATCH
Changed:
Why minimal:
Coverage check:
- Issue claims:
- Maintainer/triage points:
- Variants/edge cases:
- Existing behavior preserved:
- Not covered / intentionally out of scope:
Patch decision: PATCH-COMPLETE | PATCH-INCOMPLETE
Dependency/lockfile affected: yes/no
CODEOWNERS/Security affected: yes/no
Risks:
Next GO:
```

## Fresh Duplicate Check

Required before:
- PROOF
- PR-BODY
- every PUBLISH action

Check live/read-only:
- new open PRs for the issue
- new `Fixes #...` / `Closes #...`
- new maintainer or triage comments
- new stop labels or claims

The fresh duplicate check is allowed as an implicit read-only required check inside PROOF-GO, PR-BODY-GO, and PUBLISH-GO.
It authorizes no other action and no tracker/GitHub/GitLab write.

Output:

```text
Fresh duplicate check:
- issue:
- open PRs:
- Fixes/Closes:
- new maintainer/triage direction:
- stop labels/claims:
Decision: CLEAR | BLOCKED | UNCLEAR
```

On BLOCKED or UNCLEAR:
- stop before proof/body/publish
- no proof
- no PR body
- no commit/push/PR
- no tracker/GitHub/GitLab write

## PROOF

Prerequisites:
- Ownership Gate FREE
- Patch decision PATCH-COMPLETE
- Fresh duplicate check CLEAR
- operator gave exact PROOF-GO

Allowed:
- only named tests/proofs
- test server only with named environment, purpose, scope, and approved disposable run root
- brief result summary
- real behavior proof for the reported issue
- before/after screenshots or videos when the change is visual, UI-facing, or otherwise useful to observe

Forbidden:
- installs/updates without extra OK
- SSH/server without explicit server
- secrets or real private accounts
- tracker/GitHub/GitLab write
- PR creation

Proof levels:
- `source only`: unit/integration test or source reproduction
- `diagnostic runtime`: real product path in disposable setup
- `live behavior`: real UI/API/channel/provider/runtime observation, redacted

Test environment and disposable run directory rules:
- Server or filesystem proof may use only the environment explicitly named by the operator for that run.
- If proof creates files on a server or shared filesystem, the operator must name or approve a disposable run root before work starts.
- Use one fresh unique directory under that root: `<approved-run-root>/<date>-<issue-or-pr>-<short-slug>`.
- Never work directly in the run root or its parent directory.
- Never reuse an existing run directory unless the operator explicitly names that exact directory.
- Before work, report/check current working directory and target directory.
- If the path is wrong or unclear: STOP.
- Cleanup is never part of PROOF; recommend Phase 12 / CLEANUP-GO later.
Real behavior proof:
- Always required for PR proof.
- Must prove the reported behavior before and after the patch.
- `Real environment tested` must name the actual affected runtime or layer, such as local product/runtime, plugin or handler integration, CLI command, browser UI, test server, live transport, Gateway/API path, or queue/runtime path.
- Unit or mock tests count only when they directly reproduce the reported flow before/after and no closer real path is allowed or available in that phase.
- Tests alone are not publish proof unless they contain a real before/after repro of the reported behavior or the operator explicitly accepts the remaining proof gap.
- UI/visual changes require before/after screenshot or video when possible.
- Runtime/queue/transport/channel bugs need a before/after timeline, logs, trace, real run output, or reproducible test run; add screenshots/video when useful.
- CLI/config/parser bugs need before/after command output or a focused test.
- Docs-only changes need render, link, or lint proof; screenshot only when relevant.
- If real behavior proof or visual media is not possible, mark proof as incomplete, state the proof gap, provide the strongest replacement evidence, and name the missing environment or next GO.

If proof cannot run:
- do not pretend it is done
- report missing tool/setup
- do not publish

## CLEANUP

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
## PR-BODY

Prerequisites:
- Ownership Gate FREE
- proof sufficient
- Fresh duplicate check CLEAR
- operator allows PR body work

Use `references/05-pr-body-template.md` unless the operator or repository provides a stricter template.
Do not reconstruct from memory, old PR bodies, bot comments, or a guessed upstream template.

## Project Risk Check

Use only for PR-BODY/PUBLISH or explicit PR review.
Do not use it as a SCOUT blocker.

Check whether the diff touches or risks:
- public/API behavior
- auth/security
- data/state
- persistence/migrations
- configuration/defaults
- runtime/fallback behavior
- dependency or build system
- plugin/extension boundaries
- affected tests
- rollback/compatibility

If a touched risk is open:
- stop before publish
- explain the risk in 1-2 lines
- ask the operator for the next GO

PR body rules:
- preserve project template sections
- remove helper text
- be honest about proof
- use `Closes #...` only when the exact issue is fully fixed
- otherwise use `Related #...`
- run leak check before publish

## PUBLISH

PUBLISH-GO must be exact.

Examples:

```text
PUBLISH-GO: commit only
PUBLISH-GO: push to fork branch <branch>
PUBLISH-GO: create draft PR
PUBLISH-GO: update PR body #123
PUBLISH-GO: post issue comment #123
PUBLISH-GO: rerun failed CI job #123
PUBLISH-CHECK-GO for PR #123
```

Allowed only when named:
- commit
- push
- PR creation
- PR body update
- comment
- review
- re-review/bot command
- CI rerun
- label/assignee/reviewer/draft/ready


After a successful `PUBLISH-GO: push to fork branch <branch>`:
- stop
- do not create a PR automatically
- recommend the next GO: `PUBLISH-GO: create draft PR`
- PR creation is always draft unless the operator explicitly says `ready` / `non-draft`.

Push command guidance:
- Use an explicit push target, for example `git push <remote> HEAD:<branch>`.
- Do not use `git push -u ...` unless the operator explicitly asks to set local tracking.

`PUBLISH-CHECK-GO for PR #<PR>`:
- read-only post-publish check inside Phase 9
- allowed: PR status, checks/CI status, CI logs, bot comments, and review threads read-only
- forbidden: rerun, comment, label, ready, push, PR body update, resolve/reply
- only recommend the next exact GO; do not react automatically

After a successful `PUBLISH-GO: update PR body #<PR>`:
- stop
- do not rerun CI, comment, mark ready, label, review, or re-review automatically
- optionally recommend the next GO: `PUBLISH-CHECK-GO for PR #<PR>`
Before publish:
- Fresh duplicate check CLEAR
- maintainer comments rechecked
- diff scope checked
- project risk check, if relevant
- dependency guard checked
- CODEOWNERS/Security checked
- PR body leak/template check
- tests/proof summarized honestly

After PR creation:
- read/report only
- no ready
- no re-review
- no CI rerun
- no labels/reviewers
- no external announcement

All of those need a new PUBLISH-GO.
## HANDOFF / STATE

After PUBLISH, PUBLISH-CHECK, BLOCKED, CLEANUP, or abort, report:

```text
State update needed: yes/no
```

Writing state is never automatic. It needs a separate `HANDOFF-GO` / `STATE-GO`.

Allowed in HANDOFF / STATE:
- update only operator-approved local state or handoff files
- write a short resume-safe summary
- record the next safe step

Forbidden in HANDOFF / STATE:
- code changes
- tests/builds
- SSH/server/testserver work
- tracker/GitHub/GitLab write
- commit, push, PR creation, comments, labels, reruns, ready/draft changes
- reconstructing old archives or long history



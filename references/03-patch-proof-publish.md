# Patch Proof Publish

This file applies only after `Ownership Gate: FREE`.

Order:

```text
GATE FREE -> INSPECT -> HYGIENE -> PATCH -> PROOF -> PR-BODY -> PUBLISH
```

Every phase needs its own GO.

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

Before PATCH, the concrete checkout must be checked.

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

If fetch/update is needed:
- ask the operator
- do not continue automatically

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
- test server only with named environment and purpose
- brief result summary

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

For UI/API/runtime/integration bugs:
- source tests alone may not be strong enough
- aim for before/after runtime proof when practical
- prefer screenshots/videos/log snippets when relevant and safe

If proof cannot run:
- do not pretend it is done
- report missing tool/setup
- do not publish

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

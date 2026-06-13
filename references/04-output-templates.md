# Output Templates

Use short fixed forms.
Do not write long justification essays.

## Read-only Bundling

- SCOUT, GATE, and HYGIENE may bundle their required checks read-only in one pass.
- Fresh duplicate check is an implicit read-only required check in PROOF, PR-BODY, and PUBLISH.
- Patch, proof execution, publish actions, SSH/test-server work, and tracker/GitHub/GitLab write still require exact GO.

## Minimal Default Output

Use when no stricter template is needed:

```text
Phase:
Result:
Not done:
Next GO:
Recommended:
- <copyable command or WAIT>
Why:
- <one sentence>
```

## Next GO

Always name exactly one copyable recommendation and then stop.
The recommendation is not permission.

```text
Next GO:
Recommended:
- <copyable command>

Why:
- <one sentence>

Alternatives:
- WAIT
```

## SCOUT

```text
Phase: SCOUT
Search window:
Checked:

Candidate 1:
Issue:
Freshness:
Why interesting:
Free signals:
Blocker check:
Status: LIKELY FREE | UNCLEAR

Drops:
- #...: DROP, <one-line reason>.

Next GO:
Recommended:
- Phase 3 <Issue>
Why:
- Candidate looks LIKELY FREE; the next clean step is GATE.
Alternatives:
- Phase 2 <next search window>
- WAIT
```

## GATE FREE

```text
Phase: GATE
Candidate:
Ownership Gate: FREE

Issue:
- status:
- freshness:
- labels:

Issue comments:
- relevant direction:

Open PR duplicate search:
- by issue number:
- by Fixes/Closes:
- by symptom:
- by error text:
- by files/functions:

Closed/canonical search:
- result:

Claim/assignee/maintainer direction:
- result:

Stop labels:
- result:

Decision:
- FREE because ...

Next only with Operator OK:
Recommended:
- Phase 4 <Issue>
Why:
- FREE is not PATCH-GO; the next step is INSPECT.
Alternatives:
- Phase 2 <project/search-window>
- WAIT
```

## GATE BLOCKED / UNCLEAR

```text
Phase: GATE
Candidate:
Ownership Gate: BLOCKED | UNCLEAR
Blocker/unclear:
Decision: stop
Not done:
- no code
- no patch
- no proof
- no tracker/GitHub/GitLab write
Next GO:
Recommended:
- WAIT
```

## INSPECT

```text
Phase: INSPECT
Candidate:
Root-cause hypothesis:
Affected paths:
Minimal fix:
Do not touch:
Risk:
Expected proof level:
Stop signs:
Next GO:
Recommended:
- Phase 5 <Checkout>
Why:
- The concrete checkout must be readiness-ok before PATCH.
```

## HYGIENE

```text
Phase: HYGIENE
Checked:
Clean:
Occupied/dirty/unclear:
Patch readiness:
- branch:
- clean/dirty:
- free/occupied:
- base/current state:
- ready for PATCH: yes/no
Needs OK for:
- fetch
- switch
- fast-forward
- cleanup
- branch delete
Next GO:
Recommended:
- Phase 6 <Issue> on <Checkout>
Why:
- Checkout is clean/free/readiness-ok.
```

## PATCH

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
Recommended:
- Phase 7 <Issue>
Why:
- Patch decision is PATCH-COMPLETE; the next step is proof.
Alternatives:
- WAIT
```

## PROOF

```text
Phase: PROOF
Candidate:
Fresh duplicate check:
- open PRs:
- Fixes/Closes:
- new maintainer/triage direction:
- stop labels/claims:
- Decision: CLEAR | BLOCKED | UNCLEAR
Proof type: source only | diagnostic runtime | live behavior
Tests/Proof:
Result:
Baseline comparison:
What was not tested:
Screenshots/Videos: yes/no
Expected rating:
Cleanup needed: yes/no
Next GO:
Recommended:
- Phase 8 <Issue>
Why:
- Proof is complete; the next step is PR body or publish decision.
Alternatives:
- WAIT
```

## Fresh Duplicate Check STOP

```text
Phase: PROOF | PR-BODY | PUBLISH
Status: STOP
Problem: duplicate | blocked | unclear
Fresh duplicate check:
- open PRs:
- Fixes/Closes:
- new maintainer/triage direction:
- stop labels/claims:
Decision: BLOCKED | UNCLEAR
Not done:
- no proof
- no PR body
- no commit/push/PR
- no tracker/GitHub/GitLab write
Next GO:
- Operator decides.
```

## Stop / Resume Protocol

Use on BLOCKED, UNCLEAR, duplicate, wrong layer, weak proof, missing setup, dirty checkout, or missing permission.

```text
Phase:
Status: STOP
Problem:
Evidence:
Blocked because:
Can resume if:
Required GO:
Do not repeat:
Not done:
Next GO:
- Operator decides.
```

## PR-BODY

```text
Phase: PR-BODY
Fresh duplicate check:
- Decision: CLEAR | BLOCKED | UNCLEAR
Source:
- Template read: `references/05-pr-body-template.md` yes/no
Checks:
- Canon structure unchanged:
- Helper text removed:
- Leak check:
- Related/Closes correct:
- Proof honest:
- Security Impact:
- Human Verification:
- Project risk check:
Next GO:
Recommended:
- Phase 9 <exact publish action>
Why:
- PR body is prepared; publish needs its own GO.
```

## PUBLISH

```text
Phase: PUBLISH
Allowed action:
Fresh duplicate check:
- Decision: CLEAR | BLOCKED | UNCLEAR
Executed:
Link/Commit:
Not executed:
Next safe step:
```

## UPSTREAM-PR-REVIEW

```text
Phase: UPSTREAM-PR-REVIEW
PR:
Scope:
Checked:
- PR metadata:
- Diff:
- relevant issue/review/CI signals:
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
- no patch
- no checkout
- no test server
- no tracker/GitHub/GitLab write
Next GO:
Recommended:
- WAIT
```

## Drift Stop

```text
Phase: <current>
Status: STOP
Reason: I drifted outside the allowed phase.
I was about to:
Allowed phase only allowed:
Next GO:
- Operator decides.
```

## No Commands

```text
Understood. No commands, no reads, no writes. I will wait.
```

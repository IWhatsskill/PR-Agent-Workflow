# Scout And Gate

## SCOUT Purpose

Find current, editable PR candidates for bug, stability, regression, reliability, documentation, or maintenance work.
Do not patch, do not prove, do not inspect code, and do not write a blocker catalog.

## Starting Point

Use the operator-provided issue tracker, triage board, label search, saved query, milestone, project board, or dashboard.

If no current source is provided:
- stop
- ask for the current tracker/query/dashboard source
- do not guess from old notes

Search windows can include:
1. Ready/triaged issues with no linked PR.
2. Clear-shape issues with no linked PR.
3. Small clear-shape issues with no linked PR.
4. Issues with source reproduction or failing test evidence.
5. Issues by impact: security, data-loss, crash, auth/provider, state, compatibility.
6. Recent regressions, release blockers, or CI failures.

SCOUT may check multiple defined search windows in one read-only pass until:
- up to 3 candidates are found, or
- the checked window can be reported as empty/blocker-heavy.

This does not authorize GATE, INSPECT, code reads, checkout use, patch, proof, or tracker/GitHub/GitLab write.

## Freshness

Default:
- Prefer activity from the last 7 days.

Fresh activity includes:
- new label
- triage bot comment
- maintainer comment
- new linked PR
- reopen
- new reproduction
- release/regression relevance
- dashboard/project-board mention

Older issues only qualify when:
- dashboard/project-current
- operator-named
- maintainer/triage-current
- release/regression-current
- fresh ready/fix-shape/repro signal

Old without exception:
- `DROP-old`
- one line
- no analysis

## Drop Rule

Drop immediately on:
- hard stop label
- open linked/canonical PR
- maintainer or triage counter-direction
- obviously blocking ownership
- wrong layer
- too old without freshness exception

Drop output:

```text
#12345: DROP, open canonical PR #99999.
```

No long problem description for drops.

## Stop Labels

Project-specific stop labels win.
Common hard stop examples:

```text
no-new-fix-pr
needs-maintainer-review
needs-product-decision
needs-security-review
needs-live-repro
needs-info
blocked
duplicate
wontfix
```

Continue only when the operator explicitly chooses that blocked issue for Blocked-Path-Review.

## Scout Output

Maximum 3 candidates, maximum 5 drops.

```text
Phase: SCOUT
Search window:
Checked:

Candidate 1:
Issue:
Freshness: activity <= 7 days | dashboard-current | operator-named
Why interesting:
Free signals:
Blocker check:
Status: LIKELY FREE | UNCLEAR

Drops:
- #...: DROP, <one-line reason>.

Next GO:
Recommended:
- <copyable next command>
Why:
- <one sentence>
Alternatives:
- WAIT
```

Do not write:

```text
There are no open issues to work on.
```

Unless the operator requested a true exhaustive search and the full defined search space was verifiably checked.

## Ownership Gate

GATE decides exactly one candidate:

```text
Ownership Gate: FREE | BLOCKED | UNCLEAR
```

Only `FREE` allows the next phase.
`FREE` is not PATCH-GO.

## Required Gate Searches

All searches are live/read-only:
1. Issue directly.
2. Issue comments.
3. Open PRs by issue number.
4. Open PRs by `Fixes #<issue>`.
5. Open PRs by `Closes #<issue>`.
6. Open PRs by symptom keywords.
7. Open PRs by exact error message, if available.
8. Open PRs by likely files/functions, if known.
9. Closed/merged PRs when canonical/superseding/approved.
10. Maintainer or triage-bot direction.
11. Assignee, claim, owner hints.
12. Stop labels.

With GATE-GO, all required searches for exactly one candidate may run in one read-only pass.
This does not authorize code deep dive, patch planning, checkout, proof, tests, or tracker/GitHub/GitLab write.

## Gate Decisions

FREE:
- issue is open or clearly canonical
- no open/canonical/superseding PR
- no owner/assignee/claim blocker
- no maintainer or triage counter-direction
- no hard stop label
- fix layer seems appropriate
- all required searches ran

BLOCKED:
- open/canonical/superseding PR found
- maintainer/triage names another direction
- hard stop label
- assignee/claim/owner blocks it
- issue is closed/superseded

UNCLEAR:
- required search could not run
- contradictory results
- possible overlap with another PR
- unclear fix layer
- unclear maintainer direction
- stale without clean freshness exception

## Gate Output

```text
Phase: GATE
Candidate:
Ownership Gate: FREE | BLOCKED | UNCLEAR

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
- FREE/BLOCKED/UNCLEAR because ...

Next only with Operator OK:
Recommended:
- <copyable next command>
Why:
- <one sentence>
Alternatives:
- WAIT
```

If not clearly FREE:

```text
STOP.
Do not build.
Do not prove.
Do not argue.
```

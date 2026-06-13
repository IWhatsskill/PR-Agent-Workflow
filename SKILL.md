---
name: pr-agent-workflow
description: "Safe phase-based workflow for PR agents: scout, ownership gate, inspect, hygiene, patch, proof, PR body, publish, and read-only upstream PR review."
license: MIT
---

# PR Agent Workflow

This skill is a workflow and safety protocol for pull request agents.
It is not permission to take action.

## Absolute Rule

Nothing happens without a clear GO from the operator.

A phase name authorizes only that phase.
It does not authorize the next phase.

Examples:
- `SCOUT-GO` allows read-only issue and PR metadata only.
- `GATE-GO` allows ownership and duplicate checks only.
- `INSPECT-GO` allows code and test inspection without edits.
- `HYGIENE-GO` allows local status and readiness checks only.
- `PATCH-GO` allows only the approved minimal change.
- `PROOF-GO` allows only the named tests or proof.
- `PUBLISH-GO` allows only the exact named write action.

If the operator says `no commands`, run no commands, including read-only commands.
If the operator says to stop, wait, or go inactive, do nothing in the background.

## System And Server Safety

Being on a server or inside a repository is not permission to act there.

No server, SSH, terminal, file, test, build, service, config, firewall, network, install, update, cleanup, issue tracker, GitHub/GitLab, patch, commit, push, or PR action may happen without the matching GO.

PR proof may use only a test environment named by the operator for that run.
Never assume a default server from old notes.
Use a disposable run directory when filesystem or server proof is needed.
Cleanup also needs explicit GO.

## Selective Loading

Do not load all references automatically.
Read only the files needed for the current phase.

- Start and safety: `references/00-safety.md`
- Phases: `references/01-phases.md`
- Scout and gate: `references/02-scout-and-gate.md`
- Patch, proof, publish: `references/03-patch-proof-publish.md`
- Output templates: `references/04-output-templates.md`
- PR body template: `references/05-pr-body-template.md`
- Failure patterns: `references/06-failure-patterns.md`
- Maintainer test notes: `references/07-maintainer-test.md`

## Phases

- Phase 0: STARTTEST / wake the agent
- Phase 1: ORIENT
- Phase 2: SCOUT
- Phase 3: GATE
- Phase 4: INSPECT
- Phase 5: HYGIENE / checkout readiness before patch
- Phase 6: PATCH
- Phase 7: PROOF
- Phase 8: PR-BODY
- Phase 9: PUBLISH
- Phase 10: SERVER-CONTEXT
- Phase 11: BLOCKED-PATH-REVIEW
- Phase 12: UPSTREAM-PR-REVIEW

Normal flow:

```text
SCOUT -> GATE -> INSPECT -> HYGIENE -> PATCH -> PROOF -> PR-BODY -> PUBLISH
```

Workflow order wins over numeric order.

## Hard Gates

- Read-only phases may bundle their required checks in one pass.
- Write/change phases always require exact GO.
- Bundling checks never authorizes phase escalation.
- Bundling checks never authorizes tests, builds, patch, commit, push, PR creation, comments, SSH/server work, or tracker/GitHub/GitLab write.
- `FREE` from GATE is not PATCH-GO.
- After INSPECT, the normal next phase is HYGIENE, not PATCH.
- PATCH must output `Coverage check` and `Patch decision`.
- PROOF may be recommended only when `Patch decision: PATCH-COMPLETE`.
- Before PROOF, PR-BODY, and every PUBLISH action, run a fresh duplicate check.
- If the fresh duplicate check is `BLOCKED` or `UNCLEAR`, stop.
- UPSTREAM-PR-REVIEW is read-only review of external or upstream PRs. No patch, no test server, no tracker/GitHub/GitLab write.

Concrete bundling rules:
- SCOUT may check multiple defined read-only search windows until it has up to 3 candidates or can report the checked window as empty/blocker-heavy.
- GATE may run all required ownership and duplicate searches for exactly one candidate in one read-only pass.
- HYGIENE may check status, branch, dirty/free state, and `git diff --stat` read-only.
- Fresh duplicate check is an implicit read-only required check inside PROOF, PR-BODY, and PUBLISH.

## Minimal Output

Start every answer with:

```text
Phase:
Result:
Not done:
Next GO:
```

When recommending a next GO, give exactly one copyable command and then stop.
A recommendation is not permission to execute it.

## Stop Protocol

Stop immediately on:
- new duplicate, open, or canonical PR
- ownership not clearly FREE
- project-defined stop label
- patch scope expanding beyond approval
- proof missing or too weak
- server/setup requiring new permission
- write action not exactly authorized
- dirty, occupied, or unclear checkout
- private or secret data risk

Output:

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
```

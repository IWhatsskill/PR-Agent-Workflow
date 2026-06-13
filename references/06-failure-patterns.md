# Failure Patterns

These patterns come from real agent failures.
Use them to stop early, not to complicate every phase.

## 1. SCOUT Becomes Blocker Triage

Wrong:
- broad search
- many blocked candidates
- long analysis for every blocker
- no useful FREE or LIKELY FREE candidate

Right:
- small search window
- maximum 3 candidate cards
- maximum 5 drops, one line each
- blocked candidates are not progress
- switch search window instead of writing a blocker catalog
- read-only search windows may be bundled, but still stop at up to 3 candidates or a clean empty/blocker-heavy window report

## 2. FREE Is Treated As PATCH-GO

Wrong:
- GATE reports FREE and the agent starts code/proof.

Right:
- FREE only means ownership looks clear.
- the next normal phase is INSPECT.
- after INSPECT comes Phase 5 checkout readiness.
- PATCH needs its own PATCH-GO.

## 3. INSPECT Skips Checkout Readiness

Wrong:
- INSPECT recommends PATCH directly.

Right:
- INSPECT recommends HYGIENE / checkout readiness.
- patch only when the concrete checkout is clean, free, and fresh enough.
- fetch/switch/fast-forward needs extra OK.

## 4. Patch Covers Only One Issue Variant

Wrong:
- patch covers only one input/config named by the issue.
- another explicit issue claim is ignored.
- proof is still recommended.

Right:
- Phase 6 must provide PATCH self-check.
- if a claim is not covered, explain why it is out of scope.
- if not clearly out of scope: PATCH-INCOMPLETE.

## 5. New Duplicate PR After Gate

Wrong:
- Gate was FREE earlier, and the agent continues proof/publish after a new PR appears.

Right:
- fresh duplicate check before PROOF, PR-BODY, and PUBLISH.
- direct/canonical PR means BLOCKED or UNCLEAR.
- stop: no proof, no PR body, no commit/push/PR.

## 6. Runtime Tools Missing During Proof

Wrong:
- node/test runner is missing, and the agent pretends proof is complete or installs tools on its own.

Right:
- stop
- name the missing tool/setup
- no install/update without extra OK
- do not publish with weak proof

## 7. Server Context Is Treated As Permission

Wrong:
- agent is on a server and runs commands because it can.

Right:
- being on a server is not permission.
- every server action needs exact GO.
- proof uses a disposable run directory.
- no cleanup without OK.

## 8. GitHub Write Drift

Wrong:
- after PR creation, the agent posts re-review, changes labels, reruns CI, or comments.

Right:
- every tracker/GitHub/GitLab write action needs exact PUBLISH-GO.
- after PR creation, read/report only.

## 9. Read-only Bundling Becomes Phase Escalation

Wrong:
- SCOUT bundles search windows and then starts GATE or INSPECT.
- GATE bundles duplicate checks and then starts code reading.
- HYGIENE checks status and then fetches or switches branch.

Right:
- bundling is read-only only.
- bundling never authorizes the next phase.
- write/change actions still need exact GO.

## 10. Old Archives Become Active Work

Wrong:
- old branch notes, PR notes, body, or proof are treated as the current task.

Right:
- active state decides active work.
- archive is evidence only when the exact topic is named.
- old proof does not prove a new PR.

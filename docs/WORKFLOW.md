# PR Agent Workflow

Safe phase-based workflow for pull request agents.

[Who this is for](#who-this-is-for) | [Core rule](#core-rule) | [Invoke](#how-to-invoke-the-skill) | [Flow](#normal-pr-flow) | [Phases](#phase-guide) | [Files](#included-files)

This skill helps an agent work on pull requests without jumping ahead. It separates discovery, ownership checks, inspection, checkout readiness, patching, proof, PR body work, and publishing into explicit phases. Each phase needs its own operator GO.

## At A Glance

- General-purpose phase-gated PR workflow.
- Separates scout, gate, inspect, hygiene, patch, proof, PR body, and publish.
- Requires explicit operator approval before each write step.
- Includes reusable output templates and failure-pattern guidance.
- Useful for cautious agent-assisted maintenance, bug fixing, and upstream PR work.

## Who This Is For

Use this skill when an agent should help with PR work while staying conservative around duplicates, ownership, proof quality, and write actions.

Good use cases:

- Find current bug, stability, regression, or maintenance candidates.
- Check whether an issue is free to work on before reading code deeply.
- Inspect a candidate and define the smallest safe fix.
- Prepare a patch only after the candidate is free and the checkout is ready.
- Prove the change before preparing a PR body.
- Publish only one explicitly approved write action.
- Review upstream or external PRs in read-only mode.

## Core Rule

A phase authorizes only that phase. It never authorizes the next one.

Examples:

- `SCOUT` does not allow `GATE`.
- `GATE FREE` does not allow `PATCH`.
- `PATCH` does not allow tests or proof.
- `PROOF` does not allow commits, pushes, PR creation, or comments.
- `PUBLISH` allows only the exact write action named by the operator.

Read-only phases may bundle their required checks. Write or change phases always require an exact GO.

## How To Invoke The Skill

Use the skill name from `SKILL.md`:

```text
Use pr-agent-workflow. Phase 0.
Use pr-agent-workflow. Phase 2 <project or search window>.
Use pr-agent-workflow. Phase 3 <issue or ticket>.
Use pr-agent-workflow. Phase 12 <pull request>.
```

The display folder is `PR Agent Workflow/`. The skill invocation name is `pr-agent-workflow`.

## Normal PR Flow

```text
SCOUT -> GATE -> INSPECT -> HYGIENE -> PATCH -> PROOF -> PR-BODY -> PUBLISH
```

Workflow order matters more than phase numbers.

## Phase Guide

### Phase 0: STARTTEST

Purpose: wake the agent and confirm it understands the safety model.

Use when:

- starting a fresh agent
- checking whether the skill is installed and understood
- recovering after context loss

Allowed:

- read the skill instructions
- report the safe next step

Not allowed:

- tracker or repository searches
- code reads
- tests
- patches
- writes

Example:

```text
Use pr-agent-workflow. Phase 0.
```

### Phase 1: ORIENT

Purpose: understand the active scope before work begins.

Use when:

- the agent needs to understand the current task
- the operator provides a handoff or current state
- the next action is unclear

Allowed:

- read only the state or handoff information provided or allowed by the operator
- identify the next safe phase

Not allowed:

- issue tracker, GitHub, or GitLab checks unless allowed
- code reads unless the operator allowed that scope
- tests, patches, proof, or writes

### Phase 2: SCOUT

Purpose: find current, editable bug, stability, regression, or maintenance PR candidates.

Use when:

- the operator wants fresh PR candidates
- the agent should search issue and PR metadata read-only
- the goal is candidate discovery, not fixing

Allowed:

- read-only issue and PR metadata
- operator-provided tracker, dashboard, label, query, milestone, or saved search
- multiple read-only search windows until up to 3 candidates are found
- short candidate cards
- short drop reasons for blocked candidates

Not allowed:

- code reads
- checkout use or reservation
- tests or proof planning
- patch ideas
- PR body work
- tracker, GitHub, or GitLab writes
- long blocker triage

Example:

```text
Use pr-agent-workflow. Phase 2 <project or search window>.
```

Expected result: up to 3 candidates marked `LIKELY FREE` or `UNCLEAR`, plus a recommended next GO.

### Phase 3: GATE

Purpose: decide whether exactly one candidate is free to work on.

Use when:

- SCOUT found a promising candidate
- the operator names one issue or ticket for ownership and duplicate checks

Allowed:

- read issue metadata and comments
- search open and closed duplicate or canonical PRs
- check maintainer, bot, or triage direction
- check assignee, claim, owner, and stop-label signals
- run all required duplicate and ownership checks for that one candidate in one read-only pass

Not allowed:

- deep code inspection except file names needed for duplicate search
- patch planning
- tests or proof
- checkout use
- write actions

Decision values:

- `FREE`: no blocking duplicate, owner, claim, stop label, or canonical PR found
- `BLOCKED`: a direct blocker exists
- `UNCLEAR`: the agent cannot safely decide

Normal next step after `FREE`: Phase 4 `INSPECT`, not `PATCH`.

Example:

```text
Use pr-agent-workflow. Phase 3 <issue or ticket>.
```

### Phase 4: INSPECT

Purpose: read affected code and tests to define the minimal safe fix.

Use when:

- GATE is `FREE`
- the operator wants root-cause analysis and patch shape

Allowed:

- read affected code paths
- read existing tests
- form a root-cause hypothesis
- describe the minimal fix
- estimate proof level and risk

Not allowed:

- file edits
- tests or builds
- proof execution
- checkout reservation unless allowed
- PR body work
- write actions

Normal next step: Phase 5 `HYGIENE` for a concrete checkout.

### Phase 5: HYGIENE

Purpose: check whether the local checkout is safe and ready for patching.

Use when:

- INSPECT is complete
- a concrete checkout or working tree is named
- the agent must verify clean/free state before PATCH

First read-only checks may include:

- branch name
- clean or dirty state
- free, occupied, or unclear status
- `git diff --stat`
- base/current freshness estimate

Not allowed without extra GO:

- fetch
- pull
- switch
- merge
- reset
- clean
- branch delete
- file delete
- write actions

Expected output includes:

```text
Patch readiness:
- branch:
- clean/dirty:
- free/occupied:
- base/current state:
- ready for PATCH: yes/no
```

### Phase 6: PATCH

Purpose: implement only the approved minimal patch.

Use when:

- GATE is `FREE`
- INSPECT is complete
- HYGIENE says the checkout is ready
- the operator gives PATCH-GO

Allowed:

- make the approved minimal code or test changes
- perform a PATCH self-check
- report whether the patch is complete or incomplete

Not allowed:

- tests or builds without PROOF-GO
- commit, push, PR creation, comments, or reviews
- broad refactors
- dependency or lockfile changes unless explicitly approved

Required output:

```text
Patch decision: PATCH-COMPLETE | PATCH-INCOMPLETE
```

Only recommend Phase 7 when the patch decision is `PATCH-COMPLETE`.

### Phase 7: PROOF

Purpose: prove the patch works.

Use when:

- PATCH is complete
- a fresh duplicate check is `CLEAR`
- the operator gives exact PROOF-GO

Allowed:

- run only the named tests or proof steps
- summarize results, failures, and untested areas
- include source-only, diagnostic runtime, or live behavior proof when approved

Not allowed:

- tracker, GitHub, or GitLab writes
- PR creation
- new installs or updates without extra approval
- server use unless the environment, purpose, and scope are named
- secret or private data exposure

If the fresh duplicate check becomes `BLOCKED` or `UNCLEAR`, stop before proof.

### Phase 8: PR-BODY

Purpose: prepare or check the PR body after the patch and proof are sufficient.

Use when:

- ownership is still `FREE`
- proof is sufficient
- the fresh duplicate check is `CLEAR`
- the operator allows PR body work

Allowed:

- draft the PR body using the bundled template
- check for overclaiming
- include proof and risk honestly

Not allowed:

- write the body to GitHub, GitLab, or another tracker
- use old PR bodies from memory
- overclaim proof or scope

### Phase 9: PUBLISH

Purpose: execute exactly one approved write action.

Use when:

- the operator names the exact action
- the fresh duplicate check is `CLEAR`
- the target repository, branch, PR, or comment target is explicit

Examples:

```text
PUBLISH-GO: commit only
PUBLISH-GO: push branch <branch> to <remote>
PUBLISH-GO: create draft PR
PUBLISH-GO: update PR body #123
PUBLISH-GO: post review comment only
PUBLISH-GO: rerun failed CI job only
```

Allowed only when named:

- commit
- push
- create draft PR
- update PR body
- comment
- review or re-review command
- CI rerun
- labels, reviewers, draft/ready state

Not allowed:

- any write action not named in the GO
- push to upstream/origin unless explicitly named
- ready/re-review/CI rerun after PR creation without a new GO

### Phase 10: SERVER-CONTEXT

Purpose: read and summarize server or test-environment rules only.

Use when:

- proof may involve a server or test environment
- the operator wants the agent to explain server safety before any action

Allowed:

- read server-safety rules bundled with the skill
- report that server work requires an explicitly named environment, purpose, scope, and cleanup permission

Not allowed:

- SSH
- server commands
- tests
- proof
- cleanup

### Phase 11: BLOCKED-PATH-REVIEW

Purpose: review a blocked or unclear issue only when the operator explicitly chooses it.

Use when:

- a candidate is blocked but the operator wants to understand why
- there may be a narrow remaining path worth documenting

Allowed:

- explain the blocker read-only
- identify a possible narrow path if one exists

Not allowed:

- treating the issue as a normal PR candidate
- patching
- proof
- checkout use
- write actions
- arguing against a clear blocker

### Phase 12: UPSTREAM-PR-REVIEW

Purpose: review external or upstream PRs read-only for relevance and risk.

Use when:

- the operator wants to understand an existing upstream PR
- the agent should estimate impact on integrations or agent setups

Allowed:

- PR metadata read-only
- PR diff read-only
- related issue, review, or CI summary read-only if allowed
- risk estimate for public APIs, auth, data/state, runtime, migrations, configuration, defaults, compatibility, and tests
- recommendation: `watch`, `test`, `wait`, `irrelevant`, or `blocked/unclear`

Not allowed:

- patch
- checkout use or reservation
- tests or builds
- test server or SSH
- PR comment or review submission
- CI rerun
- labels, reviewers, draft/ready changes
- write actions

## Practical Usage Pattern

1. Start with Phase 0 when waking a new agent.
2. Use Phase 2 to find candidates.
3. Use Phase 3 for one candidate only.
4. Continue to Phase 4 only if GATE is `FREE`.
5. Run Phase 5 before any patch work.
6. Patch only with Phase 6.
7. Prove only with Phase 7.
8. Draft the PR body only with Phase 8.
9. Publish only with an exact Phase 9 action.

After each phase, the agent should recommend exactly one next GO and stop. The recommendation is not permission to execute it.

## Stop Conditions

Stop immediately if any of these appear:

- new duplicate or canonical PR
- ownership not clearly `FREE`
- project-defined stop label or maintainer direction
- patch scope expands beyond approval
- proof is missing or too weak
- server/setup needs new permission
- write action is not exactly authorized
- checkout is dirty, occupied, or unclear
- private or secret data risk

## Included Files

```text
SKILL.md
agents/openai.yaml
references/00-safety.md
references/01-phases.md
references/02-scout-and-gate.md
references/03-patch-proof-publish.md
references/04-output-templates.md
references/05-pr-body-template.md
references/06-failure-patterns.md
references/07-skill-test.md
LICENSE
.clawhubignore
```

## License

MIT. See `LICENSE`.

# Skill Test

This file is optional validation documentation for the public skill.
It is not a runtime instruction.

## Structure Check

Expected:

```text
pr-agent-workflow/
  SKILL.md
  LICENSE
  .clawhubignore
  agents/openai.yaml
  references/00-safety.md
  references/01-phases.md
  references/02-scout-and-gate.md
  references/03-patch-proof-publish.md
  references/04-output-templates.md
  references/05-pr-body-template.md
  references/06-failure-patterns.md
  references/07-skill-test.md
```

## Redaction Check

Before release, search for:

```text
project-specific private names from source skill
private server names
private agent names
local absolute paths
SSH aliases from private source
tailnet names
\.ssh
token|secret|password|private key
source-project-specific public issue numbers or project names
```

Allowed matches should be generic safety terms only.

## Validation

Run the skill validator:

```text
quick_validate.py pr-agent-workflow
```

Check the ZIP:
- paths use `/`
- `LICENSE` is present
- no secret-looking filenames
- no local paths
- no project-specific adapter with private operational details

## Forward-Test Prompts

### Starttest

```text
Use pr-agent-workflow. Phase 0.
Read only SKILL.md and the start/safety reference.
Output what is forbidden without further GO.
```

Expected:
- no commands
- no tracker/GitHub/GitLab write
- no SSH/server action
- next GO named

### Scout

```text
Use pr-agent-workflow. Phase 2.
Search the operator-provided issue query read-only for up to three likely free bug or stability candidates.
```

Expected:
- maximum 3 candidates
- may bundle multiple defined read-only search windows
- blocked candidates dropped briefly
- no code reads
- no patch
- next GO: GATE for one candidate

### Gate Bundling

```text
Use pr-agent-workflow. Phase 3 <issue>.
Run the ownership and duplicate gate read-only for exactly this issue.
```

Expected:
- all required gate searches may run in one read-only pass
- no code deep dive
- no patch/proof
- no tracker/GitHub/GitLab write

### Blocked Path

```text
Use pr-agent-workflow. Phase 11 <issue>.
Review read-only whether any remaining path exists despite blockers.
```

Expected:
- no normal PR work
- clear BLOCKED/UNCLEAR/FREE-REST
- no patch/proof/write

### Cleanup

```text
Use pr-agent-workflow. Phase 12 <approved disposable run directory>.
Clean up only that exact disposable run directory. No wildcards, no parent/root cleanup.
```
### Upstream PR Review

```text
Use pr-agent-workflow. Phase 13 STATE-GO for <issue-or-pr>.
Use pr-agent-workflow. Phase 14 <PR>.
Read-only PR review. No patch, no checkout, no test server, no write.
```

Expected:
- relevance/risk for integrators or agent operators
- recommendation: watch / test / wait / irrelevant




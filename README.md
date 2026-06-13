# PR Agent Workflow

Safe phase-based workflow for pull request agents.

[Quick start](#quick-start) | [Phases](#phases) | [Safety](#safety) | [Files](#files) | [Install](#install)

## Overview

`pr-agent-workflow` helps an agent work on pull requests without jumping ahead. It separates scouting, ownership checks, inspection, checkout hygiene, patching, proof, PR body work, and publishing into explicit phases.

Use it for cautious agent-assisted maintenance, bug fixes, regressions, upstream PR work, and small patch flows where every write action should be deliberate.

## Quick Start

```text
Use pr-agent-workflow. Phase 0.
Use pr-agent-workflow. Phase 2 <project or search window>.
Use pr-agent-workflow. Phase 3 <issue or ticket>.
Use pr-agent-workflow. Phase 12 <pull request>.
```

The skill invocation name is:

```text
pr-agent-workflow
```

## Phases

```text
STARTTEST -> ORIENT -> SCOUT -> GATE -> INSPECT -> HYGIENE -> PATCH -> PROOF -> PR-BODY -> PUBLISH
```

The important rule is simple:

```text
One phase does not authorize the next phase.
```

Examples:

- `SCOUT-GO` allows read-only issue and PR metadata only.
- `GATE-GO` allows ownership and duplicate checks only.
- `PATCH-GO` allows only the approved minimal change.
- `PROOF-GO` allows only the named proof.
- `PUBLISH-GO` allows only the exact named write action.

## Safety

- No server, SSH, terminal, file, test, build, service, config, network, install, update, cleanup, issue tracker, GitHub/GitLab, patch, commit, push, or PR action without the matching GO.
- Read-only phases may inspect only the approved scope.
- Write phases require exact operator approval.
- If the operator says stop, wait, inactive, or no commands, the agent does nothing.
- Secrets and private data must not be printed.

## Files

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
references/07-maintainer-test.md
```

## Install

Clone or copy this folder into your skills directory, then invoke the skill by name:

```text
pr-agent-workflow
```

## License

MIT

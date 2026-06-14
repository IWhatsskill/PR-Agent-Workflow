# PR Agent Workflow

**Safe phase-based workflow for pull request agents**

This is a compact Codex skill for agents working on pull requests. It keeps PR work split into explicit phases so an agent can scout, inspect, patch, prove, and publish without skipping approval gates.

Use it when you want cautious agent-assisted maintenance, bug fixing, upstream PR work, or review handoffs without turning the agent into autopilot.

<p align="center">
  <a href="SKILL.md"><strong>Skill file</strong></a>
  &nbsp;|&nbsp;
  <a href="docs/WORKFLOW.md"><strong>Workflow guide</strong></a>
  &nbsp;|&nbsp;
  <a href="references/04-output-templates.md"><strong>Output templates</strong></a>
  &nbsp;|&nbsp;
  <a href="references/05-pr-body-template.md"><strong>PR body template</strong></a>
  &nbsp;|&nbsp;
  <a href="references/06-failure-patterns.md"><strong>Failure patterns</strong></a>
</p>

## What It Covers

| Area | Purpose |
| --- | --- |
| Scout and gate | Find useful PR candidates and avoid duplicate or owned work. |
| Inspect and patch | Read narrowly, define the smallest safe change, then patch only after GO. |
| Proof and publish | Run proof, prepare PR notes, and publish only the exact approved action. |

## Quick Start

Ask Codex to use the skill by name:

```text
Use pr-agent-workflow. Phase 0.
Use pr-agent-workflow. Phase 2 <project or search window>.
Use pr-agent-workflow. Phase 3 <issue or ticket>.
```

The detailed phase guide lives in [docs/WORKFLOW.md](docs/WORKFLOW.md).

## Repository Map

| Path | Purpose |
| --- | --- |
| [SKILL.md](SKILL.md) | Main skill entry file. |
| [docs/WORKFLOW.md](docs/WORKFLOW.md) | Full README-style guide with phases, stop conditions, and usage patterns. |
| [references/](references/) | Safety rules, phase details, templates, and failure-pattern notes. |
| [agents/openai.yaml](agents/openai.yaml) | Agent metadata/config included with the skill package. |

## License

MIT. See [LICENSE](LICENSE).

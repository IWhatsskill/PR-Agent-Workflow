# Install

Install the PR Agent Workflow skill from GitHub repo `IWhatsskill/PR-Agent-Workflow`.

## For All Local Agents

```bash
openclaw skills install git:IWhatsskill/PR-Agent-Workflow@main --global
```

## For One Specific Agent Or Workspace Only

1. Switch to that agent's workspace.
2. Install without `--global`.

```bash
openclaw skills install git:IWhatsskill/PR-Agent-Workflow@main
```

Then start a new session so the skill snapshot reloads.

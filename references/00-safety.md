# Safety

Read this before any PR work with this skill.

## Operator GO

The operator's latest concrete instruction controls the current step within safe limits.

Without clear GO:
- execute nothing
- write nothing
- delete nothing
- commit nothing
- push nothing
- create or update no PRs
- run no tests or builds
- do no SSH or server work
- do no setup, update, or cleanup action

If the operator allows only read-only work, stay read-only.
If the operator says `no commands`, run no commands.

## Secrets And Private Data

Never read, print, copy, or package:
- private keys
- API tokens
- GitHub tokens
- passwords
- `.env` contents
- private logs with personal data
- production data
- private account IDs
- local absolute paths from private environments
- private server names or SSH aliases
- private agent or workspace names

Public keys may be shown only when the operator asks for that exact public key.

## Public Skill Boundary

This public skill must not contain private infrastructure.

Local organizations may keep adapter notes outside this public skill.
Such adapter notes may only make the workflow stricter, not weaker.

Adapters may define:
- allowed checkouts
- allowed test environments
- PR body template source
- internal output conventions
- additional stop labels

## Server Safety

For server or test environment work, the operator must name:

```text
Server/test environment:
Purpose:
Allowed scope:
Cleanup permission:
```

Without those details:
- do not SSH
- do not test connectivity
- run no server commands
- read no server logs
- write no server files
- restart no services
- change no firewall, VPN, or network settings
- use no real private accounts or secrets for proof

## Archives And Old Notes

Old PR notes, proof, bodies, logs, and archives are not automatically active.
They are not work instructions unless the operator names the exact topic or the active state names it.

If current state and old notes conflict:
- stop
- report the conflict briefly
- ask for the next GO

## Communication

Be brief, direct, and auditable.
Mark uncertainty as a hypothesis.
Do not write long blocker lists.
Drop blocked candidates immediately and briefly.

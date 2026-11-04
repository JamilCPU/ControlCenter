# ControlCenter

ControlCenter was an experiment in running a home lab with a small team of role-based AI agents. It contains no application code: the repository is a set of Markdown prompts (one per agent role) plus a `CLAUDE.md` describing the idea, intended to be loaded as context by Claude Code sub-agents. A "Master Auditor" prompt dispatches the specialist roles in parallel and combines their reports into a single "State of the Lab" summary.

This was an experiment and has been superseded; the repository is kept for reference.

## Stack

- Markdown agent definitions, written for Claude Code's Agent tool
- No build step, no runtime dependencies

## Running

There is nothing to install or run. To try the approach, open a Claude Code session in this directory so `CLAUDE.md` is picked up, then ask it to act as one of the roles, for example:

```text
Load agents/infrastructure.md and run a full health check.
```

The prompts describe a specific home lab (a Windows PC and several Raspberry Pis), so network details are `192.0.2.x` documentation placeholders; replace them and the described services with your own before using the roles.

## Project layout

- `CLAUDE.md` - project purpose, directory conventions and how the auditor dispatches agents
- `agents/auditor.md` - Master Auditor: runs sub-audits and scores overall health
- `agents/infrastructure.md` - Docker, services and network health
- `agents/pi-cluster.md` - Raspberry Pi fleet operations
- `agents/security.md` - secrets, CORS and exposed-port review
- `agents/quality.md` - tests, linting, documentation and project hygiene
- `agents/devops.md` - CI/CD, backups and deployment automation
- `agents/research.md` - tool and approach evaluation

## Status

Superseded and no longer maintained. The `state/` directory referenced in `CLAUDE.md` (audit history) is deliberately not versioned.

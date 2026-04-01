# ControlCenter

## Mission

**Build a self-improving machine that creates monetizable software with meaningful impact on other humans.**

This is a dual mandate — not a trade-off:

1. **Self-improvement** — The lab infrastructure, agent stack, and automation pipelines get more capable over time. Every fix, every new agent, every automated process makes the next task easier and faster.

2. **External value** — The machine ships real products to real users. Software that solves problems, reaches people, and generates revenue. Not just internal tooling.

These reinforce each other: a more capable lab ships better products faster. Successful products justify more investment in the lab.

## Project Purpose

ControlCenter is the orchestration hub for this home lab network. It does not run services or write code. Its sole purpose is to coordinate autonomous agents that manage the lab AND produce valuable external products.

The Control Center Director (invoked via `/control-center`) is the executive layer: it thinks strategically, dispatches specialists, and drives the system toward its dual mandate.

Think of it as the "brain": it holds agent definitions, tracks state, coordinates work, and always asks — *does this make the machine better at creating value for humans?*

## Current Product Portfolio

| Product | Status | External Users | Monetization Path |
|---------|--------|---------------|------------------|
| SIGNAL AI News | Live, no users | 0 | Sponsorships, newsletter |
| Knowledge Hub | Spec only | 0 | TikTok creator fund |
| Portfolio | Live, personal | Personal | N/A |
| YoutubeUploader | Active, personal | 1 | Open-source / sponsor |

## Quality Bar for External Products

Before any product reaches external users, it must have:
- Tests on critical user paths
- Monitoring and uptime alerting
- No committed secrets or plaintext credentials
- A README a stranger can understand
- Error handling at all user-facing boundaries

## Directory Structure

```
F:/Dev/ControlCenter/
  agents/             # Agent role definitions (Markdown files)
    auditor.md        # Master Auditor -- dispatches full system audits
    infrastructure.md # Infrastructure Agent -- Docker, services, endpoints, Pis
    [future agents]   # Security, Code Quality, Deployment, etc.
  state/              # Persistent state and tracking files
    roadmap.md        # Prioritized improvement plan, updated after each audit
    [future state]    # Audit snapshots, metrics history, etc.
  CLAUDE.md           # This file -- project context for any agent working here
```

### agents/

Each file in `agents/` defines a single agent role using a standardized structure:

```
# Role: [Name]
## Identity       -- Who the agent is and what it does
## Context        -- What it needs to know (topology, services, paths)
## Capabilities   -- What it is authorized to do autonomously
## Procedures     -- Step-by-step playbooks it follows
## Constraints    -- Hard rules it must never violate
## Output Format  -- How it structures its reports
```

Agent files are not executable. They are prompts. When the Master Auditor or a user invokes an agent, the agent file contents are loaded as context for a new Agent tool call.

### state/

State files track the lab's condition over time. The primary state file is `roadmap.md`, which contains:

- The prioritized improvement roadmap (Phases 1-5)
- Metadata: last audit date, next scheduled audit, overall health score
- A changelog of when items were completed or updated

State files are updated after each audit cycle. They serve as the baseline for delta comparisons between audits.

## How the Auditor Works

The Master Auditor (`agents/auditor.md`) is the primary entry point for system-wide assessment. Its workflow:

1. **Load baseline** -- reads `state/roadmap.md` and searches shared memory for the last audit
2. **Dispatch sub-audits** -- launches parallel Agent tool calls, one per domain:
   - Infrastructure health (via `agents/infrastructure.md`)
   - Security scan (inline instructions)
   - Code quality check (inline instructions)
   - Pi cluster status (via `agents/infrastructure.md`)
   - Swarm pipeline review (inline instructions)
3. **Collect results** -- waits for all sub-audits to return structured reports
4. **Compare against baseline** -- marks each roadmap item as IMPROVED, UNCHANGED, DEGRADED, or NEW
5. **Score** -- computes an overall health score (0-100) with weighted domain scores
6. **Synthesize** -- produces a unified "State of the Lab" report
7. **Persist** -- saves the audit summary to shared memory for future comparisons

## How Agents Are Dispatched

Agents are dispatched using the Agent tool. The caller passes:

- The agent role definition (from `agents/*.md`) as context
- A specific task or question
- Any relevant data the agent needs

Agents run in parallel when their tasks are independent. The Master Auditor dispatches all domain sub-audits simultaneously, then joins results.

Example dispatch pattern (conceptual):
```
[Agent tool call: Infrastructure Health]
  Context: contents of agents/infrastructure.md
  Task: "Run a full health check and return the structured report"

[Agent tool call: Security Scan]
  Context: inline security audit instructions from auditor.md
  Task: "Scan all projects for secrets, CORS issues, and exposed ports"

[Agent tool call: Code Quality]
  Context: inline code quality instructions from auditor.md
  Task: "Assess tests, CI, deps, READMEs, and linting for all projects"
```

All three run in parallel. Results are collected and synthesized by the auditor.

## How State Is Tracked

State flows through three layers:

1. **roadmap.md** -- the persistent, human-readable improvement plan. Updated after each audit. This is the source of truth for what needs to be done.
2. **Shared memory (MCP)** -- the agent-memory server at :8765. Stores audit summaries, solutions to past problems, and lab facts. Searchable by any agent on the network. This is the source of truth for historical data.
3. **Git history** -- the ControlCenter repo itself. Changes to agent definitions and state files are committed, providing a full audit trail.

After every full audit:
- `roadmap.md` is updated with current status of each item
- An audit summary is saved to shared memory with tags like `["audit", "state-of-the-lab"]`
- Changes are committed to the repo

## Integration with Swarm

The Swarm project (`F:/Dev/Swarm/`) contains specialized AI agents for various tasks. ControlCenter audits Swarm's capabilities as part of the full system audit but does not manage Swarm's internals. The relationship is:

- ControlCenter **audits** Swarm (agent availability, recent activity, capability gaps)
- ControlCenter **does not modify** Swarm agents, configs, or code
- Swarm agents may **read** ControlCenter state (roadmap, agent definitions) when they need lab context
- Both ControlCenter and Swarm use the **shared memory server** as a common knowledge layer

## Integration with Shared Memory

The shared memory MCP server (`F:/Dev/agent-memory/`, port :8765) is the lab's collective knowledge base. ControlCenter agents interact with it through MCP tool calls:

- `search_memory(query)` -- find past audit results, known issues, solutions
- `save_memory(memory, content, tags, device)` -- store new findings
- `list_recent_memories()` -- see what has been recorded recently

Every agent should **search memory before starting work** and **save results after completing work**. This ensures the lab gets smarter over time and agents do not re-solve problems that have already been resolved.

## Core Rules

1. **The auditor never writes code.** It audits, reports, and recommends. It never modifies application source code, Docker configurations, or environment files.
2. **The auditor never executes fixes.** It identifies problems and suggests solutions. A human or a specialized agent (infrastructure, deployment) executes the fix.
3. **Every audit compares against the baseline.** There is no standalone audit. Every assessment references the prior state to measure trajectory.
4. **Every audit persists its results.** Findings are saved to shared memory and state files are updated. No audit should be lost.
5. **Agents are read-only by default.** Unless an agent's role definition explicitly grants write access to a specific system, it operates in read-only mode.
6. **State files are the contract.** If it is not in `roadmap.md` or shared memory, it did not happen. Agents trust state files over assumptions.

# claude-plugins

Personal Claude Code plugin — 14 specialized agents with an orchestrator-first workflow.

## Usage

### Load for a single session

```bash
claude --plugin-dir ./claude-plugins
```

### Install globally (user scope)

Requires a marketplace. See [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) to self-host one, then:

```bash
claude plugin install claude-plugins@your-marketplace
```

### Reload after changes

```
/reload-plugins
```

## How it works

When the plugin is active, `settings.json` sets `orchestrator` as the main agent. The orchestrator breaks down tasks and delegates to the appropriate specialized agent. Simple questions are answered directly without delegation.

## Agents

| Agent | Model | Role |
|---|---|---|
| **orchestrator** | inherited | Workflow coordinator — delegates to other agents |
| **architect** | inherited | System design and implementation planning |
| **ask** | inherited | Read-only Q&A and explanations |
| **code** | sonnet | Hands-on implementation (default workhorse) |
| **code-reviewer** | sonnet | Pre-commit code review |
| **db** | sonnet | Database schema, migrations, queries |
| **debug** | sonnet | Bug investigation and root cause analysis |
| **devops** | sonnet | Docker, CI/CD, infrastructure |
| **docs** | sonnet | Documentation writer |
| **pr-writer** | inherited | PR titles and descriptions |
| **refactor** | sonnet | Code restructuring without behavior changes |
| **researcher** | inherited | External research via web search |
| **security** | inherited | Security auditing (read-only) |
| **tester** | sonnet | Test writing |

"Inherited" means the agent uses whatever model is active in your Claude Code session.

### Design principles

- **Orchestrator-first**: `settings.json` activates the orchestrator as the main thread for all non-trivial tasks.
- **Parallel execution**: The orchestrator spawns independent subtasks (e.g. multiple file writes) as parallel agents rather than sequentially.
- **Model tiering**: Planning/research agents inherit the session model; implementation agents use sonnet for speed.
- **Permission modes**: Read-only agents (`ask`, `architect`, `code-reviewer`, `pr-writer`, `researcher`, `security`) use `permissionMode: plan` to prevent accidental writes.
- **Clear boundaries**: Each agent has explicit "What You Should NOT Do" guardrails.

## Structure

```
claude-plugins/
├── .claude-plugin/
│   └── plugin.json       # Plugin manifest
├── agents/               # 14 agent definitions
├── settings.json         # Activates orchestrator as main agent
└── README.md
```

## Requirements

- Claude Code >= 1.0.33

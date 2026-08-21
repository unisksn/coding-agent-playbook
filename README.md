# Coding Agent Playbook

Practical patterns, workflows, prompts, and harnesses for AI coding agents.

This repository collects reusable materials for working effectively with coding agents such as **Claude Code, Codex, and Cursor**.

The focus is not on a specific tool, language, or repository, but on practical engineering workflows that improve correctness, context efficiency, review quality, and maintainability.

## Contents

### Prompts

Reusable prompts for auditing, designing, and improving coding-agent workflows.

| Resource                                                            | Description                                                                                                              |
| ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| [Claude Code Harness Audit](./prompts/claude-code-harness-audit.md) | Audit and improve a personal Claude Code harness for use across multiple repositories, languages, and technology stacks. |

### Slides

Presentation materials for explaining coding-agent workflows and engineering practices.

| Resource                                                                                                                         | Description                                                                                                                                                                         |
| -------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Claude Code Engineering Workflow](https://unisksn.github.io/coding-agent-playbook/slides/claude-code-engineering-workflow.html) | Practical workflow for model selection, planning, implementation, context management, subagents, and independent review. ([source](./slides/claude-code-engineering-workflow.html)) |

## Repository Harness

This repository also applies coding-agent instructions to itself.

The shared repository instructions are defined in [`AGENTS.md`](./AGENTS.md). They currently focus on safely working with a public repository, including preventing secrets, credentials, and non-public information from being committed.

[`CLAUDE.md`](./CLAUDE.md) imports these shared instructions for Claude Code instead of duplicating them.

```text
AGENTS.md
    ↑
CLAUDE.md
```

The intention is to keep repository-wide rules:

* tool-independent where possible
* small and explicit
* focused on preventing concrete failures
* shared rather than duplicated across coding-agent configurations

Additional rules should be introduced only when there is a clear repository-wide need.

## Repository Structure

```text
coding-agent-playbook/
├── AGENTS.md
├── CLAUDE.md
├── README.md
├── prompts/
│   └── claude-code-harness-audit.md
└── slides/
    └── claude-code-engineering-workflow.html
```

* `AGENTS.md` — shared repository instructions for coding agents.
* `CLAUDE.md` — Claude Code entry point that imports the shared instructions.
* `prompts/` — prompts intended to be given directly to coding agents.
* `slides/` — presentation materials, published through GitHub Pages.

Additional categories will be introduced only when they are needed.

## Principles

The materials in this repository generally favor:

* clear outcomes, constraints, and completion criteria over excessive step-by-step prompting
* deliberate context management
* separating exploration, planning, implementation, and review when appropriate
* using independent review for important design and implementation decisions
* keeping reusable harness instructions small and maintainable
* sharing tool-independent repository rules instead of duplicating them
* treating tool-specific recommendations as changeable rather than permanent rules

## Point-in-Time Guidance

Coding agents evolve quickly.

Model behavior, features, context management, pricing, and recommended workflows can change over time. Tool-specific materials in this repository should therefore be treated as **point-in-time guidance**, not permanent best practices.

When applying them, check the current official documentation for the relevant tool.

# Coding Agent Playbook

Practical patterns, workflows, prompts, and harnesses for AI coding agents.

This repository collects reusable materials for working effectively with coding agents such as **Claude Code, Codex, and Cursor**.

The focus is not on a specific tool, language, or repository, but on practical engineering workflows that improve correctness, context efficiency, review quality, and maintainability.

## Contents

### Articles

Long-form write-ups on engineering practices for working with coding agents.

| Resource                                                                                            | Description                                                                                                                                                     |
| ----------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Rethinking Code Review in the AI Era](./articles/rethinking-code-review-in-the-ai-era.md) | Why splitting a pull request should be decided by dependencies, risk, and verifiability rather than by size, and how to divide review work between humans and AI. Written in Japanese. |

### Prompts

Reusable prompts for auditing, designing, and improving coding-agent workflows.

| Resource                                                            | Description                                                                                                              |
| ------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------ |
| [Claude Code Harness Audit](./prompts/claude-code-harness-audit.md) | Audit and improve a personal Claude Code harness for use across multiple repositories, languages, and technology stacks. |
| [Session Name Setup](./prompts/session-name-setup.md) | Install a user-scope `session-name` skill and a `UserPromptSubmit` hook that name Claude Code sessions as `{repo}-{PR or ticket}-{description}`, so they are easy to find in `/resume`. Written in Japanese. |

### Slides

Presentation materials for explaining coding-agent workflows and engineering practices.

| Resource                                                                                                                         | Description                                                                                                                                                                         |
| -------------------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Claude Code Engineering Workflow](https://unisksn.github.io/coding-agent-playbook/slides/claude-code-engineering-workflow.html) | Practical workflow for model selection, planning, implementation, context management, subagents, and independent review. ([source](./slides/claude-code-engineering-workflow.html)) |

### Skills

Reusable [Agent Skills](https://agentskills.io/) that coding agents load on demand. Each skill is a directory with a `SKILL.md`, and is written to be independent of any particular repository.

| Resource                                                | Description                                                                                                                                                                                        |
| ------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Code Review Strict](./skills/code-review-strict/SKILL.md) | Review a change for security, privacy, data integrity, and performance before releasing it to production. Invoked as `/code-review-strict [PR number \| branch \| revision range \| path]`. Written in Japanese. |
| [Frontend Testing](./skills/frontend-testing/SKILL.md) | Decide which layer (unit, component, E2E) a frontend test belongs to, and avoid duplication, excessive mocking, and brittle selectors. Written in Japanese. |

To use a skill, symlink or copy its directory into the skills location of your agent:

```text
~/.claude/skills/<name>   # Claude Code, user scope
~/.agents/skills/<name>   # Codex, user scope
.claude/skills/<name>     # Claude Code, repository scope
.agents/skills/<name>     # Codex, repository scope
```

Symlinking keeps this repository as the single source of truth, so local edits show up as a diff here instead of drifting across copies.

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
├── LICENSE
├── README.md
├── articles/
│   ├── images/
│   │   └── pr-review-throughput-ja.svg
│   └── rethinking-code-review-in-the-ai-era.md
├── prompts/
│   ├── claude-code-harness-audit.md
│   ├── security-privacy-data-performance-review.md
│   └── session-name-setup.md
├── skills/
│   ├── code-review-strict/
│   │   └── SKILL.md
│   └── frontend-testing/
│       └── SKILL.md
└── slides/
    └── claude-code-engineering-workflow.html
```

* `AGENTS.md` — shared repository instructions for coding agents.
* `CLAUDE.md` — Claude Code entry point that imports the shared instructions.
* `articles/` — long-form write-ups on engineering practices, with their images.
* `prompts/` — prompts intended to be given directly to coding agents.
* `skills/` — Agent Skills that agents load on demand, each in its own directory with a `SKILL.md`.
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

## License

[MIT](./LICENSE)

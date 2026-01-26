# Agent Skills Library

This repository contains a collection of **Agent Skills** designed to guide AI software engineers (agents) through a structured, documentation-driven development process.

## Using These Skills

You can use the `skr` CLI to install these skills into your own projects.

### 1. Install from Registry (Recommended)
These skills are automatically published to GitHub Container Registry.

```bash
# General syntax
skr install ghcr.io/andrewhowdencom/<skill>

# Examples:
skr install ghcr.io/andrewhowdencom/git:latest
skr install ghcr.io/andrewhowdencom/go:latest
skr install ghcr.io/andrewhowdencom/ci:latest
```

### 2. Install from Source
If you are modifying these skills or working offline:

```bash
# 1. Build the skill locally
cd skills/go
skr build . --tag go:local

# 2. Install into your project
cd /path/to/your/project
skr install go:local
```

## Available Skills

| Skill | Description | Registry Ref |
| :--- | :--- | :--- |
| **[architecture](./skills/architecture/SKILL.md)** | System design patterns | `ghcr.io/andrewhowdencom/architecture` |
| **[ci](./skills/ci/SKILL.md)** | CI/CD pipelines | `ghcr.io/andrewhowdencom/ci` |
| **[cli](./skills/cli/SKILL.md)** | CLI development | `ghcr.io/andrewhowdencom/cli` |
| **[configuration](./skills/configuration/SKILL.md)** | Config management | `ghcr.io/andrewhowdencom/configuration` |
| **[dependency-injection](./skills/dependency_injection/SKILL.md)** | DI patterns | `ghcr.io/andrewhowdencom/dependency-injection` |
| **[documentation](./skills/documentation/SKILL.md)** | Documentation standards | `ghcr.io/andrewhowdencom/documentation` |
| **[git](./skills/git/SKILL.md)** | Git workflow & standards | `ghcr.io/andrewhowdencom/git` |
| **[go](./skills/go/SKILL.md)** | Go language standards | `ghcr.io/andrewhowdencom/go` |
| **[instrumentation](./skills/instrumentation/SKILL.md)** | Metrics & Tracing | `ghcr.io/andrewhowdencom/instrumentation` |
| **[rpc](./skills/rpc/SKILL.md)** | RPC/Proto standards | `ghcr.io/andrewhowdencom/rpc` |
| **[task_runner](./skills/task_runner/SKILL.md)** | Taskfile patterns | `ghcr.io/andrewhowdencom/task_runner` |
| **[tools](./skills/tools/SKILL.md)** | Tooling configuration | `ghcr.io/andrewhowdencom/tools` |

## Philosophy: Inlined Expertise

All skills in this repository follow the **Inlined Expertise** philosophy. The `SKILL.md` file contains the most critical, actionable "Standard Operating Procedures" (SOPs) directly, reducing the need for agents to fetch external references for common tasks.

# Project Template

This repository is a template for new software projects, designed to guide AI software engineers (agents) through a structured, documentation-driven development process.

## Skills

This project uses [Agent Skills](https://agentskills.io) to provide context and instructions to AI agents.
All skills are located in the [`skills/`](./skills/) directory.

Each skill contains a `SKILL.md` file with specific instructions and references to detailed documentation.

- [architecture](./skills/architecture/SKILL.md)
- [ci](./skills/ci/SKILL.md)
- [cli](./skills/cli/SKILL.md)
- [configuration](./skills/configuration/SKILL.md)
- [dependency-injection](./skills/dependency_injection/SKILL.md)
- [documentation](./skills/documentation/SKILL.md)
- [git](./skills/git/SKILL.md)
- [instrumentation](./skills/instrumentation/SKILL.md)
- [languages](./skills/languages/SKILL.md)
- [rpc](./skills/rpc/SKILL.md)
- [task_runner](./skills/task_runner/SKILL.md)
- [tests](./skills/tests/SKILL.md)
- [tools](./skills/tools/SKILL.md)

## Installation

Skills can be installed globally for use across all projects, or maintained locally for project-specific needs.

### Global Installation (Recommended for Defaults)
To make these skills available to Antigravity globally:

```bash
# Create global skills directory
mkdir -p ~/.antigravity/skills

# Install all skills
cp -r skills/* ~/.antigravity/skills/
```

### Single Skill Installation
To install a specific skill (e.g., just `git` conventions):

```bash
cp -r skills/git ~/.antigravity/skills/
```

## Usage & Colocation

Antigravity and other agents check for skills relative to the **Active Workspace Root** (the folder you have opened).

### Resolution Order

1.  **Workspace Local**: `./skills/`
    *   *Location*: Directly inside your open project folder.
    *   *Use Case*: Project-specific conventions, overrides, and version-controlled skills.
2.  **User Global**: `~/.antigravity/skills/`
    *   *Location*: User's home directory.
    *   *Use Case*: Personal defaults and shared tools across all projects.

### Working with Git Worktrees

If you use **Git Worktrees**, specific behaviors apply:

*   **Committed Skills**: If `skills/` is committed to your repository, it will be present in every worktree directory. Opening any worktree folder will correctly load the skills for that branch.
*   **Shared/Untracked Skills**: If you prefer to manage skills *independently* of the git history (e.g., sharing one `skills/` folder across multiple worktrees without committing it), you can:
    1.  Place `skills/` in the parent directory of your worktrees.
    2.  **Symlink** it into your active worktree: `ln -s ../skills ./skills`.


### Overriding a Skill
To override a global skill for a specific project (e.g., a specific `ci` workflow):
1.  Create a `skills/ci` directory in your project root.
2.  Add your custom `SKILL.md`.
3.  The agent will use the local `skills/ci` instead of the global one.


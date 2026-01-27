---
name: worktree
description: Standard Operating Procedures for managing git worktrees to isolate tasks.
---

# Worktree Management

This skill mandates and guides the use of `git worktree` for task isolation.

## 1. Detection & Mandate

**CRITICAL**: You MUST inspect the workspace root before beginning work.

### Recognition Pattern
You are in a **Worktree-Enabled Workspace** if:
1.  There is a directory named `main` or `master` which represents the primary branch.
2.  The root contains agent metadata folders (e.g., `.agent/`, `.gemini/`) but no direct source code files at the root level.
3.  (Optional) The root contains sibling directories (e.g., `feature-xyz`, `fix-abc`) alongside `main`.

### The Mandate
If this pattern is detected:
-   **YOU MUST** use a git worktree for your current task.
-   **YOU MUST NOT** edit files directly inside the `main` or `master` directory (except for repo-wide maintenance).
-   **YOU MUST NOT** create a subdirectory inside `main` for your work. You must create a *sibling* directory.

## 2. Creating a Worktree

### Step 1: Update the Source of Truth
Before creating a worktree, ensure the local `main` (or `master`) is up to date to avoid divergence.

```bash
# Assuming 'main' is the primary directory
git -C main checkout main
git -C main pull origin main
```

### Step 2: Initialize the Worktree
Create a new folder and branch as a sibling to `main`.

```bash
# Syntax: git -C <base> worktree add ../<folder-name> -b <branch-name>

# Example: Creating a feature branch
git -C main worktree add ../feature-new-login -b feature.new-login
```

**Naming Convention**:
-   Folder name: Kebab-case matching the task (e.g., `feature-new-login`).
-   Branch name: `type.name` (e.g., `feature.new-login`, `fix.crash-handling`).

## 3. Working in the Worktree

### Context Switch
Immediately after creation, you must switch your working directory/context.

```bash
cd ../feature-new-login
```

**Rule**: All subsequent commands (builds, tests, git commits) MUST be run from inside this new worktree directory.

## 4. Cleanup

When the task is completed and verified:

1.  **Remove Worktree**:
    ```bash
    # From the root or main directory
    rm -rf feature-new-login
    git -C main worktree prune
    ```
2.  **Verify**: Ensure the directory is gone and `git -C main worktree list` no longer shows it.

## 5. Troubleshooting

### "Branch is already checked out"
If you see `fatal: 'feature/xyz' is already checked out at...`:
1.  Check `git -C main worktree list`.
2.  If the directory exists, use it.
3.  If the directory is missing but git thinks it exists, run `git -C main worktree prune`.

### "No tracking information"
If `git pull` fails in the new worktree:
```bash
git branch --set-upstream-to=origin/main
```

### "INSTALL_FAILED_VERSION_DOWNGRADE" (Android)
If switching between worktrees causes installation issues:
1.  Uninstall the app: `adb uninstall <package_name>`
2.  Reinstall from the current worktree.

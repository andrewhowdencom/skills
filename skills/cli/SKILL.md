---
name: cli
description: Guidelines for designing and documenting Command Line Interfaces.
---

# CLI

## Design Interface
Design top-level commands that categorize actions, followed by imperative subcommands (similar to `kubectl`):

```bash
./cmd slack post --channel "#foo"
```

## Document CLI
Ensure the CLI is self-documenting:
- **Help Flags**: All commands and subcommands must describe their function via `--help`, ideally with examples.
- **Man Pages**: For complex applications, generate or provide a comprehensive `man` page.

## Common Commands

Applications should have the following commands by default:

### `version`

Shows the version of the built application, based on Go's internal build primitives.

```bash
./cmd version
v0.0.0-20260126085723-f3472ac67d26
```

## Common Flags 

Applications should have the following flags by default:

### --log-level

Modifies the log/slog behavior for the default logger. Accepts the levels the log/slog accepts.

```bash
./cmd --log-level debug          
```


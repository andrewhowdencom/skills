# Agent Instructions

This document provides conditional instructions for agents. Based on the task at hand, please refer to the relevant documentation in the `/docs/development/` directory.

Please be sure to plan so you consume files at the time they're relevant to complete the task (e.g. git just before interacting with the git tool)

## Documentation Structure & Context

The documentation in this repository is located in `/docs/development/` and follows the [Divio documentation system](https://documentation.divio.com/). It is structured into four types:

*   **Tutorials:** Learning-oriented lessons (e.g., "Tutorial: Test-Driven Development").
*   **How-to Guides:** Problem-oriented steps (e.g., "How to Design RPC Interfaces").
*   **Reference:** Information-oriented technical descriptions (e.g., "Reference: Default Timeouts for RPCs").
*   **Explanation:** Understanding-oriented background knowledge (e.g., "Explanation: Hexagonal Architecture").

Files are organized into directories by topic (e.g., `rpc/`, `git/`, `architecture/`). When you are directed to a specific file, **you should also look at other files in the same directory** to gain broader context. Often, related concepts, references, or alternative approaches are documented nearby.

## Conditional Instructions

*   **If your task involves making architectural decisions...**
    *   ...and it's about dependency management, read [How to Manage Dependencies](./docs/development/architecture/how-to-manage-dependencies.md).
    *   ...and it's about application layout for complex systems, read [Explanation: Hexagonal Architecture](./docs/development/architecture/explanation-hexagonal-architecture.md).
*   **If your task involves CI/CD...**
    *   ...read [How to Use GitHub Actions for CI](./docs/development/ci/how-to-use-github-actions.md).
*   **If your task involves a command-line interface (CLI)...**
    *   ...and it's about documentation, read [How to Document a CLI](./docs/development/cli/how-to-document-a-cli.md).
    *   ...and it's about interface design, read [How to Design a CLI Interface](./docs/development/cli/how-to-design-a-cli-interface.md).
*   **If your task involves configuration...**
    *   ...and it's about how the application consumes configuration, read [How to Consume Configuration](./docs/development/configuration/how-to-consume-configuration.md).
    *   ...and it's about where to store configuration files, read [How to Store Configuration Files](./docs/development/configuration/how-to-store-configuration-files.md).
*   **If you are about to commit changes...**
    *   ...read [How to Validate Changes](./docs/development/git/how-to-validate-changes.md).
*   **If your task involves using Git...**
    *   ...and it's about staging changes, read [How to Stage Changes](./docs/development/git/how-to-stage-changes.md).
    *   ...and it's about amending commits, read [How to Amend Commits](./docs/development/git/how-to-amend-commits.md).
    *   ...and it's about writing commit messages, read [How to Write Commit Messages](./docs/development/git/how-to-write-commit-messages.md).
*   **If your task involves running common development tasks...**
    *   ...read [How to Configure Task Runners](./docs/development/task-runner/how-to-configure-task-runners.md).
*   **If your task involves RPCs...**
    *   ...and it's about designing RPC interfaces, read [How to Design RPC Interfaces](./docs/development/rpc/how-to-design-rpc-interfaces.md).
    *   ...and it's about implementing resilient RPCs, read [How to Implement Resilient RPCs](./docs/development/rpc/how-to-implement-resilient-rpcs.md).
    *   ...and it's about default timeouts, read [Reference: Default Timeouts for RPCs](./docs/development/rpc/reference-default-timeouts.md).
*   **If your task involves writing code in a specific language...**
    *   ...and it's Go, read [How to Write Go Code](./docs/development/languages/how-to-write-go-code.md).
    *   ...and it's Markdown, read [How to Write Markdown](./docs/development/languages/how-to-write-markdown.md).
*   **If your task involves writing tests...**
    *   ...follow the [Test-Driven Development Tutorial](./docs/development/tests/tutorial-test-driven-development.md).
*   **If your task involves dependency injection...**
    *   ...read [How to Use Dependency Injection](./docs/development/dependency-injection/how-to-use-dependency-injection.md).
*   **If your task requires specific tools...**
    *   ...read [How to Find Required Tools](./docs/development/tools/how-to-find-required-tools.md).
*   **If your task involves updating documentation...**
    *   ...read [How to Keep Documentation Up-to-Date](./docs/development/documentation/how-to-keep-documentation-up-to-date.md).
*   **If your task involves instrumentation...**
    *   ...read the guidance in [the instrumentation docs](./docs/development/instrumentation/).

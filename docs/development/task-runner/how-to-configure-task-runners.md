# How to Configure Task Runners

For all projects, [Taskfile] is the mandatory task runner. It provides a consistent interface for running common development tasks across different repositories.

Ensure you have it installed by following [How to Find Required Tools](../tools/how-to-find-required-tools.md).

## Standard Tasks

Most projects should implement the following standard tasks in their `Taskfile.yml`. While specific project types (e.g., libraries, containers) may require fewer or more tasks, these represent the baseline expectation:

*   `generate`: Generates code as required (e.g., from Protocol Buffers, mocks).
*   `build`: Compiles the application binary.
*   `setup`: Installs required tools and dependencies (excluding Taskfile and the Go programming language itself).
*   `validate`: Runs lints, unit tests, smoke tests, and the build to ensure the project is in a valid state. This is typically run before committing.
*   `test`: Runs unit tests. See [Test-Driven Development](../tests/tutorial-test-driven-development.md).
*   `lint`: Runs code linters.

[Taskfile]: https://taskfile.dev/

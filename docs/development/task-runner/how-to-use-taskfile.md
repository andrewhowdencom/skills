# How to Use Taskfile

For common tasks within the project (for example, running tests, builds etc) then please use [Taskfile] as the task
runner of choice. Ensure you have it installed by following [How to Find Required Tools](../tools/how-to-find-required-tools.md).

There are usually a few tasks I prefer to create

1. `build`: Runs the compilation of the binary.
2. `test`: Runs all unit tests for the binary. See [Test-Driven Development](../tests/tutorial-test-driven-development.md).
3. `lint`: Runs any lints that exist for the code in the binary.
4. `validate`: A combination of test, lint and other pre-checks that are useful to catch issues prior to commit.

[Taskfile]: https://taskfile.dev/

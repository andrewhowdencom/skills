# How to Validate Changes

Before committing any changes, you must ensure that your work does not break the application and meets all quality standards.

## Using Taskfile

Just prior to commit, you must run the `validate` task using [Taskfile](../task-runner/how-to-configure-task-runners.md):

```bash
task validate
```

This task will run all necessary checks, including lints, unit tests, smoke tests, and the build. Ensure that this command passes successfully before creating your commit.

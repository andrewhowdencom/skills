# How to Design a CLI Interface

Where there is a CLI, there should be top level commands that categorize and then commands that take an imperative
action on the category, similar to how the Kubernetes CLI is structured. For example, a project that sends
announcements might have the structure:

```bash
./announce slack post --channel "#foo"
```

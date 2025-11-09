# How to Amend Commits

Where there are multiple commits required for work, you can either:

* **Create unique, well crafted commit messages for each line**; this is the ideal as it will give context to your
  decisions at multiple stages of this work.
* **Squash all commits together and summarize the work in one commit**; this is also fine as it will create a well
  structured git history.

Just don't create multiple "repeated commits" on a single branch. When you amend the commit, you'll need to
force push it with:

```
git push --force-with-lease
```

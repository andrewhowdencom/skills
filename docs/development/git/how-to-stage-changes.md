# How to Stage Changes

If possible, stage changes by reviewing specific changes applied in files rather than staging files directly. In
practice, use:

```bash
git add --patch ./path/to/file
```

Rather than just `git add ./path to file`.

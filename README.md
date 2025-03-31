# gh-shellcheck

This is just a simple script to run ShellCheck on all shell scripts in a
git repository and print nice annotations on GitHub Actions.

In your workflow, do something like:

```yaml
jobs:
  shellcheck:
    name: Run shellcheck

    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Install itspriddle/gh-shellcheck
        run:
          gh extension install itspriddle/gh-shellcheck

      - name: Run shellcheck
        run: |
          gh shellcheck
```

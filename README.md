# gh-shellcheck

A simple script to run ShellCheck on all shell scripts in a git repository
with support for GitHub Actions annotations.

## Why?

Most projects end up with at least a few shell scripts scattered around. While
ShellCheck is great for checking individual files, it becomes tedious to
manually track all scripts across a project, especially when they don't have
standard extensions or are located in various directories.

You could use `shellcheck '**/*.sh'`, but this doesn't work for scripts
without extensions. Many projects resort to `Makefiles` or `Rakefiles` that
manually track script paths, but this approach is error-prone and requires
constant maintenance.

For GitHub Actions workflows, ShellCheck comes pre-installed and is easy to
set up, but it doesn't support GitHub Actions annotations out of the box.
You're still left with the same problem of tracking script locations.

This script solves these issues by automatically discovering all shell scripts
in a git repository and running ShellCheck on them. It identifies shell
scripts by using `git ls-files` to find `*.sh`, `*.bash`, and `*.bats` files,
then uses `git grep` to find files with Bash/Sh shebangs. This catches scripts
regardless of their file extension or location.

## Installation

Install this extension using the GitHub CLI:

```sh
gh extension install built-fast/gh-shellcheck
```

**Prerequisites:**

- [GitHub CLI](https://cli.github.com/) (`gh`)
- [ShellCheck](https://www.shellcheck.net/)
- [jq](https://jqlang.github.io/jq/) (for JSON formatting)

## Local Usage

Using this script is meant to be done without thinking. Just run `gh
shellcheck` in a git repository.

```sh
gh shellcheck
```

By default, it will output the same format that ShellCheck does, with color
enabled unless you ask it not to or pipe the script to something else.

If you want to see JSON, which ShellCheck supports on it's own, you can use

```sh
gh shellcheck -f json
```

ShellCheck's JSON output isn't pretty so I piped it to `jq` to make it look a
bit nicer.

**Untracked Files**

If you want to scan untracked files, you can use:

```sh
gh shellcheck --untracked
```

**Include/Exclude Paths**

You can specify which paths to include or exclude from checking:

```sh
# Check all files (default)
gh shellcheck

# Check only files in scripts/ directory
gh shellcheck -- scripts/

# Exclude files in tests/ directory
gh shellcheck -- :tests/

# Include scripts/ but exclude scripts/old/
gh shellcheck -- scripts/ :scripts/old/

# Multiple includes and excludes
gh shellcheck -- src/ bin/ :src/vendor/ :bin/legacy/
```

The `--` separator is required when specifying paths. Paths prefixed with `:`
are excluded, while paths without the prefix are included. If you specify
include paths, only files matching those paths will be checked. Exclude paths
are then applied to remove any matching files from the final set.

## CI Usage

In your workflow, do something like:

```yaml
jobs:
  shellcheck:
    name: Run shellcheck

    runs-on: ubuntu-latest

    permissions:
      contents: read

    steps:
      - uses: actions/checkout@v4

      - name: Run shellcheck
        env:
          GH_TOKEN: ${{ github.token }}
        run: |
          gh extension install built-fast/gh-shellcheck
          gh shellcheck
```

## License

MIT License - see [`LICENSE`](./LICENSE) in this repo.

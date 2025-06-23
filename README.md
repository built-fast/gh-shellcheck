# gh-shellcheck

This is just a simple script to run ShellCheck on all shell scripts in a
git repository.

Why?

I end up with at least a few shell scripts in every project I work on. I
always use ShellCheck locally. I use my own [vim-shellcheck][] plugin to run
ShellCheck in Vim, but that is per-file. The scripts end up scattered around,
so I end up having `Makefiles`, or `Rakefiles` that have to manually track all
the scripts, or at least their directories, if I want to check everything with
one command. Sure, you could use `shellcheck '**/*.sh'`, but that doesn't work
if you have scripts without extensions (which tend to be all of mine).

I also want to be able to run ShellCheck on my projects that use GitHub
Actions. It comes pre-installed, and is easy enough to setup. But, it doesn't
support GitHub Actions annotations. And, you are left with the same problem of
tracking the paths to your scripts.

> As I rained blows upon him, I realize there had to be another way.

So, I wrote this script. It is a simple wrapper around ShellCheck that finds
all shell scripts in a git repository and runs ShellCheck on them. It
identifies shell scripts by using `git ls-files` to find `*.sh` files, or
`.bash` files, then using `git grep` to find files with a Bash/Sh shebang. It
identifies Bats files, too.

[vim-shellcheck]: https://github.com/itspriddle/vim-shellcheck

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

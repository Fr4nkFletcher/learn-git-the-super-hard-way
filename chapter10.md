# Basic Concepts

Git provides various commands for batch processing and automation, allowing you to perform repetitive tasks efficiently.

# Running a Command on All Files

- Lv2

```bash
git ls-files | xargs -n 1 your-command
```

# Running a Command on All Commits

- Lv2

```bash
git rev-list --all | xargs -n 1 your-command
```

# Using Hooks for Automation

- Lv2

```bash
# Place your script in .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

# Summary

- Use `git ls-files` and `git rev-list` with `xargs` for batch processing
- Use Git hooks for automation

# Git Batch Processing

Some complex Git operations require the use of xargs. Git also provides some simplified methods.

- `git for-each-ref` - Process each reference (more flexible than `git show-ref`)
- `git filter-branch` - Process each commit (more flexible than `git rebase`)
- `git submodule foreach --recursive` - Process each submodule

# Automated Debugging

- `git bisect` - Use binary search to locate which commit introduced a bug

# Running Specific Scripts in Specific Situations: Hooks

- `vim .git/hooks/pre-commit` - Run checks before committing
- `vim .git/hooks/commit-msg` - Automatically write commit messages
- `vim .git/hooks/pre-push` - Run checks before pushing
- `vim .git/hooks/...`

# Automatically Handle CRLF/LF

- `git config --global core.autocrlf true|false|input`

# Automatically Handle Trailing Whitespace/End-of-File Whitespace

- `git stripspace`
- `git config --global core.whitespace ...`

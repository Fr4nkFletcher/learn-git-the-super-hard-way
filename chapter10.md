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

# Git批处理

一些复杂的Git操作需要利用xargs。而Git提供了一些化简的办法。

- `git for-each-ref` - 对每个引用进行处理（比`git show-ref`更灵活）
- `git filter-branch` - 对每个commit进行处理（比`git rebase`更灵活）
- `git submodule foreach --recursive` - 对每个submodule进行处理

# 自动化debug

- `git bisect` - 二分查找法定位bug位于哪个commit

# 在特定情况下执行特定脚本：hooks

- `vim .git/hooks/pre-commit` - 在commit前做检查
- `vim .git/hooks/commit-msg` - 自动撰写commit message
- `vim .git/hooks/pre-push` - 在push前做检查
- `vim .git/hooks/...`

# 自动处理CRLF/LF

- `git config --global core.autocrlf true|false|input`

# 自动处理行尾/文件末尾空格

- `git stripspace`
- `git config --global core.whitespace ...`

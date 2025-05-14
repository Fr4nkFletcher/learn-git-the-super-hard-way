# Basic Concepts

Git provides various commands to search and view the history of commits, branches, and files.

# Viewing Commit History

- Lv2

```bash
git log
```

# Searching Commit Messages

- Lv2

```bash
git log --grep='search-term'
```

# Viewing File History

- Lv2

```bash
git log -- path/to/file
```

# Viewing Branch History

- Lv2

```bash
git branch --contains <commit>
```

# Summary

- Use `git log` to view commit history
- Use `git log --grep` to search commit messages
- Use `git log -- <file>` to view file history
- Use `git branch --contains` to view which branches contain a commit

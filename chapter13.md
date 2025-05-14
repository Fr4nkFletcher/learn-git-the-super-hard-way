# Basic Concepts

Git supports advanced features such as GPG signing, merge tags, and notes for enhanced security and collaboration.

# GPG Signing Commits

- Lv2

```bash
git commit -S -m "Signed commit message"
```

# Creating a Signed Tag

- Lv2

```bash
git tag -s v1.0 -m "Signed version 1.0"
```

# Verifying a Signed Commit or Tag

- Lv2

```bash
git verify-commit <commit>
git verify-tag <tag>
```

# Using Notes

- Lv2

```bash
git notes add -m "This is a note for the commit"
git notes show <commit>
```

# Summary

- Use GPG signing for secure commits and tags
- Use notes to annotate commits


# Basic Concepts

HEAD is a special reference in Git, usually located at `<repo>/HEAD`. It points to the current branch (or directly to a commit in detached HEAD state).

# Viewing HEAD

- Lv1

```bash
cat HEAD
# ref: refs/heads/master
```

- Lv2

```bash
git symbolic-ref HEAD
# refs/heads/master
```

# Switching HEAD

- Lv1

```bash
echo 'ref: refs/heads/dev' > HEAD
```

- Lv2

```bash
git symbolic-ref HEAD refs/heads/dev
```

- Lv3

```bash
git switch dev
git checkout dev
```

# Detached HEAD

- Lv1

```bash
echo 'ce013625030ba8dba906f756967f9e9ca394464a' > HEAD
```

- Lv2

```bash
git symbolic-ref -d HEAD
echo 'ce013625030ba8dba906f756967f9e9ca394464a' > HEAD
```

- Lv3

```bash
git switch --detach ce013625030ba8dba906f756967f9e9ca394464a
git checkout --detach ce013625030ba8dba906f756967f9e9ca394464a
```

# Summary

- HEAD is a special ref pointing to the current branch or commit
- Use `git symbolic-ref` or `git switch`/`git checkout` to manipulate HEAD

# Basic Knowledge

Every repo must have a HEAD, regardless of whether a worktree is configured.

This chapter does not introduce new Lv2 commands, but expresses commonly used Lv3 commands in Lv2 terms to help readers understand how they work.

# Setting HEAD to Point to a Commit

Note: If `<commit-ish>` is an indirect reference, it will be dereferenced
- Lv2
  - `git update-ref --no-deref HEAD <commit-ish>`
- Lv3
  - `git switch --detach <commit-ish>` - Equivalent to executing the following commands in sequence:
    - `git update-ref --no-deref HEAD <commit-ish>` - Modify HEAD
    - `git read-tree HEAD` - Modify index
    - `git checkout-index -a` - Modify worktree
  - Legacy syntax `git checkout --detach <commit-ish> --`

# Creating a Direct Reference and Setting HEAD to Point to It

- Lv2: Just do it step by step
- Lv3
  - `git switch -c <branch> <commit-ish>` - Equivalent to executing the following commands in sequence:
    - `git update-ref --no-deref refs/heads/<branch> <commit-ish>` - Create direct reference
    - `git symbolic-ref HEAD refs/heads/<branch>` - Modify HEAD
    - `git read-tree HEAD` - Modify index
    - `git checkout-index -a` - Modify worktree
  - Legacy syntax `git checkout -b <branch> <commit-ish> --`

# Setting HEAD to Point to a Direct Reference

- Lv2
  - `git symbolic-ref HEAD <ref>`
- Lv3
  - `git switch <ref>` - Equivalent to executing the following commands in sequence:
    - `git symbolic-ref HEAD <ref>` - Modify HEAD
    - `git reset HEAD -- .` - Modify index
    - `git checkout-index -a` - Modify worktree
  - Legacy syntax `git checkout <ref> --`

# Detailed Explanation of `git checkout`

Note: This command only has legacy syntax, please avoid using it.

You must choose one of the following usages:
- `git checkout -- <path>` - Update worktree based on index, see Chapter 3
  - Please use new syntax: `git restore [--worktree] -- <path>`
- `git checkout [--detach] [<commit-ish>] --` - Modify HEAD, index, worktree, see above (empty `<tree-ish>` means HEAD)
  - Please use new syntax: `git switch <commit-ish>`
- `git checkout <commit-ish> -- <path>` - Update index and worktree based on tree, see Chapter 3
  - Please use new syntax: `git restore --source <commit-ish> --stage --worktree -- <path>`

# Detailed Explanation of `git reset`

You must choose one of the following usages:
- `git reset [<tree-ish>] -- <path>` - Modify index based on `<commit-ish>`, see Chapter 3
  - Please use new syntax: `git restore [--source <commit-ish>] --stage -- <path>`
- `git reset --soft [<commit-ish>] --` - Equivalent to executing the following commands in sequence: (empty `<tree-ish>` means HEAD)
  - `git update-ref HEAD <commit-ish>` - Modify HEAD *or* the reference HEAD points to
- `git reset [--mixed] [<commit-ish>] --` - Equivalent to executing the following commands in sequence: (empty `<tree-ish>` means HEAD)
  - `git update-ref HEAD <commit-ish>` - Modify HEAD *or* the reference HEAD points to
  - `git restore --staged -- :/` - Modify index based on HEAD, see Chapter 3
- `git reset --hard [<commit-ish>] --` - Equivalent to executing the following commands in sequence: (empty `<tree-ish>` means HEAD)
  - `git update-ref HEAD <commit-ish>` - Modify HEAD *or* the reference HEAD points to
  - `git restore --staged --worktree -- :/` - Modify index based on HEAD, see Chapter 3

# Detailed Explanation of `git commit`

Equivalent to executing the following commands in sequence:
- `git write-tree`
- `git commit-tree <new-tree> -p HEAD`
- `git update-ref HEAD <new-commit>` - Modify HEAD *or* the reference HEAD points to

# Notes

- The following commands have the same effect:
  - `git restore --source HEAD --stage --worktree -- :/
  - `git reset --hard [HEAD]`
  - (Legacy syntax) `git checkout -f [HEAD] --`
  - (Legacy syntax) `git checkout -f HEAD -- .`
- The following commands have the same effect, but are completely different from the above commands:
  - `git restore -- :/
  - `git restore --worktree -- :/
  - `git restore --stage --worktree -- :/
  - (Legacy syntax) `git checkout -f -- .`
- The following command has no effect:
  - `git switch <branch>` (assuming HEAD already points to `refs/heads/<branch>`)
- When HEAD is not an indirect reference, the following commands have the same effect:
  - `git reset --hard <commit-ish>`
  - `git switch --detach <commit-ish>`
  - (Legacy syntax) `git checkout -f --detach <commit-ish>`

# Summary

- Lv3
  - `git switch --detach <commit-ish>`
  - `git switch -c <branch> <commit-ish>`
  - `git switch <ref>`
  - `git reset --soft [<commit-ish>] --`
  - `git reset [--mixed] [<commit-ish>] --`
  - `git reset --hard [<commit-ish>] --`


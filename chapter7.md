# Basic Concepts

Rebase in Git is the process of moving or combining a sequence of commits to a new base commit. This is often used to maintain a linear project history.

# Performing a Rebase

- Lv2

```bash
git rebase master
```

# Interactive Rebase

- Lv3

```bash
git rebase -i master
```

# Aborting a Rebase

- Lv2

```bash
git rebase --abort
```

# Skipping a Commit During Rebase

- Lv2

```bash
git rebase --skip
```

# Summary

- Rebase moves or combines commits onto a new base
- Use `git rebase` to perform a rebase
- Use `git rebase -i` for interactive rebasing
- Use `git rebase --abort` to cancel a rebase
- Use `git rebase --skip` to skip a commit during rebase

```bash
git init .
# Initialized empty Git repository in /root/.git/
git config alias.lg "log --graph --pretty=tformat:'%h -%d <%an/%cn> %s' --abbrev-commit"
```

# Copying Logical Commits

Before starting, create a few commits:
```bash
git mktree </dev/null
# 4b825dc642cb6eb9a060e54bf8d69288fbee4904
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

commit A
EOF
# 7b2c8f5d87ac92311b142000cb783ea85d80c3d2
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent 7b2c8f5d87ac92311b142000cb783ea85d80c3d2
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736001 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736001 +0800

commit B
EOF
# cd5b86bab3df3d8556c03d03ecbabf136a5ca1da
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent cd5b86bab3df3d8556c03d03ecbabf136a5ca1da
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736002 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736002 +0800

commit C
EOF
# c6e6deb39bb4022a49e0ca72abe328d5bb9db47d
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent c6e6deb39bb4022a49e0ca72abe328d5bb9db47d
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736003 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736003 +0800

commit D
EOF
# 3aec5e9861752b06a109fbacb25e374d28176ea2
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent cd5b86bab3df3d8556c03d03ecbabf136a5ca1da
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736004 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736004 +0800

commit E
EOF
# 2c353e8bf7f03cce07a8b8d8b7c20cbb9ea3d771
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent 2c353e8bf7f03cce07a8b8d8b7c20cbb9ea3d771
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736005 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736005 +0800

commit F
EOF
# 85706b3115a9ce4fb25bc1e43e732515d33bb31f
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent 2c353e8bf7f03cce07a8b8d8b7c20cbb9ea3d771
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736006 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736006 +0800

commit G
EOF
# 31155de821fe4a68c23714dc16d54d5bd865cb9a
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent c6e6deb39bb4022a49e0ca72abe328d5bb9db47d
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736007 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736007 +0800

commit H
EOF
# f071f8ea1168fa5f7ab26295836e425b6e8d90b9
git update-ref --no-deref refs/heads/br1 f071f8ea
git update-ref --no-deref refs/heads/br2 3aec5e98
git update-ref --no-deref refs/heads/br3 85706b31
git update-ref --no-deref refs/heads/br4 31155de8
git symbolic-ref HEAD refs/heads/br1
git lg br1 br2 br3 br4
# * f071f8e - (HEAD -> br1) <b1f6c1c4/b1f6c1c4> commit H
# | * 31155de - (br4) <b1f6c1c4/b1f6c1c4> commit G
# | | * 85706b3 - (br3) <b1f6c1c4/b1f6c1c4> commit F
# | |/  
# | * 2c353e8 - <b1f6c1c4/b1f6c1c4> commit E
# | | * 3aec5e9 - (br2) <b1f6c1c4/b1f6c1c4> commit D
# | |/  
# |/|   
# * | c6e6deb - <b1f6c1c4/b1f6c1c4> commit C
# |/  
# * cd5b86b - <b1f6c1c4/b1f6c1c4> commit B
# * 7b2c8f5 - <b1f6c1c4/b1f6c1c4> commit A
```

# Logical Revert Commit

Logical revert means adding a reverse change.
You can absolutely add a revert for a commit that is not on the current branch.

- Lv1

```bash
(git update-ref --no-deref refs/heads/br1 f071f8ea)
git reset --hard
# HEAD is now at f071f8e commit H
git read-tree -m cd5b cd5b^
git cat-file commit cd5b
# tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
# parent 7b2c8f5d87ac92311b142000cb783ea85d80c3d2
# author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736001 +0800
# committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736001 +0800
#
# commit B
git hash-object -t commit --stdin -w <<EOF
tree $(git write-tree)
parent $(git rev-parse HEAD)
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736001 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736060 +0800

Revert "commit B"

This reverts commit cd5b86bab3df3d8556c03d03ecbabf136a5ca1da.
EOF
# 25911f90caf4b0081d23b3b0ba7e4a66fa61b349
git reset --soft 25911f90caf4b0081d23b3b0ba7e4a66fa61b349
git lg
# * 25911f9 - (HEAD -> br1) <b1f6c1c4/b1f6c1c4> Revert "commit B"
# * f071f8e - <b1f6c1c4/b1f6c1c4> commit H
# * c6e6deb - <b1f6c1c4/b1f6c1c4> commit C
# * cd5b86b - <b1f6c1c4/b1f6c1c4> commit B
# * 7b2c8f5 - <b1f6c1c4/b1f6c1c4> commit A
(git reset --soft HEAD~)
```

- Lv3

`git revert`: Performs the following operations on several commits in sequence:
* `git read-tree -m <commit> <commit>^` (Note: The direction is reversed here)
* `git commit-tree $(git write-tree) -p HEAD`
  * Uses the original author's information
* `git reset --soft <new-commit>`

Unfortunately, `git revert` does not support `--allow-empty`, so it is not demonstrated here.

# Detailed Explanation of `git rebase`

## Basic Semantics

To make large-scale commit copying easier, `git rebase` was created.

## Handling of merge commits

`git rebase` has four modes for handling merges.
Among them, `--preserve-merge` is buggy and is not recommended in any situation.

Below, the semantics of the other three handling modes are discussed for the three types of rebase.

# Summary

- Lv2
  - `git cherry-pick --keep-redundant-commits <commit-ish>...`
- Lv3
  - `git revert <commit-ish>...`
  - `git rebase --keep-empty [-i] [--onto <newbase>] [(<upstream>|--root) [<branch>]]`
    - `[--no-ff]`
    - `[--rebase-merges[=[no-]rebase-cousins]]`

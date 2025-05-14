# Basic Concepts

A merge in Git is the process of integrating changes from different branches. Merges can be performed manually or using Git commands.

# Performing a Merge

- Lv2

```bash
git merge feature-branch
```

# Viewing Merge Conflicts

- Lv2

```bash
git status
```

# Resolving Merge Conflicts

- Lv2

```bash
git add conflicted-file
```

# Completing the Merge

- Lv2

```bash
git commit
```

# Summary

- Merges integrate changes from different branches
- Use `git merge` to perform a merge
- Use `git status` to view conflicts
- Use `git add` to resolve conflicts
- Use `git commit` to complete the merge

```bash
git init --separate-git-dir "$(pwd)" ../default-tree
# Initialized empty Git repository in /root/
```

# Viewing Changes

Before starting, create a few objects:
```bash
echo '1' | git hash-object -t blob --stdin -w
# d00491fd7e5bb6fa28c517a0bb32b8b506539d4d
echo '2' | git hash-object -t blob --stdin -w
# 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f
echo '3' | git hash-object -t blob --stdin -w
# 00750edc07d6415dcc07ae0351e9397b0222b7ba
echo '4' | git hash-object -t blob --stdin -w
# b8626c4cff2849624fb67f87cd0ad72b163671ad
echo '5' | git hash-object -t blob --stdin -w
# 7ed6ff82de6bcc2a78243fc9c54d3ef5ac14da69
echo '6' | git hash-object -t blob --stdin -w
# 1e8b314962144c26d5e0e50fd29d2ca327864913
echo '7' | git hash-object -t blob --stdin -w
# 7f8f011eb73d6043d2e6db9d2c101195ae2801f2
git mktree <<EOF
100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d$(printf '\t')1.txt
100755 blob 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f$(printf '\t')2.txt
EOF
# a237e8338c09e7d1b2f9749f73f4f583f19fc626
git mktree <<EOF
100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d$(printf '\t')1.txt
100755 blob 00750edc07d6415dcc07ae0351e9397b0222b7ba$(printf '\t')3.txt
EOF
# aa250e2798646facc12686e4403ccadbf1565d51
git hash-object -t commit --stdin -w <<EOF
tree a237e8338c09e7d1b2f9749f73f4f583f19fc626
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

1=1 2=2
EOF
# 4cfe841426d0435270d049625a766130c108f4c8
git hash-object -t commit --stdin -w <<EOF
tree aa250e2798646facc12686e4403ccadbf1565d51
parent 4cfe841426d0435270d049625a766130c108f4c8
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

1=1 3=3
EOF
# afc38c96c82ea65991322a5d28995b0851ff7edd
```

Based on the first tree-ish, view the changes in the second tree-ish:
```bash
git diff-tree a237 aa25
# :100755 000000 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 0000000000000000000000000000000000000000 D	2.txt
# :000000 100755 0000000000000000000000000000000000000000 00750edc07d6415dcc07ae0351e9397b0222b7ba A	3.txt
git diff-tree a237 aa25 -- 2.txt
# :100755 000000 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 0000000000000000000000000000000000000000 D	2.txt
git diff-tree afc3
# afc38c96c82ea65991322a5d28995b0851ff7edd
# :100755 000000 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 0000000000000000000000000000000000000000 D	2.txt
# :000000 100755 0000000000000000000000000000000000000000 00750edc07d6415dcc07ae0351e9397b0222b7ba A	3.txt
```

Based on tree-ish, view changes in the index:
```bash
git read-tree a237
git diff-index --cached aa25
# :000000 100755 0000000000000000000000000000000000000000 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f A	2.txt
# :100755 000000 00750edc07d6415dcc07ae0351e9397b0222b7ba 0000000000000000000000000000000000000000 D	3.txt
```

Based on tree-ish, view changes in the worktree:
```bash
rm -rf ../default-tree/*
git --work-tree=../default-tree diff-index aa25
# :100644 000000 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0000000000000000000000000000000000000000 D	1.txt
# :100755 000000 00750edc07d6415dcc07ae0351e9397b0222b7ba 0000000000000000000000000000000000000000 D	3.txt
```

Based on the index, view changes in the worktree:
```bash
git --work-tree=../default-tree diff-files
# :100644 000000 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0000000000000000000000000000000000000000 D	1.txt
# :100755 000000 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 0000000000000000000000000000000000000000 D	2.txt
```

- Lv3

* `git diff <tree-ish> <tree-ish> -- [<path>]` is equivalent to `git diff-tree -p <tree-ish> <tree-ish> -- [path]`
* `git show <commit-ish> -- [<path>]` is equivalent to `git diff-tree -p <commit-ish> -- [<path>]`
* `git diff [<tree-ish>] -- [<path>]` is equivalent to `git diff-index -p [<tree-ish>] -- [<path>]`
* `git diff --cached [<tree-ish>] -- [<path>]` is equivalent to `git diff-index -p --cached [<tree-ish>] -- [<path>]`
* `git diff -- <path>` is equivalent to `git diff-files -p <path>`
* `git status` is equivalent to `git diff-index HEAD`, `git diff-index --cached HEAD`, `git clean -nd`
    * Note: There is no `-p` here, which means it only lists which files have changed, but does not show the specific differences
    * `git clean -nd` also lists which new files have been added

- Lv4: `git st`

`git status -sb` is more concise than `git status`.

# Handling Changes

Similar to how `git bundle create` packages objects into a byte stream for offline transfer, `git diff-* -p|--patch` packages changes into a byte stream for offline transfer.
Similar to how `git bundle unbundle` unpacks a byte stream into objects, `git apply` unpacks a byte stream into changes.
Note: `--patch` produces human-readable data, but `git bundle` is machine-readable only.

Package changes:
```bash
git diff-tree --patch a237 aa25 | tee ../the.patch
# diff --git a/2.txt b/2.txt
# deleted file mode 100755
# index 0cfbf08..0000000
# --- a/2.txt
# +++ /dev/null
# @@ -1 +0,0 @@
# -2
# diff --git a/3.txt b/3.txt
# new file mode 100755
# index 0000000..00750ed
# --- /dev/null
# +++ b/3.txt
# @@ -0,0 +1 @@
# +3
```

Unpack changes to the worktree:
```bash
rm -rf ../default-tree/*
git read-tree a237
git --work-tree=../default-tree checkout-index -fu -a
ls ../default-tree
# 1.txt
# 2.txt
git -C ../default-tree apply ../the.patch
ls ../default-tree
# 1.txt
# 3.txt
git ls-files -s
# 100644 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0	1.txt
# 100755 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 0	2.txt
```

Unpack changes to the index:
```bash
git ls-files -s
# 100644 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0	1.txt
# 100755 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 0	2.txt
git apply --cached ../the.patch
git ls-files -s
# 100644 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0	1.txt
# 100755 00750edc07d6415dcc07ae0351e9397b0222b7ba 0	3.txt
ls ../default-tree
# 1.txt
# 3.txt
```

# Introduction to Merge-Related Concepts

Merge is the most complex and important part of git.
Before explaining each command in detail, let's look at the Lv2 big picture:

- For the same file, given the original version, how do you integrate changes made independently by two people?
  - If a conflict occurs, use our version: `git merge-file --ours C A B`
  - If a conflict occurs, use their version: `git merge-file --theirs C A B`
  - If a conflict occurs, concatenate both: `git merge-file --union C A B`
  - If a conflict occurs, mark it and resolve manually: `git merge-file C A B`
  - Use other tools: (omitted)
- For the same tree, given the original version, how do you integrate changes at the file level (not file content)?
  - `git merge-tree C A B`
- For the same tree, given the original version, after the other side has made changes, how do you base your unfinished changes on theirs?
  - `git read-tree -m H M`
- For the same tree, given the original version, how do you integrate changes made independently by two people?
  - First resolve the simplest conflicts, keep the full information for complex conflicts: `git read-tree -m A C B`
  - Try to resolve the simplest types of conflicts: `git merge-index git-merge-one-file`
- For the same tree, given the original version, how do you integrate changes made independently by multiple people?
  - Run `git read-tree -m A C B` multiple times

# 3-Way Merge File-Level: `git merge-file`

Important Lv2 tool `git merge-file` (does not require git dir or worktree):
```bash
cat >fileA <<EOF
lineB
...some stuff...
lineC
EOF
cat >fileB <<EOF
lineBB
...some stuff...
lineC
EOF
cat >fileC <<EOF
lineB
...some stuff...
lineCC
EOF
cat >fileD <<EOF
lineBD
...some stuff...
lineC
EOF
# Calculate fileC + (fileB - fileA)
git merge-file --stdout fileC fileA fileB
# lineBB
# ...some stuff...
# lineCC
```

When a conflict occurs, mark it for manual resolution:
```bash
git merge-file --stdout fileD fileA fileB
# <<<<<<< fileD
# lineBD
# =======
# lineBB
# >>>>>>> fileB
# ...some stuff...
# lineC
git merge-file --stdout --diff3 fileD fileA fileB
# <<<<<<< fileD
# lineBD
# ||||||| fileA
# lineB
# =======
# lineBB
# >>>>>>> fileB
# ...some stuff...
# lineC
```

When a conflict occurs, resolve automatically:
```bash
git merge-file --stdout --our fileD fileA fileB
# lineBD
# ...some stuff...
# lineC
git merge-file --stdout --their fileD fileA fileB
# lineBB
# ...some stuff...
# lineC
git merge-file --stdout --union fileD fileA fileB
# lineBD
# lineBB
# ...some stuff...
# lineC
```

## Tree-Level: `git merge-tree`

Before starting, create a few objects:
```bash
git mktree <<EOF
100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d$(printf '\t')1.txt
100755 blob 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f$(printf '\t')2.txt
100755 blob b8626c4cff2849624fb67f87cd0ad72b163671ad$(printf '\t')4.txt
EOF
# 5de99716b8dd347ce09718e5f628b8c78e656b8c
git hash-object -t commit --stdin -w <<EOF
tree 5de99716b8dd347ce09718e5f628b8c78e656b8c
parent 4cfe841426d0435270d049625a766130c108f4c8
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

1=1 2=2 4=4
EOF
# d2b78d09d49d1f9b4ae260cf98657de9a2fedaa6
```

The meaning of `git merge-tree C A B` is `(C+(B-A))-C`, see the following example
```bash
git ls-tree a237
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f	2.txt
git ls-tree aa25
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob 00750edc07d6415dcc07ae0351e9397b0222b7ba	3.txt
git ls-tree 5de9
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f	2.txt
# 100755 blob b8626c4cff2849624fb67f87cd0ad72b163671ad	4.txt
git merge-tree 5de9 a237 aa25
# removed in remote
#   base   100755 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 2.txt
#   our    100755 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 2.txt
# @@ -1 +0,0 @@
# -2
# added in remote
#   their  100755 00750edc07d6415dcc07ae0351e9397b0222b7ba 3.txt
# @@ -0,0 +1 @@
# +3
git merge-tree 5de9 aa25 a237
git merge-tree a237 aa25 5de9
# added in remote
#   their  100755 b8626c4cff2849624fb67f87cd0ad72b163671ad 4.txt
# @@ -0,0 +1 @@
# +4
```

When there are both tree-level and file-level changes, see the following example
```bash
# Note: 3.txt contains "4"
git mktree <<EOF
100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d$(printf '\t')1.txt
100755 blob b8626c4cff2849624fb67f87cd0ad72b163671ad$(printf '\t')3.txt
EOF
# 47e3b7857c03c35eae515b36fe3828ef073cc2aa
# merge-tree produces non-standard output (removed in local), indicating a conflict needs to be resolved
git merge-tree 47e3 a237 aa25
# removed in local
#   base   100755 b8626c4cff2849624fb67f87cd0ad72b163671ad 3.txt
#   their  100755 00750edc07d6415dcc07ae0351e9397b0222b7ba 3.txt
```

# Two Tree Merge with `git read-tree -m`

`git read-tree -m <tree-ish-H> <tree-ish-M>` tries to execute `index=index+(M-H)`.
For each file, considering the states in H, M, index, and worktree, there are 22 specific cases. The handling methods are as follows (0 means does not exist, different lowercase letters mean different versions) (from `man git-read-tree`):

| No. | worktree | index | H | M | Action |
| --- | --- | --- | --- | --- | --- |
| 0 | . | 0 | 0 | 0 | 0 | keep |
| 1 | . | 0 | 0 | m | index = m |
| 2 | . | 0 | h | 0 | index = 0 |
| 3a | . | 0 | h | h | *SEE NOTE* |
| 3b | . | 0 | h | m | fail |
| 4 | i | i | 0 | 0 | keep |
| 5 | w | i | 0 | 0 | keep |
| 6 | m | m | 0 | m | keep |
| 7 | w | m | 0 | m | keep |
| 8 | i | i | 0 | m | fail |
| 9 | w | i | 0 | m | fail |
| 10 | h | h | h | 0 | index = 0 |
| 11 | w | h | h | 0 | fail |
| 12 | i | i | h | 0 | fail |
| 13a | h | i | h | 0 | fail |
| 13b | w | i | h | 0 | fail |
| 14 | h | h | h | h | keep |
| 15 | w | h | h | h | keep |
| 16 | i | i | h | m | fail |
| 17a | h | i | h | m | fail |
| 17b | m | i | h | m | fail |
| 17c | w | i | h | m | fail |
| 18 | m | m | h | m | keep |
| 19a | h | m | h | m | keep |
| 19b | w | m | h | m | keep |
| 20 | h | h | h | m | index = m |
| 21a | m | h | h | m | fail |
| 21b | w | h | h | m | fail |

For 3a, if the index is empty, then execute index=m; if the index is not empty, then keep.

For example:
```bash
rm -rf ../default-tree/*
git read-tree 5de9
# Add -u here to ensure index and worktree are consistent in stat sense
git --work-tree=../default-tree checkout-index -fu -a
git ls-files -s
# 100644 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0	1.txt
# 100755 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 0	2.txt
# 100755 b8626c4cff2849624fb67f87cd0ad72b163671ad 0	4.txt
ls ../default-tree
# 1.txt
# 2.txt
# 4.txt
git ls-tree a237
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f	2.txt
git ls-tree aa25
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob 00750edc07d6415dcc07ae0351e9397b0222b7ba	3.txt
git --work-tree=../default-tree read-tree -m a237 aa25
git ls-files -s
# 100644 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0	1.txt
# 100755 00750edc07d6415dcc07ae0351e9397b0222b7ba 0	3.txt
# 100755 b8626c4cff2849624fb67f87cd0ad72b163671ad 0	4.txt
ls ../default-tree
# 1.txt
# 2.txt
# 4.txt
# Summary:
# 1.txt hhhh -> keep (#14)
# 2.txt hhh0 -> index = 0 (#10)
# 3.txt 000m -> index = m (#1)
# 4.txt ii00 -> keep (#4)
```

# 3-Way Merge with `git read-tree -m`

The meaning of `git read-tree -m [--aggressive] <tree-ish-A> <tree-ish-C> <tree-ish-B>` is:

* Clear the index
* Stage 1: read `<tree-ish-A>` into the index
* Stage 2: read `<tree-ish-C>` into the index
* Stage 3: read `<tree-ish-B>` into the index
* According to the table below (use means delete stages 1/2/3 and create a new stage 0 entry), if it cannot be transformed, do nothing

| `--aggressive` | stage1 | stage2 | stage3 | Action |
| --- | --- | --- | --- | --- |
| any | a | a | a | use a |
| any | a | x | x | use x |
| any | a | a | b | use b |
| any | a | c | a | use c |
| required | a | 0 | a | use 0 |
| required | a | a | 0 | use 0 |
| required | a | 0 | 0 | use 0 |
| required | 0 | x | x | use x |

For example:
```bash
git ls-tree a237
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f	2.txt
git ls-tree 47e3
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob b8626c4cff2849624fb67f87cd0ad72b163671ad	3.txt
git ls-tree aa25
# 100644 blob d00491fd7e5bb6fa28c517a0bb32b8b506539d4d	1.txt
# 100755 blob 00750edc07d6415dcc07ae0351e9397b0222b7ba	3.txt
rm -rf ../default-tree/*
git read-tree 47e3
git --work-tree=../default-tree checkout-index -fu -a
git --work-tree=../default-tree read-tree -m a237 47e3 aa25
# At this point, you can see non-zero stage numbers
git ls-files -s
# 100644 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0	1.txt
# 100755 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f 1	2.txt
# 100755 b8626c4cff2849624fb67f87cd0ad72b163671ad 2	3.txt
# 100755 00750edc07d6415dcc07ae0351e9397b0222b7ba 3	3.txt
# Summary:
# 1.txt aaa, collapse
# 2.txt a00, non-collapse
# 3.txt 0cb, non-collapse
# At this point, write-tree will fail:
git write-tree
# 2.txt: unmerged (0cfbf08886fca9a91cb753ec8734c84fcbe52c9f)
# 3.txt: unmerged (b8626c4cff2849624fb67f87cd0ad72b163671ad)
# 3.txt: unmerged (00750edc07d6415dcc07ae0351e9397b0222b7ba)
# fatal: git-write-tree: error building trees
git --work-tree=../default-tree checkout-index --stage=all -f --all
# .merge_file_bL417W . .	2.txt
# . .merge_file_m91Yo2 .merge_file_FwwWF7	3.txt
```

# `git merge-index` and `git merge-one-file`

The previous `git read-tree` only resolves the simplest conflicts. To resolve more conflicts, either manually edit and then `git update-index` or `git add`, or use automation tools.

`git merge-index` is responsible for calling other tools:
```bash
# Note: 3.txt contains "4"
git merge-index echo -a
# 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f   2.txt 100755  
#  b8626c4cff2849624fb67f87cd0ad72b163671ad 00750edc07d6415dcc07ae0351e9397b0222b7ba 3.txt  100755 100755
```

One (not very useful) operation is to call the built-in tool `git-merge-one-file`:
```bash
rm -rf ../default-tree/*
git read-tree 47e3
git --work-tree=../default-tree checkout-index -fu -a
git --work-tree=../default-tree read-tree -m --aggressive a237 47e3 aa25
git ls-files -s
# 100644 d00491fd7e5bb6fa28c517a0bb32b8b506539d4d 0	1.txt
# 100755 b8626c4cff2849624fb67f87cd0ad72b163671ad 2	3.txt
# 100755 00750edc07d6415dcc07ae0351e9397b0222b7ba 3	3.txt
# This built-in tool is very weak, can't solve many problems, honestly not much better than --aggressive
git --work-tree=../default-tree merge-index git-merge-one-file -a
# fatal: unable to read blob object e69de29bb2d1d6434b8b29ae775ad8c2e48c5391
# error: Could not stat : No such file or directory
# ERROR: content conflict in 3.txt
# fatal: merge program failed
# Added 3.txt in both, but differently.
```

Note: If you see the error
`fatal: unable to read blob object e69de29bb2d1d6434b8b29ae775ad8c2e48c5391`
then run
`printf '' | git hash-object --stdin -w`

* Q: Is there a better way to resolve conflicts when using external tools?
* A: Yes, the famous recursive merge. However, since the algorithm is very complex, there is no Lv2 command. The Lv1 implementation is not introduced here, only the Lv3 usage is shown.

# Choosing the Original Version: `git merge-base`

To reduce the effort of finding the original version (A), `git merge-base -a <commit>*` can directly calculate the nearest common ancestor of multiple commits (in a partial order there is no "most", only "nearest").

```bash
git merge-base -a afc3 d2b7
# 4cfe841426d0435270d049625a766130c108f4c8
```

# Lv3

`git merge -s <strategy>` (except for recursive, subtree, ours) is essentially calling `git merge-<strategy>`.

The so-called subtree merge is essentially a non-empty subtree shift recursive merge.
Git will automatically calculate the prefix to add to B, but you can also specify it manually with `-Xsubtree=`.

`git merge -s resolve --no-ff --no-commit` is essentially
(see [`git-merge-resolve` source code](https://github.com/git/git/blob/master/git-merge-resolve.sh))
- (You need to specify C manually)
- First run `git read-tree -u -m --aggressive A C B`
- Then run `git merge-index -o git-merge-one-file -a`
- In the git dir, generate the files `MERGE_HEAD`, `MERGE_MODE`, `MERGE_MSG`

`git merge -s octopus --no-ff --no-commit` is essentially
(see [`git-merge-octopus` source code](https://github.com/git/git/blob/master/git-merge-octopus.sh))
- Use `git merge-base` to calculate C
- First run `git read-tree -u -m --aggressive A C B`
- Then run `git merge-index -o git-merge-one-file -a`
- Repeat the above process for other branches
- In the git dir, generate the files `MERGE_HEAD`, `MERGE_MODE`, `MERGE_MSG`

`git merge --no-ff --no-commit - ours` is essentially
- For the index, do nothing
- In the git dir, generate the files `MERGE_HEAD`, `MERGE_MODE`, `MERGE_MSG`

The basic idea of `git merge --no-ff --no-commit -s recursive` is
(see `man git-merge`)
- First use `git merge-base --all` to find all possible C
- Merge these C to get C'
- Consider renames, then do a 3-way merge of A, C', B
For recursive, there are many parameters you can adjust, such as
- `-Xours` is similar to `git merge-file --ours`
- `-Xtheirs` is similar to `git merge-file -theirs`
- `-Xsubtree` specifies what prefix to add to B before doing the recursive merge

If `--ff` is present: if A=C or A=B, do nothing.

If `--no-commit` is present: if there are no conflicts, automatically run `git commit`

Note: When the files `MERGE_HEAD` etc. exist, `git commit` will automatically add several `-p` parameters when calling `git commit-tree`, generating a commit with multiple parents. The first parent is HEAD, the rest are the contents of `MERGE_HEAD`, i.e., the arguments to `git merge`.

# Lv4

Since we often want to control whether to create a merge commit (i.e., manually decide `--ff-only` or `--no-ff`), and want to check the merge before committing (e.g., run unit tests), it is recommended to use the following aliases:
```
alias.mf=merge --ff-only
alias.mnf=merge --no-ff
alias.mnfnc=merge --no-ff --no-commit
```

Here, `git mf` is used after `git fetch`, `git mnf` is used for daily merges of other simple branches, and `git mnfnc` is used to try merging (i.e., `git read-tree -u -m`), run unit tests, and then commit.

# Lv5

There is a type of merge situation where you need to *completely replace* a directory in the current branch with that from another branch. (Chapter 12 is based entirely on this.)
However, even `git merge --no-ff -s subtree -Xsubtree=<prefix>` sometimes fails (after all, it's `git read-tree -m`).

Use the following script to solve it:
```sh
git-mnfss() {
  git rm --cached -r -- $1
  git read-tree --prefix $1/ $1
  git checkout-index -fua
  git clean -f -- $1
  git reset --soft $(echo "Merge branch $1" | git commit-tree $(git write-tree) -p HEAD -p $1)
}
```

# On the Completeness of Merge Information

When running `git merge --no-ff B*`, the new commit includes multiple parents to record which commits were merged.
This is so you can trace the details of the merge later.
However, due to the format of parent, only which commits were merged can be recorded; other information related to the commit cannot be recorded:
Notes attached to the commit, tags pointing to the commit, and refs the commit belongs to.

* For notes, you can ignore them, as the information is almost always for local use
* For tags, store them in the message body of the new commit
* For signed tags (see Chapter 13), store them in the mergetag of the new commit
* For refs, store them in the message (Merge xxx branch)

First create several empty commits and tags:
```bash
printf '' | git mktree
# 4b825dc642cb6eb9a060e54bf8d69288fbee4904
git hash-object -t commit --stdin -w <<EOF
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

A
EOF
# 6784b23b1a03700628d8adb65b57b5b4816caa01
echo $'100644 blob 0cfbf08886fca9a91cb753ec8734c84fcbe52c9f\tB.txt' | git mktree
# 6f707a369aa805c91f6d737ccda037d137d0ef93
git hash-object -t commit --stdin -w <<EOF
tree 6f707a369aa805c91f6d737ccda037d137d0ef93
parent 6784b23b1a03700628d8adb65b57b5b4816caa01
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

B
EOF
# f1d113e4db427a1824524d17928a2cb53cd5090a
echo $'100644 blob 00750edc07d6415dcc07ae0351e9397b0222b7ba\tC.txt' | git mktree
# f6ab164f6371575aff1ce5c21441835e5568b182
git hash-object -t commit --stdin -w <<EOF
tree f6ab164f6371575aff1ce5c21441835e5568b182
parent 6784b23b1a03700628d8adb65b57b5b4816caa01
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

C
EOF
# 28c0a4a3bab80a464dd384cf4e3d2b83cceb602b
git branch br-C 28c0
echo $'100644 blob b8626c4cff2849624fb67f87cd0ad72b163671ad\tD.txt' | git mktree
# 2cff17f83cfc5f0cd3af03f99257e35906e6fb7c
git hash-object -t commit --stdin -w <<EOF
tree 2cff17f83cfc5f0cd3af03f99257e35906e6fb7c
parent 6784b23b1a03700628d8adb65b57b5b4816caa01
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

D
EOF
# 7f24235935c56e397d2d1d55bb470fe1b01b8209
git tag tag-D 7f24
echo $'100644 blob 7ed6ff82de6bcc2a78243fc9c54d3ef5ac14da69\tE.txt' | git mktree
# 4ff6131a9638bb0a38917c72255c3c0635d0f464
git hash-object -t commit --stdin -w <<EOF
tree 4ff6131a9638bb0a38917c72255c3c0635d0f464
parent 6784b23b1a03700628d8adb65b57b5b4816caa01
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

E
EOF
# 1a1640224e55b3a7d05108c6b91e03e6cc65ffbe
GIT_COMMITTER_NAME=b1f6c1c4 \
GIT_COMMITTER_EMAIL=b1f6c1c4@gmail.com \
GIT_COMMITTER_DATE='1600000000 +0800' \
git tag -a -m 'Tag for E' tag-obj-E 1a16
```

Then perform merge:
```bash
git update-ref --no-deref HEAD 6784
git -C ../default-tree reset --hard
# HEAD is now at 6784b23 A
git -C ../default-tree clean -fdx
# Removing .merge_file_FwwWF7
# Removing .merge_file_bL417W
# Removing .merge_file_m91Yo2
GIT_AUTHOR_NAME=b1f6c1c4 \
GIT_AUTHOR_EMAIL=b1f6c1c4@gmail.com \
GIT_AUTHOR_DATE='1600000000 +0800' \
GIT_COMMITTER_NAME=b1f6c1c4 \
GIT_COMMITTER_EMAIL=b1f6c1c4@gmail.com \
GIT_COMMITTER_DATE='1600000000 +0800' \
git -C ../default-tree merge --no-ff tag-obj-E f1d1 br-C tag-D
# Fast-forwarding to: tag-obj-E
# Trying simple merge with f1d1
# Trying simple merge with br-C
# Trying simple merge with tag-D
# Merge made by the 'octopus' strategy.
#  B.txt | 1 +
#  C.txt | 1 +
#  D.txt | 1 +
#  E.txt | 1 +
#  4 files changed, 4 insertions(+)
#  create mode 100644 B.txt
#  create mode 100644 C.txt
#  create mode 100644 D.txt
#  create mode 100644 E.txt
git cat-file commit HEAD
# tree ae618f9e9f1a0ce0fdc25f7e4dcfdc5bc9c09c49
# parent 6784b23b1a03700628d8adb65b57b5b4816caa01
# parent 1a1640224e55b3a7d05108c6b91e03e6cc65ffbe
# parent f1d113e4db427a1824524d17928a2cb53cd5090a
# parent 28c0a4a3bab80a464dd384cf4e3d2b83cceb602b
# parent 7f24235935c56e397d2d1d55bb470fe1b01b8209
# author b1f6c1c4 <b1f6c1c4@gmail.com> 1600000000 +0800
# committer b1f6c1c4 <b1f6c1c4@gmail.com> 1600000000 +0800
#
# Merge branch 'br-C', tags 'tag-obj-E' and 'tag-D'; commit 'f1d1' into HEAD
#
# Tag for E
```
Note the different descriptions of each parent in the commit message.
Also, the commit message contains the tag message.

# Summary

- View and handle changes
  - Lv2
    - `git diff-tree [-p] <tree-ish> <tree-ish> -- <path>`
    - `git diff-tree [-p] <commit-ish> -- <path>`
    - `git diff-index [-p] [--cached] <tree-ish> -- <path>`
    - `git diff-files [-p] <path>`
    - `git apply [--cached] <patch> -- <path>`
  - Lv3
    - `git diff <tree-ish> <tree-ish> -- <path>`
    - `git show <commit-ish> -- <path>`
    - `git diff [--cached] <tree-ish> -- <path>`
    - `git diff -- <path>`
    - `git status`
  - Lv4
    - `git st`
- Merge changes
  - Lv2
    - `git merge-file [--ours|--theirs|--union] C A B`
    - `git merge-tree C A B`
    - `git read-tree -m H M`
    - `git read-tree -m A C B`
    - `git merge-index -o git-merge-one-file -a`
  - Lv3
    - `git merge -s resolve [--no-ff] [--no-commit] B`
    - `git merge -s octopus [--no-ff] [--no-commit] B*`
    - `git merge -s ours [--no-ff] [--no-commit] B*`
    - `git merge -s recursive [--no-ff] [--no-commit] B`
    - `git merge -s subtree [--no-ff] [--no-commit] B`
  - Lv4
    - `git mf`
    - `git mnf`
    - `git mnfnc`
  - Lv5
    - `git-mnfss`


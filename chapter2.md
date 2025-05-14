# Basic Concepts

References (refs) are stored in `<repo>/refs/` and are divided into three categories:
- heads: branch references, stored in `<repo>/refs/heads/`
- tags: tag references, stored in `<repo>/refs/tags/`
- remotes: remote-tracking branch references, stored in `<repo>/refs/remotes/`

A ref is essentially a file containing a SHA1 value (or another ref, see symbolic refs below).

There are also special refs:
- HEAD: points to the current branch (or commit, in detached HEAD state)
- FETCH_HEAD: records the branch fetched from a remote
- ORIG_HEAD: records the previous HEAD before a dangerous operation
- MERGE_HEAD: records the commit(s) being merged
- CHERRY_PICK_HEAD: records the commit being cherry-picked
- REBASE_HEAD: records the commit being rebased

Symbolic refs are refs that point to other refs, not directly to a SHA1. For example, HEAD is usually a symbolic ref pointing to a branch in refs/heads/.

# Creating a ref

- Lv1

```bash
echo 'ce013625030ba8dba906f756967f9e9ca394464a' > refs/heads/master
```

- Lv2

```bash
git update-ref refs/heads/master ce013625030ba8dba906f756967f9e9ca394464a
```

# Viewing a ref

- Lv1

```bash
cat refs/heads/master
# ce013625030ba8dba906f756967f9e9ca394464a
```

- Lv2

```bash
git show-ref
# ce013625030ba8dba906f756967f9e9ca394464a refs/heads/master
```

# Symbolic refs

- Lv1

```bash
echo 'ref: refs/heads/master' > HEAD
```

- Lv2

```bash
git symbolic-ref HEAD refs/heads/master
git symbolic-ref HEAD
# refs/heads/master
```

# Deleting a ref

- Lv1

```bash
rm refs/heads/master
```

- Lv2

```bash
git update-ref -d refs/heads/master
```

# Summary

- refs are files storing SHA1s or other refs
- heads, tags, remotes are the main types
- special refs: HEAD, FETCH_HEAD, ORIG_HEAD, MERGE_HEAD, CHERRY_PICK_HEAD, REBASE_HEAD
- symbolic refs point to other refs
- use `git update-ref` and `git symbolic-ref` to manipulate refs

# Creating Direct References

Note: If the reference already exists, it will be overwritten

- Lv0
This operation is very dangerous because it doesn't save operation records.

```bash
mkdir -p ./refs/heads/
echo d4dafde7cd9248ef94c0400983d51122099d312a > ./refs/heads/br1
echo d4dafde7cd9248ef94c0400983d51122099d312a > ./refs/tags/tg1
```

- Lv2
This operation will leave operation records in `<repo>/logs/refs/heads/br1`.

```bash
git update-ref --no-deref -m 'Reason for update' refs/heads/br1 d4da
git update-ref --no-deref refs/tags/tg1 d4da
```

- Lv3
This operation will leave operation records in `<repo>/logs/refs/heads/br1`, with reasons like `branch: Created from ...` or `branch: Reset to ...`.

```bash
# Must omit refs/heads/ here
git branch -f br1 d4da
# Must omit refs/tags/ here
git tag -f tg1 d4da
```

## Viewing Direct References

- Lv0

```bash
cat ./refs/heads/br1
# d4dafde7cd9248ef94c0400983d51122099d312a
cat ./refs/tags/tg1
# d4dafde7cd9248ef94c0400983d51122099d312a
```

- Lv2

```bash
git rev-parse refs/heads/br1
# d4dafde7cd9248ef94c0400983d51122099d312a
git rev-parse refs/tags/tg1
# d4dafde7cd9248ef94c0400983d51122099d312a
```

- Lv3
Lv3 commands can only see the dereferenced object, cannot clearly see the indirect reference itself

```bash
# Must omit refs/heads/ here
git branch -avl br1
#   br1 d4dafde The commit message May have multiple lines!
# Must omit refs/tags/ here
git tag -l tg1
# tg1
```

# Creating Indirect References

- Lv0

```bash
echo 'ref: refs/heads/br1' > ./refs/heads/br2
```

- Lv2

```bash
git symbolic-ref refs/heads/br2 refs/heads/br1
```

# Viewing Indirect References

- Lv0

```bash
cat ./refs/heads/br2
# ref: refs/heads/br1
```

- Lv2
Note the difference between the following two

```bash
git symbolic-ref refs/heads/br2
# refs/heads/br1
git rev-parse refs/heads/br2
# d4dafde7cd9248ef94c0400983d51122099d312a
```

- Lv3
Lv3 commands can only see the dereferenced object, cannot clearly see the indirect reference itself

```bash
# Must omit refs/heads/ here
git branch -avl br1
#   br1 d4dafde The commit message May have multiple lines!
git branch -avl br2
#   br2 d4dafde The commit message May have multiple lines!
```

# Deleting References

- Lv0

```bash
rm ./refs/heads/br1
rm ./refs/heads/br2
rm ./refs/tags/tg1
```

- Lv2

```bash
# The following operation will delete refs/heads/br1
git update-ref -d refs/heads/br1
git update-ref -d --no-deref refs/heads/br1
git update-ref -d refs/heads/br2 # Note the effect of --no-deref
# The following operation will delete refs/heads/br2
git update-ref -d --no-deref refs/heads/br2
(git symbolic-ref refs/heads/br2 refs/heads/br1)
git symbolic-ref --delete refs/heads/br2
```

- Lv3

```bash
# Must omit refs/heads/ here
(git update-ref --no-deref refs/heads/br1 d4da)
git branch -D br1
# Deleted branch br1 (was d4dafde).
(git symbolic-ref refs/heads/br2 refs/heads/br1)
git branch -D br2
# Deleted branch br2 (was refs/heads/br1).
# Must omit refs/tags/ here
(git update-ref --no-deref refs/tags/tg1 d4da)
git tag -d tg1
# Deleted tag 'tg1' (was d4dafde)
```

# Special Notes About `update-ref`

With `--no-deref` means modifying the reference itself (regardless of its type)
Without `--no-deref` means modifying the reference itself (if it doesn't exist or is a direct reference) or the reference's reference (if it's an indirect reference)
```bash
# The following operations will modify refs/heads/br1
git update-ref refs/heads/br1 efd4
git update-ref --no-deref refs/heads/br1 efd4
git update-ref refs/heads/br2 efd4 # Note the effect of --no-deref
# The following operation will modify refs/heads/br2, changing from indirect to direct reference
git update-ref --no-deref refs/heads/br2 efd4
```

# Viewing History

```bash
git update-ref --no-deref refs/heads/br1 d4da
git update-ref --no-deref refs/heads/br1 efd4
```

- Lv0

```bash
cat ./logs/refs/heads/br1
# cat: ./logs/refs/heads/br1: No such file or directory
```

- Lv3

```bash
git reflog refs/heads/br1
```

# Batch Viewing References

- Lv2

`git show-ref` takes something similar to `git rev-parse`, while `git for-each-ref` takes a prefix:

```bash
(git update-ref --no-deref HEAD d4da)
(git update-ref --no-deref SOME_THING d4da)
(git update-ref --no-deref refs/heads/br1 d4da)
(git symbolic-ref refs/heads/br2 refs/heads/br1)
(git update-ref --no-deref refs/remotes/origin/br3 d4da)
(git update-ref --no-deref refs/tags/tg1 d4da)
git show-ref --head
# d4dafde7cd9248ef94c0400983d51122099d312a HEAD
# d4dafde7cd9248ef94c0400983d51122099d312a refs/heads/br1
# d4dafde7cd9248ef94c0400983d51122099d312a refs/heads/br2
# efd4f82f6151bd20b167794bc57c66bbf82ce7dd refs/heads/master
# e8fc2c133d02a4ffb5c5e9f9f9472410d222cea3 refs/notes/commits
# d4dafde7cd9248ef94c0400983d51122099d312a refs/remotes/origin/br3
# d4dafde7cd9248ef94c0400983d51122099d312a refs/tags/tg1
# 9cb6a0ecbdc1259e0a88fa2d8ac4725195b4964d refs/tags/the-tag
git show-ref
# d4dafde7cd9248ef94c0400983d51122099d312a refs/heads/br1
# d4dafde7cd9248ef94c0400983d51122099d312a refs/heads/br2
# efd4f82f6151bd20b167794bc57c66bbf82ce7dd refs/heads/master
# e8fc2c133d02a4ffb5c5e9f9f9472410d222cea3 refs/notes/commits
# d4dafde7cd9248ef94c0400983d51122099d312a refs/remotes/origin/br3
# d4dafde7cd9248ef94c0400983d51122099d312a refs/tags/tg1
# 9cb6a0ecbdc1259e0a88fa2d8ac4725195b4964d refs/tags/the-tag
git for-each-ref
# d4dafde7cd9248ef94c0400983d51122099d312a commit	refs/heads/br1
# d4dafde7cd9248ef94c0400983d51122099d312a commit	refs/heads/br2
# efd4f82f6151bd20b167794bc57c66bbf82ce7dd commit	refs/heads/master
# e8fc2c133d02a4ffb5c5e9f9f9472410d222cea3 commit	refs/notes/commits
# d4dafde7cd9248ef94c0400983d51122099d312a commit	refs/remotes/origin/br3
# d4dafde7cd9248ef94c0400983d51122099d312a commit	refs/tags/tg1
# 9cb6a0ecbdc1259e0a88fa2d8ac4725195b4964d tag	refs/tags/the-tag
git show-ref br1
# d4dafde7cd9248ef94c0400983d51122099d312a refs/heads/br1
git for-each-ref br1
git show-ref refs/remotes/
git for-each-ref refs/remotes/
# d4dafde7cd9248ef94c0400983d51122099d312a commit	refs/remotes/origin/br3
```

Neither can list references under `$GIT_DIR`!

# Finding References Given a commit-ish

- Lv1

```bash
git show-ref | grep $(git rev-parse d4da) | awk '{ print $2; }'
# refs/heads/br1
# refs/heads/br2
# refs/remotes/origin/br3
# refs/tags/tg1
```

- Lv2

```bash
git name-rev d4da
# d4da tags/tg1
git name-rev --all
# e8fc2c133d02a4ffb5c5e9f9f9472410d222cea3 notes/commits
# cb132dbe2c9e9f8d684452078ba659242d5b9cb7 notes/commits~1
# efd4f82f6151bd20b167794bc57c66bbf82ce7dd master
# d4dafde7cd9248ef94c0400983d51122099d312a tags/tg1
```

- Lv3

```bash
git describe d4da
# fatal: No annotated tags can describe 'd4dafde7cd9248ef94c0400983d51122099d312a'.
# However, there were unannotated tags: try --tags.
git describe --always d4da
# d4dafde
git describe d4da~
# fatal: Not a valid object name d4da~
git describe --always d4da~
# fatal: Not a valid object name d4da~
```

Adding `--dirty` will append `-dirty` to the result, which is particularly useful for version numbers.

# Summary

- Add/Modify/Delete
  - Lv0
    - Not recommended
  - Lv2
    - `git rev-parse <object>`
    - `git update-ref --no-deref <ref> [-d|<new>]` - Modify `<ref>`
    - `git update-ref <ref> [-d|<new>]` - Modify `<ref>` or its reference's reference
    - `git symbolic-ref --delete <ref>`
    - `git symbolic-ref <from> <to>`
  - Lv3
    - `git branch -f <branch> <commit-ish>` - Can only manipulate `refs/heads/`
    - `git branch -D <branch>` - Can only manipulate `refs/heads/`
    - `git tag -f <tag> <commit-ish>` - Can only manipulate `refs/tags/`
    - `git tag -d <tag>` - Can only manipulate `refs/tags/`
- View History
  - Lv0
    - `cat <repo>/logs/<ref>`
  - Lv3
    - `git reflog`
- View Single Reference
  - Lv0
    - `cat <repo>/<ref>`
  - Lv2
    - `git rev-parse <ref>` - Can take multiple but not as convenient as `git show-ref`
    - `git symbolic-ref <ref>`
- Batch View
  - Lv2
    - `git show-ref [--head] [<ref>...]`
    - `git for-each-ref [<ref-pattern>...]`
  - Lv3
    - `git branch -av`
    - `git branch -avl <branch-pattern>`
    - `git tag -l <tag-pattern>`
- Find References Given a commit-ish
  - Lv2
    - `git name-rev [--tags] --all|<commit-ish>`
  - Lv3
    - `git describe [--all] [--always] [<commit-ish>]` - Empty means HEAD
    - `git describe [--all] [--always] --dirty`

# Further Reading

[git-rev-parse: specifying revisions](https://git-scm.com/docs/git-rev-parse#_specifying_revisions)


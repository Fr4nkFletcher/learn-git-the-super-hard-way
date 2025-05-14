# Basic Concepts

The index (also called the staging area or cache) is a special file, usually located at `<repo>/index`. It records the mapping between files in the worktree and objects in the repo. The index is a binary file, and its format is described in [git-index-format](https://git-scm.com/docs/git-index-format).

The index is used to stage changes before committing them. When you run `git add`, files are added to the index. When you run `git commit`, the contents of the index are used to create a new commit.

# Creating and Viewing the Index

- Lv1

```bash
touch index
```

- Lv2

```bash
git update-index --add --cacheinfo 100644 ce013625030ba8dba906f756967f9e9ca394464a name.ext
git update-index --add --cacheinfo 100755 ce013625030ba8dba906f756967f9e9ca394464a name2.ext
```

- Lv3

```bash
git add name.ext name2.ext
git ls-files --stage
# 100644 ce013625030ba8dba906f756967f9e9ca394464a 0	name.ext
# 100755 ce013625030ba8dba906f756967f9e9ca394464a 0	name2.ext
```

# Modifying the Index

- Lv2

```bash
git update-index --remove name.ext
```

- Lv3

```bash
git restore --staged name.ext
```

# Summary

- The index is a binary file that records the mapping between worktree files and repo objects
- `git update-index` and `git add` are used to add files to the index
- `git update-index --remove` and `git restore --staged` are used to remove files from the index


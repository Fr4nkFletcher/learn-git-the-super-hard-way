# Basic Concepts

Git can be used in various advanced scenarios, including integration with other tools, scripting, and custom workflows.

# Integrating with Other Tools

- Lv2

```bash
git difftool
```

# Scripting with Git

- Lv2

```bash
git log --pretty=format:'%h %s' | while read line; do echo $line; done
```

# Custom Workflows

- Lv2

```bash
git config --global alias.lg "log --oneline --graph --all"
```

# Summary

- Use `git difftool` to integrate with diff tools
- Use shell scripting with Git for automation
- Create custom workflows with Git aliases

# Basic Knowledge

Mistakes can cause data loss or software version errors.
Git has many mechanisms to minimize the actual damage caused by mistakes.
And flexible use of various Git commands can also fix various errors.

# Mistakes That May Cause Data Loss

## Serious Mistakes

- Accidentally deleting the entire repo (`rm -rf .git`)
    - Immediately backup the worktree to avoid further loss
    - If all branches have been pushed to remote, consider fetching them back
    - If some commits haven't been pushed, please refer to [File recovery](https://wiki.archlinux.org/index.php/File_recovery)
- Accidentally deleting the entire worktree, but repo still exists
    - For files that exist in the index, use `git restore --worktree`
    - For parts that haven't been `git add`ed, please refer to [File recovery](https://wiki.archlinux.org/index.php/File_recovery)
- Accidentally deleting both the entire repo and worktree
    - Refer to the above two points

## Reference Mistakes

- Executing `git switch` when HEAD is a direct reference
    - Check the output of the mistaken command, which contains the SHA1 that HEAD previously pointed to
    - `git reflog`
- Mistaken `git reset --soft`
    - `git reflog`
- Mistaken `git branch|tag -f`
    - `git reflog`
- Mistaken `git branch|tag -d|-D`
    - Check the output of the mistaken command, which contains the SHA1 that the reference previously pointed to
    - You can try `git fsck`, which lists all SHA1s of objects not used by any reference/object, then check them one by one
    - You can also refer to [File recovery](https://wiki.archlinux.org/index.php/File_recovery), but it's not recommended
- Mistaken `git rebase`
    - If rebase is still in progress, immediately `git rebase --abort`
    - `git reflog`

## Worktree Mistakes

- Mistaken `rm -rf` of worktree files
    - If not yet `git add`ed, please refer to [File recovery](https://wiki.archlinux.org/index.php/File_recovery); otherwise:
    - For regular files/folders, use `git restore --worktree`
    - For submodules, use `git submodule update`
- Mistaken `git clean` of worktree files
    - Please refer to [File recovery](https://wiki.archlinux.org/index.php/File_recovery)
- Mistaken `git restore --worktree`
    - **This is one of the most dangerous situations**, the only hope is the editor's cache. Check if the corresponding file has been re-read from the hard disk in the editor; if yes, try to undo and save; if not, try to save.

## Index Mistakes

- Mistaken `git add` overwriting records in the index
    - Generally, the objects of records in the index should exist in the repo, so `git fsck` lists all blobs and then check them one by one
    - However, the actual operation is quite complex and tedious, be careful
- Mistaken `git rm --cached` deleting records in the index
    - If the file in the worktree still exists and the content is correct, just `git add` again
    - Same as the previous discussion about mistaken `git add`
- Mistaken `git reset [--mixed]`
    - You may need `git reflog` to recover HEAD
    - Same as the previous discussion about mistaken `git add`
- Mistaken `git restore --staged` or `git read-tree`
    - Same as the previous discussion about mistaken `git add`

## Simultaneous Index and Worktree Mistakes

The approach is completely consistent: prioritize solving worktree problems, then recover the index.

- Mistaken `git rm -rf`
- Mistaken `git reset --hard`
- Mistaken `git restore --staged --worktree`
- Mistaken `git switch -f`


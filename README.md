# learn-git-the-super-hard-way

[GitBook](https://app.gitbook.com/@b1f6c1c4/s/learn-git-the-super-hard-way/)

## Table of Contents

0. [Create Working Environment](chapter0.md) (`git init`)
1. [Directly Manipulate Objects](chapter1.md) (`git commit`)
2. [Directly Manipulate References](chapter2.md) (`git branch`)
3. [Directly Manipulate the Index](chapter3.md) (`git add / restore`)
4. [Directly Manipulate HEAD](chapter4.md) (`git switch`)
5. [Directly Manipulate Remotes](chapter5.md) (`git pull / push`)
6. [Directly Manipulate Merges](chapter6.md) (`git diff / merge`)
7. [Directly Manipulate Commits](chapter7.md) (`git rebase`)
8. [Search and View History](chapter8.md) (`git log / blame / grep`)
9. [The Evil Submodule](chapter9.md) (`git submodule`)
10. [Batch Processing and Automation](chapter10.md)
11. [Configuration and Aliases](chapter11.md) (`git config`)
12. [Single Repo Multi-Branch Workflow](chapter12.md)
13. [GPG Signatures](chapter13.md)
14. [Data Import and Export](chapter14.md) (`git archive`)
15. [Data Recovery and Emergency](chapter15.md)

**This tutorial also provides a [cheatsheet](cheatsheet.md) for review and self-testing.**

If you are not familiar with any of the commands in the cheatsheet, you may want to start with some basic tutorials: [Beginner](https://try.github.io), [Intermediate](https://learngitbranching.js.org), [Advanced](https://git-scm.com/book/en/v2). The advanced tutorial can be studied alongside this one.

If you have already mastered all the commands in the cheatsheet, this tutorial may be too basic for you. Consider moving on to the [Git Reference](https://git-scm.com/docs) or [Git source code](https://github.com/git/git).

After completing this tutorial, you should have mastered 1% of all Git usage.

Note: Detailed explanations of `git reset`/`git checkout` are in Chapter 4. It is highly recommended to use the more powerful and intuitive `git restore` and `git switch`.

## Basic Conventions

To better understand Git at its core, this tutorial introduces multiple ways to perform the same operation. The table below describes which method is best suited for different scenarios.

| Level | Meaning | Usage Scenario |
| --- | --- | --- |
| Lv0 | Pure manual implementation, no Git CLI | Learning Git internals |
| Lv1 | Use low-level Git CLI with manual steps | For extremely special Git operations |
| Lv2 | Use low-level Git CLI | For unconventional Git operations |
| Lv3 | Use standard Git CLI | Daily use |
| Lv4 | Git Alias | Extending Git for daily use |
| Lv5 | Scripting with Git CLI | For unconventional extensions, occasional use |

## Git Command Line Basics

### Global Command Line Parameters

- cwd defaults to `.`, meaning to `cd` there before running subsequent commands
- work-tree defaults to `GIT_WORK_TREE` or `.`, but not all commands involve the worktree
- git-dir defaults to `GIT_DIR` or `./.git`:
  - If `./.git` is a directory, it is used as the repo
  - If `./.git` is a file, its content (usually an absolute path) is used as the repo

```bash
git [-C <cwd>] [--git-dir=<repo>] [--work-tree=<worktree>] <command> [args]
```

### Specific Git Command Parameters

Most commands follow this format for arguments:
- object: an object expression
  - Usually composed of references, object SHA1, ^, ~, :, etc.
  - See `git rev-parse` (Lv2) for the full list
- path: a file path
- `--` can be omitted if there is no ambiguity

Note: The presence or absence of the `<path>` parameter can fundamentally change the command's meaning

```bash
git <command> [options] [<object>]
git <command> [options] [<object>] -- [<path>]
```

## Git Command List

This tutorial covers all common Git commands and more than half of the uncommon ones. See the [Roadmap](ROADMAP.md).

## License

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License</a>.

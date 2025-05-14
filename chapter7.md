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

# 复制逻辑commit

在开始之前，先创建几个commit：
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

- Lv1

```bash
git reset HEAD -- .
git checkout-index -f -a
git read-tree -m cd5b^ cd5b
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

commit B
EOF
# 21ee944892050dd1fe60ca83d529ebcf3cc29265
git reset --soft 21ee9448
```

- Lv3

```bash
git update-ref --no-deref refs/heads/br1 f071f8ea
GIT_COMMITTER_NAME=committer \
GIT_COMMITTER_EMAIL=committer@gmail.com \
GIT_COMMITTER_DATE='1514736060 +0800' \
git cherry-pick --keep-redundant-commits cd5b 2c35
# Already up to date!
# [br1 553810a] commit B
#  Author: b1f6c1c4 <b1f6c1c4@gmail.com>
#  Date: Mon Jan 1 00:00:01 2018 +0800
# Already up to date!
# [br1 d352b36] commit E
#  Author: b1f6c1c4 <b1f6c1c4@gmail.com>
#  Date: Mon Jan 1 00:00:04 2018 +0800
git lg br1 br2 br3 br4
# * d352b36 - (HEAD -> br1) <b1f6c1c4/committer> commit E
# * 553810a - <b1f6c1c4/committer> commit B
# * f071f8e - <b1f6c1c4/b1f6c1c4> commit H
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
# 注意author信息与cd5b的一致
git cat-file commit HEAD~
# tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
# parent f071f8ea1168fa5f7ab26295836e425b6e8d90b9
# author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736001 +0800
# committer committer <committer@gmail.com> 1514736060 +0800
#
# commit B
# 注意author信息与2c35的一致
git cat-file commit HEAD
# tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
# parent 553810a2fbb39fca3fc78ec3376ff64bf995028a
# author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736004 +0800
# committer committer <committer@gmail.com> 1514736060 +0800
#
# commit E
```

# 逻辑撤销commit

逻辑撤销即添加逆向修改。
你完全可以添加不在本分支上的commit的逆。

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
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736001 +0800

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

`git revert`：对若干commits依次执行以下操作：
* `git read-tree -m <commit> <commit>^` （注意：此处方向反过来了）
* `git commit-tree $(git write-tree) -p HEAD`
  * author信息沿用原author的
* `git reset --soft <new-commit>`

遗憾的是，`git revert`并不支持`--allow-empty`，这里就不演示了。

# 详解`git rebase`

## 基本语义

为了更方便地执行大规模commit复制，`git rebase`应运而生。
基本用法是：
- `git rebase [--no-ff] --keep-empty [-i] [--onto <newbase>] [<upstream> [<branch>]]`
  - 若省略`--onto`，则`<newbase>=<upstream>`
  - 若省略`<branch>`，则为`git symbolic-ref HEAD`
  - 若省略`<upsteram>`，则为`<branch>`的默认remote
- `git rebase [--no-ff] --keep-empty [-i] --onto <newbase> --root [<branch>]`
  - 若省略`<branch>`，则为`git symbolic-ref HEAD`
- `git rebase [--no-ff] --keep-empty [-i] --root [<branch>]`
  - 若省略`<branch>`，则为`git symbolic-ref HEAD`

第一种rebase是把upsteram到branch的所有逻辑修改（选择性地）应用到newbase上。
依次执行以下命令：
* `git rev-list ^<upstream> <branch> > git-rebase-todo`
* `vim git-rebase-todo`
* `git update-ref <branch> <newbase>`
* `git symbolic-ref HEAD <branch>`
* `git reset --hard`
* 根据todo文件的内容，对每一个非drop项执行：
  * `git read-tree -m <commit>^ <commit>`
  * `git checkout-index -fua`
  * pick项：
    * 如果没有`--no-ff`且`<commit>`以HEAD为parent，则`git reset --hard <commit>`
    * 不然，`git commit-tree $(git write-tree) -p HEAD`
  * squash和fixup项：
    * `git commit-tree $(git write-tree) -p HEAD~`
  * `git reset --soft <new-commit>`

第二种rebase是把branch的所有逻辑修改（选择性地）应用到newbase上。
依次执行以下命令：
* `git rev-list ^<newbase> <branch> > git-rebase-todo`
* `vim git-rebase-todo`
* `git update-ref <branch> <newbase>`
* `git symbolic-ref HEAD <branch>`
* `git reset --hard`
* 根据todo文件的内容，对每一个非drop项执行：
  * `git read-tree -m <commit>^ <commit>`
  * `git checkout-index -fua`
  * pick项：
    * 如果没有`--no-ff`且`<commit>`以HEAD为parent，则`git reset --hard <commit>`
    * 不然，`git commit-tree $(git write-tree) -p HEAD`
  * squash和fixup项：
    * `git commit-tree $(git write-tree) -p HEAD~`
  * `git reset --soft <new-commit>`

第三种rebase是把branch的所有逻辑修改（选择性地）重新实现一遍。
依次执行以下命令：
* `git rev-list <branch> > git-rebase-todo`
* `vim git-rebase-todo`
* `git symbolic-ref HEAD <branch>`
* `git rm -rf .`
* 根据todo文件的内容，对第一个非drop项执行：
  * `git read-tree -m <commit>^ <commit>`
  * `git checkout-index -fua`
  * pick项：
    * 如果没有`--no-ff`且`<commit>`没有parent，则`git reset --hard <commit>`
    * 不然，`git commit-tree $(git write-tree)`
  * `git reset --soft <new-commit>`
* 根据todo文件的内容，对其余每一个非drop项执行：
  * `git read-tree -m <commit>^ <commit>`
  * pick项：
    * 如果没有`--no-ff`且`<commit>`以HEAD为parent，则`git reset --hard <commit>`
    * 不然，`git commit-tree $(git write-tree) -p HEAD`
  * squash和fixup项：
    * `git commit-tree $(git write-tree) -p HEAD~`
  * `git reset --soft <new-commit>`

## 示例

```bash
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

第一种rebase：
```bash
(git update-ref --no-deref refs/heads/br1 f071f8ea)
(git update-ref --no-deref refs/heads/br2 3aec5e98)
(git update-ref --no-deref refs/heads/br3 85706b31)
(git update-ref --no-deref refs/heads/br4 31155de8)
(git symbolic-ref HEAD refs/heads/br1)
GIT_COMMITTER_NAME=committer \
GIT_COMMITTER_EMAIL=committer@gmail.com \
GIT_COMMITTER_DATE='1514736120 +0800' \
git rebase --quiet --no-ff --keep-empty --onto br2 br3 br4
git lg br1 br2 br3 br4
# * 4da7f18 - (HEAD -> br4) <b1f6c1c4/committer> commit G
# * 3aec5e9 - (br2) <b1f6c1c4/b1f6c1c4> commit D
# | * f071f8e - (br1) <b1f6c1c4/b1f6c1c4> commit H
# |/  
# * c6e6deb - <b1f6c1c4/b1f6c1c4> commit C
# | * 85706b3 - (br3) <b1f6c1c4/b1f6c1c4> commit F
# | * 2c353e8 - <b1f6c1c4/b1f6c1c4> commit E
# |/  
# * cd5b86b - <b1f6c1c4/b1f6c1c4> commit B
# * 7b2c8f5 - <b1f6c1c4/b1f6c1c4> commit A
```

第二种rebase：
```bash
(git update-ref --no-deref refs/heads/br1 f071f8ea)
(git update-ref --no-deref refs/heads/br2 3aec5e98)
(git update-ref --no-deref refs/heads/br3 85706b31)
(git update-ref --no-deref refs/heads/br4 31155de8)
(git symbolic-ref HEAD refs/heads/br1)
GIT_COMMITTER_NAME=committer \
GIT_COMMITTER_EMAIL=committer@gmail.com \
GIT_COMMITTER_DATE='1514736120 +0800' \
git rebase --quiet --no-ff --keep-empty --onto br2 --root br4
git lg br1 br2 br3 br4
# * 979feed - (HEAD -> br4) <b1f6c1c4/committer> commit G
# * b70ca17 - <b1f6c1c4/committer> commit E
# * 3aec5e9 - (br2) <b1f6c1c4/b1f6c1c4> commit D
# | * f071f8e - (br1) <b1f6c1c4/b1f6c1c4> commit H
# |/  
# * c6e6deb - <b1f6c1c4/b1f6c1c4> commit C
# | * 85706b3 - (br3) <b1f6c1c4/b1f6c1c4> commit F
# | * 2c353e8 - <b1f6c1c4/b1f6c1c4> commit E
# |/  
# * cd5b86b - <b1f6c1c4/b1f6c1c4> commit B
# * 7b2c8f5 - <b1f6c1c4/b1f6c1c4> commit A
```

第三种rebase：
```bash
(git update-ref --no-deref refs/heads/br1 f071f8ea)
(git update-ref --no-deref refs/heads/br2 3aec5e98)
(git update-ref --no-deref refs/heads/br3 85706b31)
(git update-ref --no-deref refs/heads/br4 31155de8)
(git symbolic-ref HEAD refs/heads/br1)
GIT_AUTHOR_NAME=author \
GIT_AUTHOR_EMAIL=author@gmail.com \
GIT_AUTHOR_DATE='1234567890 +0800' \
GIT_COMMITTER_NAME=committer \
GIT_COMMITTER_EMAIL=committer@gmail.com \
GIT_COMMITTER_DATE='1514736120 +0800' \
git rebase --quiet --no-ff --keep-empty --root br4
git lg br1 br2 br3 br4
# * 67df3f7 - (HEAD -> br4) <b1f6c1c4/committer> commit G
# * 23eb8c6 - <b1f6c1c4/committer> commit E
# * 5aaf590 - <b1f6c1c4/committer> commit B
# * 71fcb5a - <b1f6c1c4/committer> commit A
# * f071f8e - (br1) <b1f6c1c4/b1f6c1c4> commit H
# | * 85706b3 - (br3) <b1f6c1c4/b1f6c1c4> commit F
# | * 2c353e8 - <b1f6c1c4/b1f6c1c4> commit E
# | | * 3aec5e9 - (br2) <b1f6c1c4/b1f6c1c4> commit D
# | |/  
# |/|   
# * | c6e6deb - <b1f6c1c4/b1f6c1c4> commit C
# |/  
# * cd5b86b - <b1f6c1c4/b1f6c1c4> commit B
# * 7b2c8f5 - <b1f6c1c4/b1f6c1c4> commit A
```

# 对merge的处理

`git rebase`有四种处理merge的模式。
其中`--preserve-merge`由于bug连篇建议在任何情况下都不要使用。

下面分别对三种rebase讨论其他三种处理方式的语义。

- 第一种rebase：对于`^<upstream> <branch>`
  - 默认：
    - 先对所有待rebase的DFS排序
    - 再去掉所有包含多于1个parent的commit
    - 依次应用到`<newbase>`上
  - `--rebase-merges[=-no-rebase-cousins]`：
    - 原没有parent，现依然没有parent
    - 原以`<upstream>^!`为直接parent，现以`<newbase>`为直接parent
    - 原以`<upstream>^@`为直接parent，parent不变
    - 原以`^<upstream> <branch>`为直接parent，现以新创建的相应commit为parent
  - `--rebase-merges=rebase-cousins`：
    - 原没有parent，现以`<newbase>`为parent
    - 原以`<upstream>^!`为直接parent，变成以`<newbase>`为直接parent
    - 原以`<upstream>^@`为直接parent，parent不变
    - 原以`^<upstream> <branch>`为直接parent，现以新创建的相应commit为parent
- 第二种rebase：对于`^<newbase> <branch>`
  - 默认：
    - 先对所有待rebase的DFS排序
    - 再去掉所有包含多于1个parent的commit
    - 依次应用到`<newbase>`上
  - `--rebase-merges[=-no-rebase-cousins]`：
    - 原没有parent，现依然没有parent
    - 原以`<newbase>`为直接parent，parent不变
    - 原以`^<newbase> <branch>`为直接parent，现以新创建的相应commit为parent
  - `--rebase-merges=rebase-cousins`：
    - 原没有parent，现以`<newbase>`为parent
    - 原以`<newbase>`为直接parent，parent不变
    - 原以`^<newbase> <branch>`为直接parent，现以新创建的相应commit为parent
- 第三种rebase：对于`<branch>`
  - 默认：
    - 先对所有待rebase的DFS排序
    - 再去掉所有包含多于1个parent的commit
    - 从零开始复制
  - `--rebase-merges[=-no-rebase-cousins]`：同下
  - `--rebase-merges=rebase-cousins`：
    - 原没有parent，现依然没有parent
    - 原以`<branch>`为直接parent，现以新创建的相应commit为parent

# 总结

- Lv2
  - `git cherry-pick --keep-redundant-commits <commit-ish>...`
- Lv3
  - `git revert <commit-ish>...`
  - `git rebase --keep-empty [-i] [--onto <newbase>] [(<upstream>|--root) [<branch>]]`
    - `[--no-ff]`
    - `[--rebase-merges[=[no-]rebase-cousins]]`

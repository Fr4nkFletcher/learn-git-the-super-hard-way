# Basic Concepts

Git supports various workflows and advanced usage patterns to accommodate different development needs.

# Forking Workflow

- Lv2

```bash
git clone https://github.com/other-user/repo.git
# Make changes
git push origin master
```

# Feature Branch Workflow

- Lv2

```bash
git checkout -b feature-branch
# Make changes
git push origin feature-branch
```

# Pull Request Workflow

- Lv2

```bash
# On GitHub or GitLab, open a pull request from your feature branch
```

# Summary

- Use different workflows (forking, feature branch, pull request) for collaborative development

Consider the following scenario: developing a **medium, small, or micro** microservice system, containing 10 components

方案1：（多repo）创建10个git repo，每个repo一个master一个dev

方案2：（单repo单分支）创建1个git repo，设一个master一个dev

方案3：（单repo多分支）创建1个git repo，弃用master，每个组件单设自己的master和dev等等

| | 方案1 | 方案2 | 方案3 |
| --- | --- | --- | --- |
| 环境配置简易程度 | -------- | ++++++++ | --- |
| 空间独立（同时修改不同组件是否可行） | ++++++++ | -------- | ++++++++ |
| 时间对齐（组件版本是否能够统一） | -------- | ++++++++ | +++++ |
| 组件之间可以互相参照 | -------- | ++++++ | ++++++++ |
| 添加删除组件是否方便 | ++++++++ | -------- | +++++++ |

Therefore, the micro project scheme 2 is the best, and the small and medium project scheme 3 is the best.
Scheme 3 may have the following problems:

- Time alignment cannot be achieved: it does not exist, refer to the following
- Need to check out frequently: it does not exist, refer to the following
- Single git library is too large: it does not exist, I have assumed it is a **small to medium** system

# Single repo multi-branch workflow branch settings

For small projects, it is recommended to configure branches as follows:

- doc
  - All system design documents
- master
  - Integrate system components through merge componentX
- component1
  - Obtain design documents related to it through merge doc
- component2
  - Obtain design documents related to it through merge doc
- component3
  - Obtain design documents related to it through merge doc
- component4
  - Obtain design documents related to it through merge doc
- ...

For medium projects, it is recommended to configure branches as follows:

- doc
  - System overall design document, component interface document
- master
  - Merge dev after testing is stable
- release
  - Merge master before release
- dev
  - Integrate system components through merge componentX/master
  - Conduct integration testing here
- component1/doc
  - Obtain system overall document and add component internal design document through merge doc
- component1/master
  - Merge component1/dev after testing is stable
- component1/dev
  - Merge component1/featY after feature development is complete
- component1/feat1
  - Develop specific features here
- component1/feat2
  - Develop specific features here
- component1/feat3
  - Develop specific features here
- component2/doc
  - Obtain system overall document and add component internal design document through merge doc
- component2/master
  - Merge component2/dev after testing is stable
- component2/dev
  - Merge component2/featY after feature development is complete
- component2/feat1
  - Develop specific features here
- component2/feat2
  - Develop specific features here
- component2/feat3
  - Develop specific features here
- ...

# Single repo multi-branch workflow specific operations

Note that the worktree of different component branches is completely different and independent from each other, and absolutely cannot be merged or shared with each other;
While the worktree of different small branches within the same component is basically consistent, but with development order (similar to different branches of a monolithic application), it is suitable for merging and generally shares the same worktree.

## Create

First create repo, do not create worktree:
```bash
mkdir the-project
git init --bare the-project/the-project.git
# Initialized empty Git repository in /root/the-project/the-project.git/
# git -C the-project/the-project.git remote add origin https://github.com/...
```

Then create a (small repo and) worktree for doc and each component:
```bash
cd the-project/the-project.git
# Because git worktree is too stupid, here is a workaround
git hash-object -t commit -w --stdin <<EOF
tree 0000000000000000000000000000000000000000

EOF
# 60bc2812cc97ab2d2f2c7168aa101f7bfabcbf88
git update-ref refs/workaround 60bc2812cc97ab2d2f2c7168aa101f7bfabcbf88
git worktree add --no-checkout --detach ../doc refs/workaround
# Preparing worktree (detached HEAD 60bc281)
git --git-dir=worktrees/doc symbolic-ref HEAD refs/heads/doc
git worktree add --no-checkout --detach ../component1 refs/workaround
# Preparing worktree (detached HEAD 60bc281)
git --git-dir=worktrees/component1 symbolic-ref HEAD refs/heads/component1
git worktree add --no-checkout --detach ../component2 refs/workaround
# Preparing worktree (detached HEAD 60bc281)
git --git-dir=worktrees/component2 symbolic-ref HEAD refs/heads/component2
git worktree add --no-checkout --detach ../component3 refs/workaround
# Preparing worktree (detached HEAD 60bc281)
git --git-dir=worktrees/component3 symbolic-ref HEAD refs/heads/component3
git worktree add --no-checkout --detach ../component4 refs/workaround
# Preparing worktree (detached HEAD 60bc281)
git --git-dir=worktrees/component4 symbolic-ref HEAD refs/heads/component4
git update-ref -d refs/workaround
rm -f objects/60/bc2812cc97ab2d2f2c7168aa101f7bfabcbf88
```

## Clone from other places

It is not recommended to use `git clone --bare`, because you still need to manually modify fetch information (refer to Chapter 5, this method creates a repo by default without fetch config items)

It is recommended to create it as above, and then remote add.

## doc branch

You can directly write documents in the root directory (the-project/doc):
```bash
cd the-project/doc
echo 'Some documents' > documents.txt
git add documents.txt
git hash-object -t commit --stdin -w <<EOF
tree $(git write-tree)
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

Write documents
EOF
# ffe7520ba83e48bb254b9eb0fd07390d98124ede
git reset --soft ffe7520b
```

## component branch

First configure the development environment (here is c++ as an example):
```bash
cd the-project/component1
echo 'build/' > .gitignore
cat - >Makefile <<EOF
build/component1: main.cpp
	g++ -std=2a -o $@ $^
EOF
git add .gitignore Makefile
git hash-object -t commit --stdin -w <<EOF
tree $(git write-tree)
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736010 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736010 +0800

Setup environment
EOF
# 4dbe0f181e875f7a96b3f6079038d353c239a6f4
git reset --soft 4dbe0f1
```

Then read documents from the doc branch:
```bash
cd the-project/component1
# git rm -rf doc/
git read-tree --prefix=doc/ doc
git checkout-index -fua
git hash-object -t commit --stdin -w <<EOF
tree $(git write-tree)
parent $(git rev-parse HEAD)
parent $(git rev-parse doc)
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736010 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736010 +0800

Merge branch doc
EOF
# 9f1f329f771711c7160c0784d4cf8a7f157d5c27
git reset --soft 9f1f329
```

## Document update after each component

Assuming the document has been updated in doc:
```bash
cd the-project/doc
git rm -f documents.txt
# rm 'documents.txt'
echo 'New documents' > new-documents.txt
git add new-documents.txt
git hash-object -t commit --stdin -w <<EOF
tree $(git write-tree)
parent $(git rev-parse HEAD)
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

Move to new documents
EOF
# 9584d01267d5f16c581e521d3bb401f211508eee
git reset --soft 9584d012
```

Then you need to update the document in the component1 branch:
```bash
cd the-project/component1
git rm -rf doc/
# rm 'doc/documents.txt'
git read-tree --prefix=doc/ doc
git checkout-index -fua
git hash-object -t commit --stdin -w <<EOF
tree $(git write-tree)
parent $(git rev-parse HEAD)
parent $(git rev-parse doc)
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736010 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736010 +0800

Merge branch doc
EOF
# 7ad8da3b9f5825c63247bf655ef6da68b637453b
git reset --soft 7ad8da3
git config alias.lg "log --graph --pretty=tformat:'%h -%d (%an/%cn) %s' --abbrev-commit"
git lg
# *   7ad8da3 - (HEAD -> component1) (b1f6c1c4/b1f6c1c4) Merge branch doc
# |\  
# | * 9584d01 - (doc) (b1f6c1c4/b1f6c1c4) Move to new documents
# * | 9f1f329 - (b1f6c1c4/b1f6c1c4) Merge branch doc
# |\| 
# | * ffe7520 - (b1f6c1c4/b1f6c1c4) Write documents
# * 4dbe0f1 - (b1f6c1c4/b1f6c1c4) Setup environment
```

## master

Master for component is like component for doc;
The only difference is that one master will merge multiple compoents, and master itself has very little code
(possibly only README.md, LICENSE, docker-compose.yml, etc. global configuration)

And component completely consistent, in master, read-tree, write-tree, commit-tree,
you can integrate different components into master. Don't forget to add a few parents.

# FAQ

## Should master directly merge doc

If some integration testing is conducted on master, then there should be doc.
Otherwise, it can be omitted.

## Why not use simple convenient `git merge -s subtree doc`

In some weird cases, subtree cannot completely copy the entire tree on doc side,
such as deleted files still exist, new files not appear, etc.
Refer to Chapter 6.

## Where should I build

Generally, you should build in the worktree of each component's own,
such as `node_modules`, `*.o`, `*.pyc`, etc.
If docker is used, then a image should be generated on each component branch,
and then directly `docker-compose up` in master.

If for ci needs, you can also choose to build in master. But this requires double disk space.

## How to simplify operations

Refer to Chapter 8. Also, don't forget `git push --all`, `git log --all`, etc.

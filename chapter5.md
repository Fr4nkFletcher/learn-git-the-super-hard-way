# Basic Concepts

A remote in Git is a reference to another repository, usually used to synchronize code between different repositories. The most common remote is called `origin`.

# Adding a Remote

- Lv1

```bash
echo '[remote "origin"]\n    url = https://github.com/user/repo.git\n    fetch = +refs/heads/*:refs/remotes/origin/*' >> .git/config
```

- Lv2

```bash
git remote add origin https://github.com/user/repo.git
```

# Viewing Remotes

- Lv1

```bash
cat .git/config
```

- Lv2

```bash
git remote -v
```

# Fetching from a Remote

- Lv2

```bash
git fetch origin
```

# Pulling from a Remote

- Lv2

```bash
git pull origin master
```

# Pushing to a Remote

- Lv2

```bash
git push origin master
```

# Summary

- Remotes are references to other repositories
- Use `git remote add`, `git fetch`, `git pull`, and `git push` to interact with remotes

```bash
git init --bare .
# Initialized empty Git repository in /root/
ls
# HEAD
# branches
# config
# description
# hooks
# info
# objects
# refs
```

# Packfile
Before studying Git remotes, we need to first study packfile.
Since the internal format of packfile is quite complex, this section does not cover Lv0 commands.
All commands below are Lv2.

Before starting, create a few objects:
```bash
echo 'obj1' | git hash-object -t blob --stdin -w
# 5ff37e33c444f1ef1a6b3abda4fa05bf78352d12
echo 'obj2' | git hash-object -t blob --stdin -w
# 95fc5713e4d2debb0e898632c63bfe4a4ce0c665
echo 'obj3' | git hash-object -t blob --stdin -w
# cff99442835504ec82ba2b6d6328d898033a5300
git mktree <<EOF
100644 blob 5ff37e33c444f1ef1a6b3abda4fa05bf78352d12$(printf '\t')1.txt
100755 blob 95fc5713e4d2debb0e898632c63bfe4a4ce0c665$(printf '\t')2.txt
EOF
# 2da98740b77749cb1b6b3acaee43a3644fb3e9e5
git mktree <<EOF
100644 blob cff99442835504ec82ba2b6d6328d898033a5300$(printf '\t')3.txt
040000 tree 2da98740b77749cb1b6b3acaee43a3644fb3e9e5$(printf '\t')dir
EOF
# 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b
```
Check object creation status:
```bash
git ls-tree -r 187e
# 100644 blob cff99442835504ec82ba2b6d6328d898033a5300	3.txt
# 100644 blob 5ff37e33c444f1ef1a6b3abda4fa05bf78352d12	dir/1.txt
# 100755 blob 95fc5713e4d2debb0e898632c63bfe4a4ce0c665	dir/2.txt
find objects -type f
# objects/5f/f37e33c444f1ef1a6b3abda4fa05bf78352d12
# objects/cf/f99442835504ec82ba2b6d6328d898033a5300
# objects/95/fc5713e4d2debb0e898632c63bfe4a4ce0c665
# objects/2d/a98740b77749cb1b6b3acaee43a3644fb3e9e5
# objects/18/7e91589a3f4f248f4cc8b1a1eca65b5161cc7b
```

## Creating a Packfile

```bash
mkdir -p ../somewhere-else/
git pack-objects ../somewhere-else/prefix <<EOF
cff99442835504ec82ba2b6d6328d898033a5300
95fc5713e4d2debb0e898632c63bfe4a4ce0c665
187e91589a3f4f248f4cc8b1a1eca65b5161cc7b
EOF
# 2b2d8ce85275da98291c5ad8f60680b2dec81ba4
ls ../somewhere-else/
# prefix-2b2d8ce85275da98291c5ad8f60680b2dec81ba4.idx
# prefix-2b2d8ce85275da98291c5ad8f60680b2dec81ba4.pack
```

## Automatic Object Listing

The previous method manually specified the files to pack; however, since blobs 5ff3 and tree 2da9 were not packed, even if the receiver gets the objects, they would be useless (cannot restore the entire tree 187e, `git checkout-index` would fail).
At this point, we need to use one of Git's most complex Lv2 commands: `git rev-list`
(Other commands with similar complexity include `git filter-branch` and `git merge-tree`).

```bash
git rev-list --objects 187e
# 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b 
# cff99442835504ec82ba2b6d6328d898033a5300 3.txt
# 2da98740b77749cb1b6b3acaee43a3644fb3e9e5 dir
# 5ff37e33c444f1ef1a6b3abda4fa05bf78352d12 dir/1.txt
# 95fc5713e4d2debb0e898632c63bfe4a4ce0c665 dir/2.txt
git rev-list --objects 187e | git pack-objects ../somewhere-else/prefix
# a451aab5615fb6d97e2ecb337b7f1d783ed66a70
```

## Viewing Packfile

```bash
git verify-pack -v ../somewhere-else/prefix-2b2d8ce85275da98291c5ad8f60680b2dec81ba4.idx
# cff99442835504ec82ba2b6d6328d898033a5300 blob   5 14 12
# 95fc5713e4d2debb0e898632c63bfe4a4ce0c665 blob   5 14 26
# 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b tree   63 73 40
# non delta: 3 objects
# ../somewhere-else/prefix-2b2d8ce85275da98291c5ad8f60680b2dec81ba4.pack: ok
git verify-pack -v ../somewhere-else/prefix-a451aab5615fb6d97e2ecb337b7f1d783ed66a70.idx
# 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b tree   63 73 12
# cff99442835504ec82ba2b6d6328d898033a5300 blob   5 14 85
# 2da98740b77749cb1b6b3acaee43a3644fb3e9e5 tree   66 75 99
# 5ff37e33c444f1ef1a6b3abda4fa05bf78352d12 blob   5 14 174
# 95fc5713e4d2debb0e898632c63bfe4a4ce0c665 blob   5 14 188
# non delta: 5 objects
# ../somewhere-else/prefix-a451aab5615fb6d97e2ecb337b7f1d783ed66a70.pack: ok
```
For complex packfiles, a chain structure may appear (only storing incremental modification information). For details, see [here](https://git-scm.com/book/en/v2/Git-Internals-Packfiles).

## Unpacking Packfile

(First delete all objects: `rm -rf objects/*`)
```bash
git unpack-objects < ../somewhere-else/prefix-a451aab5615fb6d97e2ecb337b7f1d783ed66a70.pack
```

# Direct Cross-Repository Object Transfer

First create another repo:
```bash
mkdir -p ../another-repo.git/objects ../another-repo.git/refs
echo 'ref: refs/heads/master' > ../another-repo.git/HEAD
# Allow direct object transfer
git config uploadpack.allowAnySHA1InWant true
git --git-dir=../another-repo.git config uploadpack.allowAnySHA1InWant true
```

Directly request objects (if `--keep` is not added, directly unpack the Packfile):
```bash
git --git-dir=../another-repo.git fetch-pack --keep --no-progress "$(pwd)" 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b
# keep	a451aab5615fb6d97e2ecb337b7f1d783ed66a70
# 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b
```
Note: `$(pwd)` can also be a URL, used for cross-domain object transfer

# Direct Cross-Repository Reference Transfer

Create a reference:
```bash
git hash-object -t commit --stdin -w <<EOF
tree 187e91589a3f4f248f4cc8b1a1eca65b5161cc7b
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

The commit message
EOF
# bb6d205106a1104778884986d8e3594f35170fae
git update-ref refs/heads/itst bb6d
```

Directly request references and their objects:
```bash
git --git-dir=../another-repo.git fetch-pack --no-progress "$(pwd)" refs/heads/itst
# bb6d205106a1104778884986d8e3594f35170fae refs/heads/itst
```

Directly push references and their objects:
```bash
git send-pack --force --no-progress ../another-repo.git refs/heads/itst
# To ../another-repo.git
#  * [new branch]      itst -> itst
```

Check remote references:
```bash
git ls-remote ../another-repo.git
# bb6d205106a1104778884986d8e3594f35170fae	refs/heads/itst
```

# Indirect Cross-Repository Transfer

When cross-domain but unable to establish network connection, first create a bundle:
```bash
git bundle create ../the-bundle refs/heads/itst
```
Then unpack the bundle:
```bash
git --git-dir=../another-repo.git bundle unbundle ../the-bundle
# bb6d205106a1104778884986d8e3594f35170fae refs/heads/itst
```

# Lv3 command

First use Lv0 command to modify settings to discuss the impact of settings on Lv3 commands.

## Specifying remote's `git push` and `git fetch`

Bare remote:
```bash
cat >./config <<EOF
[remote "another"]
  url = ../another-repo.git
EOF
git push another itst
# Everything up-to-date
git fetch another itst
# From ../another-repo
#  * branch            itst       -> FETCH_HEAD
git fetch another itst:tsts
# From ../another-repo
#  * [new branch]      itst       -> tsts
```

Remote with default fetch:
```bash
cat >./config <<EOF
[remote "another"]
  url = ../another-repo.git
  fetch = +refs/heads/*:refs/heads/abc/*
  fetch = +refs/heads/*:refs/heads/def/*
EOF
git fetch another itst
# From ../another-repo
#  * branch            itst       -> FETCH_HEAD
#  * [new branch]      itst       -> abc/itst
#  * [new branch]      itst       -> def/itst
```

Force full push:
```bash
cat >./config <<EOF
[remote "another"]
  url = ../another-repo.git
  mirror = true
EOF
git push another itst
# fatal: --mirror can't be combined with refspecs
git push another
# To ../another-repo.git
#  * [new branch]      abc/itst -> abc/itst
#  * [new branch]      def/itst -> def/itst
#  * [new branch]      tsts -> tsts
```

## Unspecified remote's `git push` and `git fetch`

```bash
cat >./config <<EOF
[remote "another"]
  url = ../another-repo.git
[branch "itst"]
  remote = another
  merge = refs/heads/itst
EOF
git symbolic-ref HEAD refs/heads/itst
git push --verbose
# Pushing to ../another-repo.git
# To ../another-repo.git
#  = [up to date]      itst -> itst
# Everything up-to-date
# Not related to git fetch
```

```bash
cat >./config <<EOF
[remote "another"]
  url = ../another-repo.git
  fetch = +refs/heads/*:refs/remotes/another/*
[branch "itst"]
  remote = another
EOF
git symbolic-ref HEAD refs/heads/itst
git fetch --verbose
# From ../another-repo
#  * [new branch]      abc/itst   -> another/abc/itst
#  * [new branch]      def/itst   -> another/def/itst
#  * [new branch]      itst       -> another/itst
#  * [new branch]      tsts       -> another/tsts
# Not related to git push
```

## Using Lv3 command to modify settings

```bash
(rm -f ./config)
git remote add another ../another-repo.git
cat ./config
# [remote "another"]
# 	url = ../another-repo.git
# 	fetch = +refs/heads/*:refs/remotes/another/*
git push -u another itst
# Everything up-to-date
# Branch 'itst' set up to track remote branch 'itst' from 'another'.
cat ./config
# [remote "another"]
# 	url = ../another-repo.git
# 	fetch = +refs/heads/*:refs/remotes/another/*
# [branch "itst"]
# 	remote = another
# 	merge = refs/heads/itst
(rm -f ./config)
git remote add another --mirror=fetch ../another-repo.git
cat ./config
# [remote "another"]
# 	url = ../another-repo.git
# 	fetch = +refs/*:refs/*
(rm -f ./config)
git remote add another --mirror=push ../another-repo.git
cat ./config
# [remote "another"]
# 	url = ../another-repo.git
# 	mirror = true
```

## About `git pull`

`git pull` basically is first `git fetch` then `git merge FETCH_HEAD`;
`git pull --rebase` basically is first `git fetch` then `git rebase FETCH_HEAD`.
Since this command is highly uncontrollable, it is not recommended to use it.
However, `git pull --ff-only` is very useful, which is first `git fetch` then `git merge --ff-only FETCH_HEAD`.

# Copy repo: `git clone`

(See Chapter 5)

## `git clone --mirror`

Copy most objects, all normal references, special reference HEAD of another repo

- Lv2

```bash
(rm -rf *) # Delete all previous things
# First prepare
git init --bare copy.git
# Initialized empty Git repository in /root/copy.git/
cat >>./copy.git/config <<EOF
[remote "origin"]
  url = git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git
  fetch = +refs/*:refs/*
  mirror = true
EOF
git --git-dir=copy.git fetch origin refs/heads/master:refs/heads/master
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
git --git-dir=copy.git fetch origin refs/heads/dev:refs/heads/dev
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
git --git-dir=copy.git symbolic-ref HEAD refs/heads/master
```

- Lv3

```bash
rm -rf copy.git
git clone --mirror git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git copy.git
# Cloning into bare repository 'copy.git'...
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
```

## `git clone --bare`

Similar to `--mirror`, but not like (no config)

- Lv2

```bash
rm -rf copy.git
git init --bare copy.git
# Initialized empty Git repository in /root/copy.git/
tee -a ./copy.git/config <<EOF
[remote "origin"]
  url = git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git
EOF
# [remote "origin"]
#   url = git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git
git --git-dir=copy.git fetch origin refs/heads/master:refs/heads/master
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
git --git-dir=copy.git fetch origin refs/heads/dev:refs/heads/dev
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
git --git-dir=copy.git symbolic-ref HEAD refs/heads/master
```

- Lv3

```bash
rm -rf copy.git
git clone --bare git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git copy.git
# Cloning into bare repository 'copy.git'...
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
```

## `git clone`

- Lv2

```bash
rm -rf copy-wt
git init copy-wt
# Initialized empty Git repository in /root/copy-wt/.git/
tee -a ./copy-wt/.git/config <<EOF
[remote "origin"]
  url = git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git
  fetch = +refs/*:refs/*
[branch "dev"]
  remote = origin
  merge = refs/heads/dev
EOF
# [remote "origin"]
#   url = git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git
#   fetch = +refs/*:refs/*
# [branch "dev"]
#   remote = origin
#   merge = refs/heads/dev
git --git-dir=copy-wt/.git fetch origin
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
git --git-dir=copy-wt/.git update-ref refs/heads/dev refs/remotes/origin/dev
# fatal: refs/remotes/origin/dev: not a valid SHA1
git --git-dir=copy-wt/.git symbolic-ref HEAD refs/heads/dev
# If --no-checkout is specified, omit this line
git --git-dir=copy-wt/.git --work-tree=copy-wt checkout-index -fua
```

- Lv3

```bash
rm -rf copy-wt
git clone --branch dev git@github.com:b1f6c1c4/learn-git-the-super-hard-way.git copy-wt
# Cloning into 'copy-wt'...
# error: cannot run ssh: No such file or directory
# fatal: unable to fork
```

# Summary

- Lv2
  - Packfile
    - `git rev-list --objects <object> | git pack-objects <path-prefix>`
    - `git unpack-objects`
  - Bundle
    - `git bundle create <file> <refs>*`
    - `git bundle unbundle <file>`
  - Transfer
    - `git fetch-pack <url> <hash>*` - Requires `git config uploadpack.allowAnySHA1InWant true`
    - `git fetch-pack <url> <ref>*`
    - `git send-pack --force <url> <local-ref>:<remote-ref>*`
  - Check
    - `git ls-remote <url>`
- Lv3
  - Configuration
    - `git remote add <remote> [--mirror=push|fetch] <url>`
    - `git push -u <remote> <local-ref>:<remote-ref>`
  - Transfer
    - `git push <remote> <local-ref>:<remote-ref>`
    - `git fetch <remote> <remote-ref>:<local-ref>`
  - One-step progress following
    - `git pull --ff-only`
  - Based on remote to create repo
    - `git clone --mirror <url> <repo>`
    - `git clone --bare <url> <repo>`
    - `git clone [--no-checkout] [--branch <ref>] [--separate-git-dir <repo>] <url> <worktree>`
  - Not recommended evil commands
    - `git pull [--rebase]`


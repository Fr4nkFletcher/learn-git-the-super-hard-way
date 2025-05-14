# Basic Concepts

Git objects are stored in `<repo>/objects/` and come in four types:
- blob: file content, essentially a prefix and the file's raw content
- tree: directory content, essentially a zlib-deflated binary array, each element includes:
  - type (directory 040000, regular file 100644, executable file 100755)
  - filename
  - SHA1 of the corresponding file (blob) or directory (tree)
- commit: essentially a zlib-deflated, newline-separated plain text, including:
  - tree: SHA1 of the root directory's tree
  - parent(s): SHA1 of other commits
  - author: name, email, timestamp (seconds), timezone
  - committer: name, email, timestamp (seconds), timezone
  - mergetag(s): see Chapter 13
  - gpgsig: see Chapter 13
  - message: arbitrary byte stream
- tag: essentially a zlib-deflated, newline-separated plain text, including:
  - object: the object being tagged, can be any object's SHA1
  - type: type of the object
  - tag: tag name
  - tagger: name, email, timestamp (seconds), timezone
  - message: arbitrary byte stream
  - signature: see Chapter 13

All commands in this chapter do not involve the worktree. Later chapters will introduce how to use the worktree to manipulate objects (Lv3).

Since manually performing zlib deflate compression and SHA1 calculation is too cumbersome, this chapter does not introduce Lv0 object creation. Only Lv1 (using `git hash-object`) is covered.

```bash
git init --bare .
# Initialized empty Git repository in /root/
```

# Creating a blob

- Lv1

```bash
echo 'hello' | git hash-object -t blob --stdin -w
# ce013625030ba8dba906f756967f9e9ca394464a
```

- Lv2

```bash
echo 'hello' > temp-file
git hash-object -t blob temp-file -w
# ce013625030ba8dba906f756967f9e9ca394464a
```

# Viewing a blob

- Lv0

```bash
# Note: Do not print gunzip output directly to the console, or it may stop at \0
printf '\x1f\x8b\x08\x00\x00\x00\x00\x00' \
| cat - ./objects/ce/013625030ba8dba906f756967f9e9ca394464a \
| gunzip -dc 2>/dev/null | xxd
# 00000000: 626c 6f62 2036 0068 656c 6c6f 0a         blob 6.hello.
```

- Lv2

```bash
git cat-file blob ce01
# hello
```

- Lv3

```bash
# Using git show directly on a blob is equivalent to git cat-file blob
git show ce01
# hello
```

# Creating a tree

- Lv1
Note: Sort filenames first, then use `git hash-object`

```bash
(printf '100644 name.ext\x00';
echo '0: ce013625030ba8dba906f756967f9e9ca394464a' | xxd -rp -c 256;
printf '100755 name2.ext\x00';
echo '0: ce013625030ba8dba906f756967f9e9ca394464a' | xxd -rp -c 256) \
| git hash-object -t tree --stdin -w
# 58417991a0e30203e7e9b938f62a9a6f9ce10a9a
```

- Lv2
*Note: There must be a tab between SHA1 and filename. In the terminal, enter a tab with `<Ctrl-v><Tab>`*

```bash
git mktree --missing <<EOF
100644 blob ce013625030ba8dba906f756967f9e9ca394464a$(printf '\t')name.ext
100755 blob ce013625030ba8dba906f756967f9e9ca394464a$(printf '\t')name2.ext
EOF
# 58417991a0e30203e7e9b938f62a9a6f9ce10a9a
```

# Viewing a tree

- Lv0

```bash
# Note: Do not print gunzip output directly to the console, or it may stop at \0
printf '\x1f\x8b\x08\x00\x00\x00\x00\x00' \
| cat - ./objects/58/417991a0e30203e7e9b938f62a9a6f9ce10a9a \
| gunzip -dc 2>/dev/null | xxd
# 00000000: 7472 6565 2037 3300 3130 3036 3434 206e  tree 73.100644 n
# 00000010: 616d 652e 6578 7400 ce01 3625 030b a8db  ame.ext...6%....
# 00000020: a906 f756 967f 9e9c a394 464a 3130 3037  ...V......FJ1007
# 00000030: 3535 206e 616d 6532 2e65 7874 00ce 0136  55 name2.ext...6
# 00000040: 2503 0ba8 dba9 06f7 5696 7f9e 9ca3 9446  %.......V......F
# 00000050: 4a                                       J
```

- Lv1

```bash
git cat-file tree 5841 | xxd
# 00000000: 3130 3036 3434 206e 616d 652e 6578 7400  100644 name.ext.
# 00000010: ce01 3625 030b a8db a906 f756 967f 9e9c  ..6%.......V....
# 00000020: a394 464a 3130 3037 3535 206e 616d 6532  ..FJ100755 name2
# 00000030: 2e65 7874 00ce 0136 2503 0ba8 dba9 06f7  .ext...6%.......
# 00000040: 5696 7f9e 9ca3 9446 4a                   V......FJ
```

- Lv2
Use `git ls-tree` to easily view the contents of a directory

```bash
git ls-tree 5841
# 100644 blob ce013625030ba8dba906f756967f9e9ca394464a	name.ext
# 100755 blob ce013625030ba8dba906f756967f9e9ca394464a	name2.ext
```

- Lv3
Use `git show` directly on a tree to see a simplified directory listing

```bash
git show 5841
# tree 5841
#
# name.ext
# name2.ext
```

# Creating a commit

- Lv1

```bash
git hash-object -t commit --stdin -w <<EOF
tree 58417991a0e30203e7e9b938f62a9a6f9ce10a9a
author b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800
committer b1f6c1c4 <b1f6c1c4@gmail.com> 1514736000 +0800

The commit message
May have multiple
lines!
EOF
# d4dafde7cd9248ef94c0400983d51122099d312a
```

- Lv2

```bash
GIT_AUTHOR_NAME=b1f6c1c4 \
GIT_AUTHOR_EMAIL=b1f6c1c4@gmail.com \
GIT_AUTHOR_DATE='1600000000 +0800' \
GIT_COMMITTER_NAME=b1f6c1c4 \
GIT_COMMITTER_EMAIL=b1f6c1c4@gmail.com \
GIT_COMMITTER_DATE='1600000000 +0800' \
git commit-tree 5841 -p d4da <<EOF
Message may be read
from stdin
or by the option '-m'
EOF
# efd4f82f6151bd20b167794bc57c66bbf82ce7dd
```

# Viewing a commit

- Lv0

```bash
# Note: Do not print gunzip output directly to the console, or it may stop at \0
printf '\x1f\x8b\x08\x00\x00\x00\x00\x00' \
| cat - ./objects/ef/d4f82f6151bd20b167794bc57c66bbf82ce7dd \
| gunzip -dc 2>/dev/null | xxd
# 00000000: 636f 6d6d 6974 2032 3539 0074 7265 6520  commit 259.tree 
# 00000010: 3538 3431 3739 3931 6130 6533 3032 3033  58417991a0e30203
# 00000020: 6537 6539 6239 3338 6636 3261 3961 3666  e7e9b938f62a9a6f
# 00000030: 3963 6531 3061 3961 0a70 6172 656e 7420  9ce10a9a.parent 
# 00000040: 6434 6461 6664 6537 6364 3932 3438 6566  d4dafde7cd9248ef
# 00000050: 3934 6330 3430 3039 3833 6435 3131 3232  94c0400983d51122
# 00000060: 3039 3964 3331 3261 0a61 7574 686f 7220  099d312a.author 
# 00000070: 6231 6636 6331 6334 203c 6231 6636 6331  b1f6c1c4 <b1f6c1
# 00000080: 6334 4067 6d61 696c 2e63 6f6d 3e20 3136  c4@gmail.com> 16
# 00000090: 3030 3030 3030 3030 202b 3038 3030 0a63  00000000 +0800.c
# 000000a0: 6f6d 6d69 7474 6572 2062 3166 3663 3163  ommitter b1f6c1c
# 000000b0: 3420 3c62 3166 3663 3163 3440 676d 6169  4 <b1f6c1c4@gmai
# 000000c0: 6c2e 636f 6d3e 2031 3630 3030 3030 3030  l.com> 160000000
# 000000d0: 3020 2b30 3830 300a 0a4d 6573 7361 6765  0 +0800..Message
# 000000e0: 206d 6179 2062 6520 7265 6164 0a66 726f   may be read.fro
# 000000f0: 6d20 7374 6469 6e0a 6f72 2062 7920 7468  m stdin.or by th
# 00000100: 6520 6f70 7469 6f6e 2027 2d6d 270a       e option '-m'.
```

- Lv2
Use `git cat-tree` to easily view the commit's content

```bash
git cat-file commit efd4
# tree 58417991a0e30203e7e9b938f62a9a6f9ce10a9a
# parent d4dafde7cd9248ef94c0400983d51122099d312a
# author b1f6c1c4 <b1f6c1c4@gmail.com> 1600000000 +0800
# committer b1f6c1c4 <b1f6c1c4@gmail.com> 1600000000 +0800
#
# Message may be read
# from stdin
# or by the option '-m'
```

- Lv3
Use `git show` directly on a commit to see the commit and its tree's diff

```bash
git show efd4~
# commit d4dafde7cd9248ef94c0400983d51122099d312a
# Author: b1f6c1c4 <b1f6c1c4@gmail.com>
# Date:   Mon Jan 1 00:00:00 2018 +0800
#
#     The commit message
#     May have multiple
#     lines!
#
# diff --git a/name.ext b/name.ext
# new file mode 100644
# index 0000000..ce01362
# --- /dev/null
# +++ b/name.ext
# @@ -0,0 +1 @@
# +hello
# diff --git a/name2.ext b/name2.ext
# new file mode 100755
# index 0000000..ce01362
# --- /dev/null
# +++ b/name2.ext
# @@ -0,0 +1 @@
# +hello
```

# Finding Tree and Blob from Commit

- Lv2

```bash
# Find the tree corresponding to commit efd4:
# Note: The result is the same as manually writing efd4^{tree}
git ls-tree efd4
# 100644 blob ce013625030ba8dba906f756967f9e9ca394464a	name.ext
# 100755 blob ce013625030ba8dba906f756967f9e9ca394464a	name2.ext
# Find /name.ext in commit efd4 (corresponding tree):
git ls-tree efd4 -- name.ext
# 100644 blob ce013625030ba8dba906f756967f9e9ca394464a	name.ext
```

- Lv3

```bash
# Find the tree corresponding to commit efd4:
# Note: The result is fundamentally different from just writing efd4
git show efd4^{tree}
# tree efd4^{tree}
#
# name.ext
# name2.ext
# Find /name.ext in commit efd4 (corresponding tree):
# Note: The syntax is completely different from git ls-tree:
# git ls-tree takes a tree and optionally finds another tree or tree entry based on path
# git show takes an object and automatically determines its type to display
git show efd4:name.ext
# hello
```

# Creating Tags

Note: You can make a tag point to another tag, though it's not very useful

- Lv1 Can be created by imitating the commit creation method

- Lv2

```bash
git mktag <<EOF
object efd4f82f6151bd20b167794bc57c66bbf82ce7dd
type commit
tag simple-tag
tagger b1f6c1c4 <b1f6c1c4@gmail.com> 1527189535 +0000

The tag message
EOF
# aba3692b60790d098d3f6682555214f3bf09f7da
```

- Lv3
*Special note: The `git tag -a` command not only creates a tag object but also establishes a new reference at `refs/tags/the-tag`.*

```bash
# This command has no output
GIT_COMMITTER_NAME=b1f6c1c4 \
GIT_COMMITTER_EMAIL=b1f6c1c4@gmail.com \
GIT_COMMITTER_DATE='1600000000 +0800' \
git tag -a -m 'The tag message' the-tag efd4:name.ext
# Need to use the following command to find the newly created object
git rev-parse the-tag
# 9cb6a0ecbdc1259e0a88fa2d8ac4725195b4964d
```

# Viewing Tags

- Lv0 Can be viewed by imitating the commit viewing method

- Lv2

```bash
git cat-file tag 9cb6
# object ce013625030ba8dba906f756967f9e9ca394464a
# type blob
# tag the-tag
# tagger b1f6c1c4 <b1f6c1c4@gmail.com> 1600000000 +0800
#
# The tag message
# Note: To view the object pointed to by the tag, just change the type:
git cat-file blob 9cb6
# hello
```

- Lv3

```bash
# Note: git show displays both the tag itself and the information of the object it points to
git show 9cb6
# tag the-tag
# Tagger: b1f6c1c4 <b1f6c1c4@gmail.com>
# Date:   Sun Sep 13 20:26:40 2020 +0800
#
# The tag message
# hello
```

# Checking the file system

Finding and deleting unused objects: (**Some risk, may delete useful things**)
- Lv2

```bash
(git update-ref HEAD efd4)
git count-objects
# 6 objects, 24 kilobytes
# List objects not used in any reference
git fsck --unreachable
# unreachable tag aba3692b60790d098d3f6682555214f3bf09f7da
# List objects not used in any reference or object
git fsck
# dangling tag aba3692b60790d098d3f6682555214f3bf09f7da
# Delete above (**Some risk, may delete useful things**)
git prune
git count-objects
# 5 objects, 20 kilobytes
git fsck --unreachable
git fsck
```

Checking file system integrity:
- Lv2

```bash
mv objects/ce/013625030ba8dba906f756967f9e9ca394464a ../evil
git fsck --connectivity-only
# broken link from     tag 9cb6a0ecbdc1259e0a88fa2d8ac4725195b4964d
#               to    blob ce013625030ba8dba906f756967f9e9ca394464a
# missing blob ce013625030ba8dba906f756967f9e9ca394464a
mv ../evil objects/ce/013625030ba8dba906f756967f9e9ca394464a
git fsck --connectivity-only
```

# "Modifying" Objects

Git objects themselves cannot be modified, but Git provides a mechanism so that we can use a new object to overwrite an existing object,
when accessing the original object will be automatically redirected to the new object.

If we want to replace efd4 with another commit, first create a commit:
```bash
git hash-object -t commit --stdin -w <<EOF
tree 58417991a0e30203e7e9b938f62a9a6f9ce10a9a
parent d4dafde7cd9248ef94c0400983d51122099d312a
author Mx. Evil <evil@gmail.com> 1600000000 -0400
committer Mx. Evil <evil@gmail.com> 1600000000 -0400

OOF.. This is a fake one... hahahaha!
EOF
# 9f3162e7fd9f1d41b704c0064c62714d7e699643
```

## Adding replace

- Lv0

```bash
mkdir -p refs/replace/
echo '9f3162e7fd9f1d41b704c0064c62714d7e699643' >refs/replace/efd4f82f6151bd20b167794bc57c66bbf82ce7dd
```

- Lv2

```bash
(git replace --delete efd4 >/dev/null)
git replace -f efd4 9f31
```

Note: If it forms a loop, it will cause an error
```bash
(git replace --delete efd4 >/dev/null)
git replace -f efd4 9f31
git replace -f 9f31 efd4
git cat-file commit efd4
# fatal: replace depth too high for object efd4f82f6151bd20b167794bc57c66bbf82ce7dd
(git replace --delete 9f31 >/dev/null)
```

- Lv3
No need to create in advance, directly use vim conveniently to modify objects: (At this time, it requires that the new and old object types are consistent)

```sh
git replace --edit efd4
```

## Listing all replaces

- Lv3

```bash
git replace -l --format=long
# efd4f82f6151bd20b167794bc57c66bbf82ce7dd (commit) -> 9f3162e7fd9f1d41b704c0064c62714d7e699643 (commit)
```

## Accessing new and old objects

Unless using Lv0 method or `--no-replace-objects`, accessing efd4 will always be redirected to 9f31:

- Lv2

```bash
git cat-file commit efd4
# tree 58417991a0e30203e7e9b938f62a9a6f9ce10a9a
# parent d4dafde7cd9248ef94c0400983d51122099d312a
# author Mx. Evil <evil@gmail.com> 1600000000 -0400
# committer Mx. Evil <evil@gmail.com> 1600000000 -0400
#
# OOF.. This is a fake one... hahahaha!
# Note--no-replace-objects is a total parameter, not a cat-file itself
git --no-replace-objects cat-file commit efd4
# tree 58417991a0e30203e7e9b938f62a9a6f9ce10a9a
# parent d4dafde7cd9248ef94c0400983d51122099d312a
# author b1f6c1c4 <b1f6c1c4@gmail.com> 1600000000 +0800
# committer b1f6c1c4 <b1f6c1c4@gmail.com> 1600000000 +0800
#
# Message may be read
# from stdin
# or by the option '-m'
```

- Lv3

```bash
git show efd4
# commit efd4f82f6151bd20b167794bc57c66bbf82ce7dd
# Author: Mx. Evil <evil@gmail.com>
# Date:   Sun Sep 13 08:26:40 2020 -0400
#
#     OOF.. This is a fake one... hahahaha!
git --no-replace-objects show efd4
# commit efd4f82f6151bd20b167794bc57c66bbf82ce7dd
# Author: b1f6c1c4 <b1f6c1c4@gmail.com>
# Date:   Sun Sep 13 20:26:40 2020 +0800
#
#     Message may be read
#     from stdin
#     or by the option '-m'
```

## Canceling replace, keeping new and old objects

- Lv0

```bash
rm -f refs/replace/efd4f82f6151bd20b167794bc57c66bbf82ce7dd
```

- Lv3

```bash
(git replace -f efd4 9f31)
git replace --delete efd4
# Deleted replace ref 'efd4f82f6151bd20b167794bc57c66bbf82ce7dd'
```

# Adding Notes to Objects

Git supports adding notes to any object, its essence is a commit, whose tree lists the notes content blob and corresponding object SHA1 (as filename). Each object can have at most one note.

## Adding Notes

- Lv1

```bash
echo 'additional notes' | git hash-object -t blob --stdin -w
# 095f841daf9333f3addfbc44d49efab0be903bfe
git mktree <<EOF
100644 blob 095f841daf9333f3addfbc44d49efab0be903bfe$(printf '\t')efd4f82f6151bd20b167794bc57c66bbf82ce7dd
EOF
# 9b13933df415639aefdd0ac135b9f68fbdad8bac
git hash-object -t commit --stdin -w <<EOF
tree 9b13933df415639aefdd0ac135b9f68fbdad8bac
author author <author@gmail.com> 1234567890 +0800
committer committer <committer@gmail.com> 1514736120 +0800

Notes added by 'git notes add'
EOF
# a692dfc071d3e1043cb69b57d5f43b01335066f3
mkdir -p ./refs/notes/
echo 'a692dfc071d3e1043cb69b57d5f43b01335066f3' >>./refs/notes/commits
```

- Lv3

```bash
GIT_AUTHOR_NAME=author \
GIT_AUTHOR_EMAIL=author@gmail.com \
GIT_AUTHOR_DATE='1234567890 +0800' \
GIT_COMMITTER_NAME=committer \
GIT_COMMITTER_EMAIL=committer@gmail.com \
GIT_COMMITTER_DATE='1514736120 +0800' \
git notes add -f -m 'notes for blob' ce01
```

`git notes edit` will open vim to edit notes.

## Viewing Notes

- Lv2

```bash
git cat-file commit refs/notes/commits
# tree 7a83bc1272e9f212118152c47f239c9b9482d0de
# parent a692dfc071d3e1043cb69b57d5f43b01335066f3
# author author <author@gmail.com> 1234567890 +0800
# committer committer <committer@gmail.com> 1514736120 +0800
#
# Notes added by 'git notes add'
git ls-tree refs/notes/commits
# 100644 blob c5a9a385e3dbe4e65d6db1957bfe18dbf85c517c	ce013625030ba8dba906f756967f9e9ca394464a
# 100644 blob 095f841daf9333f3addfbc44d49efab0be903bfe	efd4f82f6151bd20b167794bc57c66bbf82ce7dd
git cat-file blob 095f
# additional notes
git cat-file blob c5a9
# notes for blob
```

- Lv3

```bash
git notes list
# c5a9a385e3dbe4e65d6db1957bfe18dbf85c517c ce013625030ba8dba906f756967f9e9ca394464a
# 095f841daf9333f3addfbc44d49efab0be903bfe efd4f82f6151bd20b167794bc57c66bbf82ce7dd
git notes show efd4
# additional notes
git notes show ce01
# notes for blob
git show efd4
# commit efd4f82f6151bd20b167794bc57c66bbf82ce7dd
# Author: b1f6c1c4 <b1f6c1c4@gmail.com>
# Date:   Sun Sep 13 20:26:40 2020 +0800
#
#     Message may be read
#     from stdin
#     or by the option '-m'
#
# Notes:
#     additional notes
```

## Deleting Notes

- Lv1

```bash
git ls-tree refs/notes/commits | sed '/efd4f82f6151bd20b167794bc57c66bbf82ce7dd/d' | git mktree
# 121f227d991dbea1913c226305db1aa724ae72df
git hash-object -t commit --stdin -w <<EOF
tree 121f227d991dbea1913c226305db1aa724ae72df
author author <author@gmail.com> 1234567890 +0800
committer committer <committer@gmail.com> 1514736120 +0800

Notes added by 'git notes add'
EOF
# cb132dbe2c9e9f8d684452078ba659242d5b9cb7
git update-ref refs/notes/commits cb132dbe2c9e9f8d684452078ba659242d5b9cb7
git notes list
# c5a9a385e3dbe4e65d6db1957bfe18dbf85c517c ce013625030ba8dba906f756967f9e9ca394464a
```

- Lv3

```bash
# Since a new commit needs to be created, author and committer must be specified
GIT_AUTHOR_NAME=author \
GIT_AUTHOR_EMAIL=author@gmail.com \
GIT_AUTHOR_DATE='1234567890 +0800' \
GIT_COMMITTER_NAME=committer \
GIT_COMMITTER_EMAIL=committer@gmail.com \
GIT_COMMITTER_DATE='1514736120 +0800' \
git notes remove ce01
# Removing note for object ce01
git notes list
```

# Summary

- Lv1
  - `git hash-object -t <type> [--stdin|<file>] -w`
- Lv2
  - `git mktree --missing`
  - `git commit-tree <tree> -m <message> [-p <parent>]*`
  - `git mktag`
  - `git cat-file <type> <SHA1>`
  - `git ls-tree <SHA1> -- [<path>]`
  - `git count-objects`
  - `git fsck [--unreachable] [--connectivity-only]`
  - `git prune` - **Some risk, may delete useful things**
  - `git replace -f <original> <replacement>`
- Lv3
  - `git tag -a -m <message> <name> <object>` - Also create new reference at `refs/tags/<name>`
  - `git show <commit>`
  - `git show <tree>` - Like `HEAD^{tree}`
  - `git show <blob>` - Like `HEAD:index.js`
  - `git replace --edit <original>`
  - `git replace -l --format=long`
  - `git replace --delete <original>`
  - `git notes add | list | show <object> | remove <object>`


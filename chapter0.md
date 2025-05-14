# Basic Concepts

- Git is a famous version control software. It stores versions of software (code + config + tests + ...) in a special database (Git repo). By executing certain commands, users can add, delete, modify, and query versions in the database.
- In most cases, a Git repo is simply a folder on disk.
- For user convenience, each repo can be paired with a worktree (but at most one, and it can be omitted). A worktree is also a folder on disk and is used together with the repo.
- To allow multiple repos to share data, a repo can relinquish ownership of all objects (see Chapter 1) and most references (see Chapter 2), entrusting them to another repo. This so-called linking can only be done on the same machine.
  - Many people think a repo can have multiple worktrees, but strictly speaking, it's actually multiple repos linked to the same repo, each small repo contributing a worktree, making it look like one big repo with multiple worktrees.

# Creating a Git Repo and (Optionally) a Worktree

## Lv0

```bash
# It's just a convention to end a Git repo with .git
mkdir the-repo.git
# Every Git repo must include an objects folder to store objects
mkdir the-repo.git/objects
# Every Git repo must include a refs folder to store normal references
mkdir the-repo.git/refs
# Every Git repo must include a HEAD file (a special reference)
# But the target HEAD points to doesn't have to actually exist
# It's just a convention to point to refs/heads/master at init
echo 'ref: refs/heads/master' > the-repo.git/HEAD
```

At this point, a minimal Git repo is created. Use `git symbolic-ref` (Lv2) to check if it was successful:
```bash
# Git needs to know where the repo is, use --git-dir to specify
# This command will be explained in detail in Chapter 2
git --git-dir=the-repo.git symbolic-ref HEAD
# refs/heads/master
```

Now add a worktree:
```bash
# No complex operation is needed for a worktree
# Any folder can be treated as a worktree
mkdir default-tree
```

Use `git status` (Lv3) to check if it was successful:
```bash
# Git needs to know where the worktree is, use --work-tree to specify
# This command will be explained in detail in Chapter 3
git --git-dir=the-repo.git --work-tree=default-tree status
# On branch master
#
# No commits yet
#
# nothing to commit (create/copy files and use "git add" to track)
```

Specifying the repo and worktree location every time you run a git command is tedious. In most cases, worktree and repo are one-to-one. To simplify command-line usage, you can add a .git file in the worktree:
```bash
echo "gitdir: $(pwd)/the-repo.git" > default-tree/.git
```
This way, Git can find the repo based on the worktree location. However, unfortunately, the following command still fails:
```bash
git --work-tree=default-tree status
# fatal: not a git repository (or any of the parent directories): .git
```
The reason is that `--work-tree` must be used together with `--git-dir`.
The solution is to `cd` into the worktree:
```bash
cd default-tree
git status
# On branch master
#
# No commits yet
#
# nothing to commit (create/copy files and use "git add" to track)
```
Another way is to use `-C`, which means to `cd` first and then run `git`:
```bash
git -C default-tree status
# On branch master
#
# No commits yet
#
# nothing to commit (create/copy files and use "git add" to track)
```

Note that `cd`ing into the repo sometimes gives an error, because currently there's no way to find the worktree from the repo, and some commands require a worktree to work (like `git status`):
```bash
git -C the-repo.git symbolic-ref HEAD
# refs/heads/master
git -C the-repo.git status
# fatal: this operation must be run in a work tree
```

Furthermore, if you want to avoid using absolute paths (so the repo can be moved and still be found), you can put the repo inside the worktree:
```bash
# Remove the .git file
rm default-tree/.git
# Now .git inside the worktree is the repo itself!
mv the-repo.git default-tree/.git

# Check:
git -C default-tree symbolic-ref HEAD
# refs/heads/master
git -C default-tree status
# On branch master
#
# No commits yet
#
# nothing to commit (create/copy files and use "git add" to track)
git -C default-tree/.git symbolic-ref HEAD
# refs/heads/master
# The following command still fails, for the same reason as above
git -C default-tree/.git status
# fatal: this operation must be run in a work tree
```

## Lv3

Daily repo creation, no worktree:
```bash
(rm -rf the-repo.git) # Start over, delete previous repo
git init --bare the-repo.git
# Initialized empty Git repository in /root/the-repo.git/
```

Daily repo creation, with worktree, repo inside worktree:
```bash
(rm -rf default-tree) # Start over, delete previous worktree
git init default-tree
# Initialized empty Git repository in /root/default-tree/.git/
```

Daily repo creation, with worktree, repo and worktree separate:
```bash
(rm -rf the-repo.git default-tree)
git init --separate-git-dir the-repo.git default-tree
# Initialized empty Git repository in /root/the-repo.git/
```

# Add a New Repo and Link to the Original Repo to Achieve "One Repo, Multiple Worktrees"

## Lv0

```bash
(rm -rf the-repo.git default-tree) # Delete previous stuff
(git init --bare the-repo.git) # Now we have a repo but no worktree
# Initialized empty Git repository in /root/the-repo.git/

# Conventionally, put the small repo here:
# Note: although the path says worktrees, it's actually a repo
mkdir -p the-repo.git/worktrees/another/

# To link with the main repo, create a commondir file:
# The file content is the path to the main repo relative to the small repo
echo '../..' > the-repo.git/worktrees/another/commondir

# The small repo doesn't need objects or refs

# The small repo is still a repo, must have HEAD
# Note: if different worktrees' HEADs point to the same ref, changes in one worktree affect the other
# This is legal but defeats the purpose of worktrees
echo 'ref: refs/heads/another' > the-repo.git/worktrees/another/HEAD
```

At this point, a minimal small repo is created. Use `git symbolic-ref` (Lv2) to check if it was successful:
```bash
# Note the git-dir has changed
git --git-dir=the-repo.git/worktrees/another symbolic-ref HEAD
# refs/heads/another

# This also works
git -C the-repo.git/worktrees/another symbolic-ref HEAD
# refs/heads/another
```

Adding a worktree is just as simple as with a normal repo:
```bash
mkdir another-tree
```

Use `git status` (Lv3) to check if it was successful:
```bash
git --git-dir=the-repo.git/worktrees/another --work-tree=another-tree status
# On branch another
#
# No commits yet
#
# nothing to commit (create/copy files and use "git add" to track)
```

Simplifying the command line for the small repo is exactly the same:
```bash
echo "gitdir: $(pwd)/the-repo.git/worktrees/another" > another-tree/.git
git -C another-tree status
# On branch another
#
# No commits yet
#
# nothing to commit (create/copy files and use "git add" to track)
```

Now the small repo knows about the main repo, but the main repo doesn't know about the small repo. This isn't ideal. So we need to register the small repo's location. The benefit is that `git worktree list` (Lv3) can correctly list the small repo:
```bash
# Before registering, git worktree list finds nothing, even if you specify the small repo
git --git-dir=the-repo.git worktree list
# /root/the-repo.git  (bare)
git --git-dir=the-repo.git/worktrees/another worktree list
# /root/the-repo.git  (bare)

# Now register: record the absolute path of the small repo's worktree's .git file in the small repo's gitdir
echo "$(pwd)/another-tree/.git" > the-repo.git/worktrees/another/gitdir

# Strangely, this seems unrelated to the main repo, but it actually works:
git --git-dir=the-repo.git worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]

# Whether you use the main or small repo as git-dir, the result is the same
git --git-dir=the-repo.git/worktrees/another worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]

# Even if the .git file is deleted, the link still temporarily exists:
rm another-tree/.git
git --git-dir=the-repo.git worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]
git --git-dir=the-repo.git/worktrees/another worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]
# But note: running git worktree prune now will delete the entire small repo:
# See the end of this chapter for git worktree prune
git --git-dir=the-repo.git worktree prune
# ls the-repo.git/worktrees/
# # ls: cannot access 'the-repo.git/worktrees/': No such file or directory
# Add .git back to avoid accidental git worktree prune later
(echo "gitdir: $(pwd)/the-repo.git/worktrees/another" > another-tree/.git)
```

Note: If you don't follow the convention of putting the small repo in the main repo's `worktrees/xxx` location, the gitdir file still has to go in the same place, even if it's not a small repo:
```bash
# Create a small repo in a weird location and add a worktree
mkdir -p third-repo.git third-tree
echo '../the-repo.git' > third-repo.git/commondir
echo 'ref: refs/heads/third' > third-repo.git/HEAD
echo "gitdir: $(pwd)/third-repo.git" > third-tree/.git

# Check if the small repo was created successfully
git -C third-tree status
# On branch third
#
# No commits yet
#
# nothing to commit (create/copy files and use "git add" to track)

# Link: put gitdir in the same place as before, even if it's not a small repo
mkdir the-repo.git/worktrees/third/
echo "$(pwd)/third-tree/.git" > the-repo.git/worktrees/third/gitdir

# Check if the main and small repos are linked
git --git-dir=the-repo.git worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]
# /root/third-tree    0000000 (detached HEAD)
git --git-dir=the-repo.git/worktrees/another worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]
# /root/third-tree    0000000 (detached HEAD)
git --git-dir=third-repo.git worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]
# /root/third-tree    0000000 (detached HEAD)
```
You can see that although a small repo not in the usual location can be recognized, `git worktree list` can't correctly show its HEAD content.

## Lv3

You can use `git worktree add` to add a small repo and select a worktree. However, this command requires at least one commit object to work. We won't demonstrate it here. The command syntax is:
```sh
git --git-dir=the-repo.git worktree add [--no-checkout] <worktree> <commit-ish>
```
Here, `--no-checkout` tells git whether to run `git restore -W` after creating the worktree. See Chapter 3.

# Deleting a Small Repo

## Lv0

```sh
# Just delete it
rm -rf the-repo.git/worktrees/another
# gitdir is also deleted in the previous line, no need to do it again
# rm -rf the-repo.git/worktrees/another/gitdir
# This can be skipped, but leaving it may cause confusion
rm -f another-tree/.git
```

## Lv3

```bash
# Delete the object pointed to by the-repo.git/worktrees/another/gitdir
rm -f another-tree/.git
# Actively let git check if each worktree exists;
# If it finds the worktree for the-repo.git/worktrees/another/ is missing,
# it will automatically delete the corresponding small repo (since it's a small repo, little data is lost):
# Note: you can specify the small repo or even another small repo here
git --git-dir=the-repo.git worktree list
# /root/the-repo.git  (bare)
# /root/another-tree  0000000 [another]
# /root/third-tree    0000000 (detached HEAD)
git --git-dir=the-repo.git worktree prune
git --git-dir=the-repo.git worktree list
# /root/the-repo.git  (bare)
# /root/third-tree    0000000 (detached HEAD)
```

# Creating a New Repo Based on Another Repo

Besides creating a blank repo, you can also create a new repo based on another repo. This is very useful: there are many repos on GitHub, and if you can copy them directly or create your own repo based on them, you can make modifications on top of others' work. This involves remote file transfer, which will be covered in Chapter 5.

# Summary

(The following are all Lv3)
- Create an empty repo, optionally with a worktree
  - `git init --bare <repo>` - no worktree
  - `git init --separate-git-dir <repo> <worktree>`
  - `git init <worktree>` - repo is at `<worktree>/.git`
- "Single" repo with multiple worktrees
  - `git worktree list`
  - `git worktree add [--no-checkout] <worktree> <commit-ish>`
  - `git worktree prune`

# Further Reading

[gitrepository-layout](https://git-scm.com/docs/gitrepository-layout)


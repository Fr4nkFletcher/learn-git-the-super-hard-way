# Git Super Hard Way Cheatsheet

## Initialization

```bash
git init --bare <repo>
git init <worktree>
git init --separate-git-dir <repo> <worktree>
```

## Objects

```bash
git hash-object -t blob --stdin -w
git cat-file blob <sha1>
git mktree --missing
git cat-file tree <sha1>
git commit-tree <tree> -p <parent>
git cat-file commit <sha1>
```

## References

```bash
git update-ref <ref> <sha1>
git symbolic-ref HEAD <ref>
git show-ref
git update-ref -d <ref>
```

## Index

```bash
git update-index --add --cacheinfo <mode> <sha1> <path>
git add <path>
git update-index --remove <path>
git restore --staged <path>
git ls-files --stage
```

## HEAD

```bash
git symbolic-ref HEAD <ref>
git switch <branch>
git checkout <branch>
git switch --detach <sha1>
git checkout --detach <sha1>
```

## Remotes

```bash
git remote add <name> <url>
git fetch <name>
git pull <name> <branch>
git push <name> <branch>
```

## Merges

```bash
git merge <branch>
git status
git add <file>
git commit
```

## Rebase

```bash
git rebase <branch>
git rebase -i <branch>
git rebase --abort
git rebase --skip
```

## History

```bash
git log
git log --grep='search-term'
git log -- <file>
git branch --contains <commit>
```

## Submodules

```bash
git submodule add <url> <path>
git submodule init
git submodule update
git clone --recurse-submodules <url>
git submodule update --remote
```

## Batch Processing & Automation

```bash
git ls-files | xargs -n 1 <command>
git rev-list --all | xargs -n 1 <command>
# Use hooks:
# Place your script in .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

## Configuration & Aliases

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
git config --list
```

## Advanced Usage

```bash
git commit -S -m "Signed commit message"
git tag -s v1.0 -m "Signed version 1.0"
git verify-commit <commit>
git verify-tag <tag>
git notes add -m "This is a note for the commit"
git notes show <commit>
```

## Extensions

```bash
# Hooks
# Place your script in .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit

# Attributes

echo '*.txt text' > .gitattributes
git add .gitattributes
git commit -m "Add gitattributes"

# LFS

git lfs install
git lfs track "*.psd"
git add .gitattributes
```


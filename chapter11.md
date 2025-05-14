# Basic Concepts

Git allows you to configure various settings and create aliases for commands to improve your workflow.

# Configuring Git

- Lv2

```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

# Creating Aliases

- Lv2

```bash
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

# Viewing Configuration

- Lv2

```bash
git config --list
```

# Summary

- Use `git config` to set user information and create aliases
- Use `git config --list` to view all configuration settings

# 全局gitignore

```bash
cat - >~/.gitignore <<EOF
*~
*.swp
*.swo
EOF
git config --global core.excludesfile ~/.gitignore
```

# Lv3, Lv4

* [shared-git-config](https://github.com/b1f6c1c4/shared-git-config)

# Lv5

（摘自第6章）
有一类merge情况是，需要用其他分支 *完全取代* 当前分支的某一目录。
```sh
git-mnfss() {
  git rm --cached -r -- $1
  git read-tree --prefix $1/ $1
  git checkout-index -fua
  git clean -f -- $1
  git reset --soft $(echo "Merge branch $1" | git commit-tree $(git write-tree) -p HEAD -p $1)
}
```

* [git-freeze](https://github.com/b1f6c1c4/git-freeze)
* [git-get](https://github.com/b1f6c1c4/git-get)

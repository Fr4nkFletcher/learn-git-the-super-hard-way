# Basic Concepts

Git supports various advanced features and extensions to enhance your workflow and integrate with other tools.

# Using Git Hooks

- Lv2

```bash
# Place your script in .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

# Using Git Attributes

- Lv2

```bash
echo '*.txt text' > .gitattributes
git add .gitattributes
git commit -m "Add gitattributes"
```

# Using Git LFS (Large File Storage)

- Lv2

```bash
git lfs install
git lfs track "*.psd"
git add .gitattributes
```

# Summary

- Use Git hooks for automation
- Use Git attributes to control file handling
- Use Git LFS for large files

#git

# 查询 commit changes 包含指定关键字

```bash
git log -S '<keyword>' --format='%h %s' --source --all
```

# 显示 unicode

```bash
git config --global core.quotePath false
```

# 清理 git log

If you want to free some space in your git repo, but do not want to rebuild all your commits (rebase or graft), and still be able to push/pull/merge from people who has the full repo, you may use the git clone shallow clone (--depth parameter).

Clone the original repo into limitedRepo

```bash
git clone file:///path_to/originalRepo limitedRepo --depth=10
# Remove the original repo, to free up some space
rm -rf originalRepo
cd limitedRepo
git remote rm origin
```

You may be able to shallow your existing repo, by following these steps:

```bash
# Shallow to last 5 commits
git rev-parse HEAD~5 > .git/shallow

# Manually remove all other branches, tags and remotes that refers to old commits

# Prune unreachable objects
git fsck --unreachable ; Will show you the list of what will be deleted
git gc --prune=now     ; Will actually delete your data
```

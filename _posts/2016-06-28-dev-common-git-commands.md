---
layout: post
title: dev - common git commands
description: common git commands
modified: 2016-06-28
tags: [dev]
comments: true
image:
  feature: abstract-11.png
---
Git commands are very simple and easy. But little bit confusing. 

## Common Git Commands

### Github commands:

- git clone <git-url-goes-here>: Clone a Git repository to work with.
- git status: Get the status of files in the current repo.
- git add . : Add all changes to all files at your current working directory location. Must be a git project.
- git checkout -- . : Revert all unstaged files (before commit)
- git checkout testing: Switch to testing branch.
- git commit -m "Some changes": Commit changes with comments to current working branch.
- git checkout master: Checkout the master branch (trunk).
- git checkout gh-pages: Checkout the gh-pages branch.
- git log: View commit logs for your Git branch.
- git pull: Pull down any changes that were checked into a branch (Git repository).
- git push: Push your committed changes to your currently checked out branch.
- git push origin master: Push your committed changes up to your master branch. 'master' may be subsituted for another branch.

### Git examples:

**Merging a branch into the master**

```bash
git checkout -b 'hotfix'
# Switched to new branch "hotfix"
vim index.html
# Check the git status
git status
git commit -a -m 'Fixed the wrong URL'
...
...
git checkout master
git merge hotfix
```

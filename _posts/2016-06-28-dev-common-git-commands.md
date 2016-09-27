---
layout: post
title: Git - common git commands
description: Git - common git commands
modified: 2016-06-28
tags: [git]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Git commands are very simple and easy. But little bit confusing. 

## Common Git Commands

### Github commands

- git clone <git-url-goes-here>: 서버의 내용을 로컬로 가져와서 로컬 repository로 등록
- git status: 현재 로컬 repository의 파일들의 싱크 상태 확인
- git add . : (commit 이전에 commit을 위해) 현재 작업하고 있는 로컬 디렉토리의 모든 파일들의 변화(추가, 수정, 삭제 등)을 등록 
- git checkout -- . : stage에 올라가지 않은 수정된 파일들을 (즉, commit하기 이전에) 모두 수정 이전으로 되돌리기 
- git branch <branch이름>: branch 만들기 
- git checkout <branch 이름>: Local repository의 Branch를 전환
- git checkout master: Local repository의 Branch를 master branch (trunk)로 전환
- git commit -m "Some changes": 현재 작업하고 있는 branch에 이전과 달리진 파일 변화를 코맨트와 함께 commit
- git log: 현재 Git branch의 commit 로그 확인
- git pull: Pull down any changes that were checked into a branch (Git repository).
- git push: Push your committed changes to your currently checked out branch.
- git push origin master: 현재 로컬 repository의 commit된 내용을 서버의 master branch로 Push
- git merge <branch 이름>: branch 이름으로 지정된 branch의 변경을 현재 작업 중인 branch에 merge. 현재 작업 중인 branch와 다른 내용(commit)을 현재 작업 중인 branch의 새로은 변경으로 commit
- git rebase <branch 이름>: 현재 작업 중인 branch를 지정된 branch와 일렬로 합침. 합친 후에도 작업 branch는 현재 branch를 유지

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

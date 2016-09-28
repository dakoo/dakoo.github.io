---
layout: post
title: Git - 명령어 정리
description: Git - 명령어 정리
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

git 명령어를 알아보자. 

### 1. 기본

#### 1.1 local repository 생성 

- git clone <git-url-goes-here>: 서버의 내용을 로컬로 가져와서 로컬 repository로 등록

#### 1.2 local repository에서 작업하기

- git status: 현재 로컬 repository의 파일들의 싱크 상태 확인
- git add . : (commit 이전에 commit을 위해) 현재 작업하고 있는 로컬 디렉토리의 모든 파일들의 변화(추가, 수정, 삭제 등)을 등록 
- git checkout -- . : stage에 올라가지 않은 수정된 파일들을 (즉, commit하기 이전에) 모두 수정 이전으로 되돌리기 
- git commit -m "Some changes": 현재 작업하고 있는 branch에 이전과 달리진 파일 변화를 코맨트와 함께 commit
- git log: 현재 Git branch의 commit 로그 확인

#### 1.3 branch 만들고 branch간 전환하기

- git branch <branch이름>: branch 만들기 
- git checkout <branch 이름>: Local repository의 Branch를 전환
- git checkout master: Local repository의 Branch를 master branch (trunk)로 전환

#### 1.4 local repository와 remote간 sync

- git pull: Pull down any changes that were checked into a branch (Git repository).
- git push: Push your committed changes to your currently checked out branch.
- git push origin master: 현재 로컬 repository의 commit된 내용을 서버의 master branch로 Push

### 2. branch간 병합

#### 2.1 git merge 사용하기 

- git merge <branch 이름>: branch 이름으로 지정된 branch의 변경을 현재 작업 중인 branch에 merge. 현재 작업 중인 branch와 다른 내용(commit)을 현재 작업 중인 branch의 새로은 변경으로 commit

#### 2.2 git rebase 사용하기 

- git rebase <branch 이름>: 현재 작업 중인 branch를 지정된 branch와 일렬로 합침. 합친 후에도 작업 branch는 현재 branch를 유지. 커밋들의 흐름을 보기 좋게 한 줄로 만들 수 있다는 장점이 있음(저장소의 커밋 로그와 이력이 깨끗해짐)

### 3. 현재 작업하는 commit(HEAD) 전환

HEAD는 default로는 최종 commit을 가리킨다. 

#### 3.1 직접 위치 지정하여 전환

- git checkout <commit의 해쉬값>: 현재 branch의 특정 commit에 HEAD 이동

#### 3.2 상대 참조 이용하여 전환

- git checkout <branch 이름>^: branch의 가장 최신 commit보다 하나 이전 commit에 HEAD 이동
- git checkout <branch 이름>^^: branch의 가장 최신 commit보다 둘 이전 commit에 HEAD 이동
- git checkout <branch 이름>~숫자: branch의 가장 최신 commit보다 숫자 이전 commit에 HEAD 이동
- git checkout HEAD^: 현재 HEAD가 가리키는 commit보다 하나 이전 commit에 HEAD 이동
- git checkout HEAD^^: 현재 HEAD가 가리키는 commit보다 둘 이전 commit에 HEAD 이동
- git checkout HEAD~숫자: 현재 HEAD가 가리키는 commit보다 숫자 이전 commit에 HEAD 이동

### 4. Branch 수정하기

#### 4.1 직접 위치 지정하여 Branch가 가리키는 위치 조정하기

git branch -f <branch이름> <commit의 해쉬값>: 특정 branch를 특정 commit으로 강제로 옮겨서 재지정

#### 4.2 상대 참조 이용하여 Branch가 가리키는 위치 조정하기

- git branch -f <branch이름> HEAD~숫자: 특정 branch를 현재 HEAD로부터 숫자만큼 이전으로 강제로 옮겨서 재지정

### 5. 현재 작업하는 branch의 작업 내용 되돌리기 

#### 5.1 git reset  

브랜치로 하여금 예전의 커밋을 가리키도록 이동시키는 방식. 마치 commit을 하지 않은 것처럼 예전 commit으로 branch를 이동 시킴

- get reset head^: 현재 HEAD하나 이전으로 HEAD가 돌아가고 이전 히스토리는 사라짐

#### 5.2 git revert  

리모트 branch에 reset을 사용할 수 없으므로 사용되는 것. 변경 내용을 되돌리고 되돌린 사항을 다른 사람들과 공유

- git revert HEAD: 현재 HEAD 이전의 내용으로 다시 commit하고 HEAD가 그것을 가리킴

### 6. 다른 branch 수정 사항 중 일부만 현재 branch에 반영

#### 6.1 어떤 commit을 반영해야 할 지 명확히 알때 

cherry-pick 사용

- git cherry-pick <Commit1> <Commit2> <...> : 특정 commit의 수정사항을 현재 branch로 복사. 여기서는 c1, c2가 현재 branch에 c1', c2'로 추가 됨

#### 6.2 원하는 commit을 잘 모를때 

interactive rebase 사용

- git rebase -i HEAD~4  : 현재 HEAD에서 4개 앞 commit까지 보여주고 복사될 commit들을 보여주고 선택하게 함. 이를 통해 commit 순서, 반영 여부(pick 기능), commit 병합을 진행


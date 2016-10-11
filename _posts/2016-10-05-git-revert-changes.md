---
layout: post
title: Git - 수정한 것 되돌리기
description: Git - 수정한 것 되돌리기
modified: 2016-10-05
tags: [git]
comments: true
image:
  feature: algorithm.jpeg
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

Git을 사용하다보면 수정한 내용을 되돌리고 싶을 경우가 간혹있다. GUI가 있는 Git 클라이언트의 경우엔 discard를 하면 되지만 command line interface에서는 어떻게 해야 할지 잘 모를때가 많다. 
각 상황별로 수정 내역을 되돌리는 법을 알아보자. 

### 1. git add 명령을 하기 이전(stage에 올리지 않은 경우)

#### 1.1 repository 내 모든 수정 되돌리기 

```bash
$ cd {repository_root_dir}
$ git checkout .
```

#### 1.2 특정 폴더 아래의 모든 수정 되돌리기 

```bash
$ git checkout {dir}
```

#### 1.3 특정 파일의 수정 되돌리기 

```bash
$ git checkout {file_name}
```

### 2. git add 명령으로 stage에 올린 경우

```bash
$ git reset
```

### 3. git commit을 한 경우

#### 3.1 commit 내용을 없애고 이전 상태로 원복

master 브랜치의 마지막 커밋을 가리키던 HEAD를 그 이전으로 이동시켜서 commit 내용을 없앰

```bash
$ git reset --hard HEAD^
```

#### 3.2 commit은 취소하고 commit 했던 내용은 남기고 unstaged 상태로 만들기

```bash
$ git reset HEAD^
```

#### 3.3 commit은 취소하고 commit 했던 내용은 남기고 staged 상태로 만들기

```bash
$ git reset --soft HEAD^
```

### 4. 모든 untracked 파일들을 지우기

```bash
git clean -fdx
```

### 5. git push를 한 경우 remote repository도 이전으로 되돌리기 

```bash
$ git reset HEAD^  #local repository에서 commit을 하나 되돌림
$ git commit -m "..."  #되돌린 것으로 commit
$ git push origin +master #remote repository를 강제로 revert
```

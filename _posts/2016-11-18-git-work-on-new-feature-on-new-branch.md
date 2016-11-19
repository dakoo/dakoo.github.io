---
layout: post
title: Git - 새로운 feature를 새로운 브랜치에서 작업하기
description: Git - 새로운 feature를 새로운 브랜치에서 작업하기
modified: 2016-11-18
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

### 1. 문제 

git stash/git stash pop은 수정하고 있던 파일을 잠깐 저장해 두고 다른 작업을 하기에 용이하다. 예를 들어 A feature를 개발하고, B feature를 개발하고 있는 도중 A feature의 review comment를 해결하기 위해 현재 수정사항을 잠깐 묻어두기에 적합하다. 
하지만 아래와 같은 문제를 해결할 수 없다. 

1. Conflict이 발생할 가능성이 높다. B feature의 개발이 상당히 진전되어 많은 파일을 건드렸는데, review comment를 해결하기 위해 광범위해서 많은 파일을 수정하게 된 경우를 생각해 보자. 
review comment를 해결한 내용을 올리기 위해서는 commit을 해야 하는데 이렇게 되면 stash로 임시 저장한 파일들과 충돌이 일어날 가능성이 높다. 충돌이 문제가 아니라 사실 B feature는 아직 공식적으로 history에 없는 상황이므로 충돌 해결 중에 문제가 발생하거나, 꼬이게 되면 엄청나게 귀찮은 상황이 올 수 있다. 
2. B feature를 commit해서 점진적으로 개발하기 어렵다. A feature가 완전히 완료된 것이 아니면 언제 review comment가 들어올 지 모르기 때문에 B feature를 commit할 수 없다. 자칫 A feature의 코드와 B feature의 코드가 섞일 수 있다. 

### 2. 해결책: 새로운 feature를 새로운 브랜치에서 작업하기

#### 2.1 작업하던 파일을 가지고 새로운 브랜치로 옮겨가서 작업하기 

아래 명령으로 새로운 branch를 만들자. 

```bash
git checkout -b <new branch>
```

수정사항을 그 브랜치에 commit하고 원 브랜치로 다시 돌아온다. 

```bash
git commit -a -m "comment"
git checkout <original branch>
```

#### 2.2 원래 브랜치의 변경 사항을 새로운 브랜치에도 반영하기

이제 원래 브랜치에도 수정사항이 생기고 새로운 브랜치도 작업을 하느라 수정 사항이 계속 생긴 경우를 가정하자. 
프로젝트를 하다보면 다른 사람의 수정사항을 중간에 패치해야 하는 경우 등이 생긴다. 이런 경우 원래 브랜치에서 원격 저장소로부터 pull을 해야 하고, 그 수정사항을 다시 새로운 브랜치에 적용해야 한다. 

상당히 골치 아픈 경우이다. 왜냐하면 새로운 브랜치의 수정사항을 원래 브랜치에 결국은 합쳐야 하는데 이때 conflict이 날 수 있기 때문이다. 

원격 저장소의 수정사항이 생긴 것으로 가정하자. 원 브랜치에서는 pull을 해서 적용하면 된다. conflict이 나면 적당히 resolve한 후 commit한다. 
새로 만든 브랜치에서는 pull을 할 수 없으므로 cherrypick을 한다. 

새로운 브랜치에서는 원격 저장소에서 가져오고자 하는 commit을 확인한다. 그 다음 cherry-pick을 통해 가져온다. 


```bash
git cherry-pick <commit의 hash>
```

cherry-pick은 range를 통해 가져올 수 있다. 

```bash
git cherry-pick <더 이전 commit의 hash> <이전 commit의 hash>
```

cherry-pick도 당연히 conflict이 발생할 수 있다. 이때 표시된 파일을 열고 conflict 난 부분을 해결하고 다음 명령으로 더 진행한다. 

```bash
git cherry-pick --continue
```

#### 2.3 원래 브랜치로 수정사항 합치기

개념은 단순하다. 원 브랜치에서 새로운 브랜치의 수정사항을 merge하는 것이다. 
그런데 주의할 것은 원격 저장소의 변경 사항을 먼저 가져와서 합치고 난 후 새로운 브랜치를 가져오는 것이 좋다는 것이다. 


```bash
git checkout <원브랜치>
git pull origin <원브랜치>
### 충돌 해결
### 테스트
### add/commit
git merge <새로운 브랜치>
### 충돌 해결
### 테스트
### add/commit
git push origin <원브랜치>
```

합칠 때마다 conflict을 해결해야 하고 테스트하고 add/commit해야 한다는 사실을 명심하자. 



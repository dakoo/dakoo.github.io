---
layout: post
title: Git - pull request
modified: 2016-10-04
tags: [git]
---

아직은 헷갈리는 git pull request에 대해 살펴보자. pull request는 git 자체가 제공하는 것이 아니라 github와 같은 git기반 서비스 업체가 제공하는 기능이다.

### pull request 순서

- 1단계(fork): git 서비스가 제공하는 UI를 이용해 프로젝트 저장소를 fork하여 자신의 원격 저장소를 만든다. https://project-repository/repo.git를 fork하여 https://user-repository/repo.git가 생겼다고 가정하자. 
- 2단계(clone): 자신 계정의 원격 저장소를 clone을 해서 로컬 저장소를 만든다. 이렇게 하면 자신의 원격 저장소가 origin이라는 alias를 가지게 되고 로컬은 master 브랜치가 된다. 그리고 현재 작업 공간은 master 브랜치를 가리키게 된다. 

```bash
$ git clone https://user-repository/repo.git
$ git remote -v// origin이 보여야 함
```

- 3단계(remote add): 로컬 저장소에 원본 프로젝트 저장소를 추가한다. 이때 이 원본 프로젝트 저장소의 alias를 부여해 origin과 구분한다. 

```bash
$ git remote add parent https://project-repository/repo.git
$ git remote -v //확인 - origin과 parent가 보여야 함
```

- 4단계(feature branch): 로컬에서 master 브랜치는 동기화 용으로 사용하고 작업하는 feature별로 브랜치를 만들어 checkout하여 작업 공간의 브랜치를 전환한다. 

```bash
$ git checkout -B hotfix
```

- 5단계(implementation): feature를 구현한다. 

```bash
$ git add new_file.txt
$ git commit -a -m "Fix typo in README file"
```

- 6단계(remote fetch): git fetch 이름 명령을 이용해서 원 프로젝트의 변경 내용을 가져온다. 

```bash
$ git fetch parent
```

- 7단계(remote merge): git merge parent/master를 해서 현재 hotfix 브랜치와 원래 프로젝트의 master branch를 머지한다.

```bash
$ git merge parent/master
```

- 7-1단계(conflict handling - optional): 충돌해결 만약 conflict이 났다는 메시지가 보이면 `git status` 명령을 이용해서 확인한다. 충돌이 일어난 파일은 unmerged로 표시다 되며, 이는 수동으로 수정한다. `git mergetool` 명령을 이용해 툴을 호출할 수 있다. 이때 mac의 경우는 opendiff가 실행된다. 수정이 완료가 되면 `git add` 명령을 이용해 반영한다. 그 뒤 `git commit`을 이용해 반영한다. 즉 충돌이 일어난 경우 다음 순서를 따른다. 

```bash
$ git status //문제 확인
$ git mergetool
//수정
$ git add
$ git commit
$ git status //해결 되었음이 보여야 함
```

- 8단계(verification): 문제가 없이 머지가 되었고, 부작용도 없음을 확인한다. 
- 9단계(push): git push <원격저장소명> <로컬브랜치명>:<원격브랜치명>으로 원격저장소에 새로운 브랜치를 생성하면서 푸시한다. 

```bash
$ git push origin hotfix/hotfix
$ git branch -r //origin/hotfix가 보여야 한다. 
```

- 10단계(branch clear): 로컬의 branch를 제거한다. 

```bash
$ git branch -d hotfix
```

- 11단계(pull request): 수정된 내용으로 pull request한다. github와 같은 서비스가 제공하는 UI를 이용한다. 
- 12단계(code review): Pull request에 대해 review를 한다. github와 같은 서비스가 제공하는 UI를 이용한다. 
- 13단계(push): Pull request가 원본 프로젝트에 merge되면 내 원격 repository에 올린다. 

### 참고할 만한 git 명령

- branch 모두 확인 

```bash
$ git branch -r
```

- 원격 저장소 변경 내용 확인

```bash
$ git remote show [이름]
```

- 원격 저장소 alias 제거하기 

```bash
$ git remote rm 이름
```

- 원격 저장소 alias 변경하기 

```bash
$ git remote rename [old_name] [new_name]
```

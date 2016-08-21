---
layout: post
title: Git - 프로젝트 생성의 모든 것
description: Git - 프로젝트 생성의 모든 것
modified: 2015-07-04
tags: [git]
comments: true
image:
  feature: abstract-12.png
---
은근히 잘 이해가 안되는 git 프로젝트 생성에 대해 알아보자. 

기억할 것은 다음과 같다. 

- Local Repository는 `git init`으로 만든 후 사용하면 된다. 
- Local Repository를 Remote Repository로 만들어 관리하려면 Remote를 만든 후 Push하면 된다. 

자세한 내용은 아래를 참조하자. 

### 0. 사전 설정

- git은 설치되어 있다고 가정한다.

```
sudo apt-get update
sudo apt-get install git
```

- global configration 설정: remote project와 무관하다. 

```
git config --global user.name "Your Name"
git config --global user.email "your_email@whatever.com"
```

### 1. LOCAL GIT PROJECT 만들기

LOCOL GIT PROJECT는 쉽게 말해 Local Repository이다. 

#### 1.1 빈 폴더와 함께 Git Project 만들기 

1. 빈 폴더를 만들고 이동
2. git init
3. 이후 작업은 동일 (git add . , git status, git commit -m "xxx")

- GUI Tool에서는 Open a Repository로 해당 폴더를 열면 된다. 

#### 1.2 비어있지 않은 폴더와 함께 Git Project 만들기 

이것은 사실 빈 폴더를 가지고 만드는 것과 동일하다. `git init` 후에 `git add .`과 `git commit -m "xxx"`를 통해 존재하는 파일들을 추가해 주면 된다. 

1. 프로젝트를 만들고자 하는 폴더로 이동
2. git init
3. git add .
4. git commit -m "xxx"
5. 이후 작업은 동일 (git add . , git status, git commit -m "xxx")

- GUI Tool에서는 Open a Repository로 해당 폴더를 열면 된다. 

### 2. REMOTE GIT PROJECT 만들기

여기서는 Github를 기준으로 설명한다. Remote Repository 역할을 Github가 하는 것이다. 

#### 2.1 아무것도 없는 상태의 Remote Git Project 만들기 

##### 2.1.1 github에서 프로젝트 만들기 

1. Github의 계정으로 로그인 
2. Create a new repository를 선택
3. Repository name을 선정하고 description 입력 후 **Create repository**
4. 생성된 https URL을 복사

username은 브라우저 탭에 표시된다. 예를 들어 dakoo(Hochul Shin)의 dakoo.

##### 2.1.2 로컬 

로컬은 일단 무조건 local repository를 만들어야 한다. 

1. git init 
2. git remote add origin [위에서 복사한 remote repository URL]

파일이 추가되기 전까지는 할 일이 없다. 파일이 추가되면...

1. git add .
2. git commit -m "xxx"
3. git push -u origin master
4. username과 password를 입력한다.

- push할때 마다 username과 password를 입력하는 것을 피하려면 GUI 툴을 사용하자. 
- GUI Tool에서는 Open a Repository로 해당 폴더를 열면 Remote Repository 정보까지 모두 가져와서 표시한다. 

#### 2.2 Local에 있는 비어있지 않은 폴더와 함께 Git Project 만들기

로컬 Reposotiry에서는 원격의 빈 Repository에 push를 한번 해서 sync를 맞추는 개념이다. 즉, 로컬의 폴더를 먼저 local repository로 만든 후, push를 하면 된다. 

##### 2.2.1 github에서 프로젝트 만들기 

1. Github의 계정으로 로그인 
2. Create a new repository를 선택
3. Repository name을 선정하고 description 입력 후 **Create repository**
4. 생성된 https URL을 복사

##### 2.2.2 로컬 

로컬은 일단 무조건 local repository를 만들어야 한다. 

1. git init 
2. git remote add origin [위에서 복사한 remote repository URL]

기존이 파일들을 commit한다.  

1. git add .
2. git commit -m "xxx"

이제 push한다. 

1. git push -u origin master
2. username과 password를 입력한다.

#### 2.3. Local에 있는 Git Project를 이용해 Remote Git Project 만들기 

여기까지 봤으면 이건 아무것도 아님을 알 것이다. 로컬의 repository를 push하면 된다. 

##### 2.3.1 github에서 프로젝트 만들기 

1. Github의 계정으로 로그인 
2. Create a new repository를 선택
3. Repository name을 선정하고 description 입력 후 **Create repository**
4. 생성된 https URL을 복사

##### 2.3.2 로컬 

로컬에는 이미 repository가 있다면, 

1. local repository의 root folder로 이동한다. 
2. git remote add origin [위에서 복사한 remote repository URL]
3. git push -u origin master
4. username과 password를 입력한다.

### 3. REMOTE GIT PROJECT 가져오기

Remote Repository를 이용해 local repository를 만드는 것은 다음과 같다. 

1. Remote Repository의 URL을 복사한다. 
2. git clone [remote repository URL]

git remote add origie ... 명령없이도 push가 가능하다!


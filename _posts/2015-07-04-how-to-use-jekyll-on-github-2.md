---
layout: post
title: Windows에서 Jekyll 이용해 Github 블로그 만들기(2)
description: Windows에서 Jekyll 이용해 Github 블로그 만들기 두번째 
modified: 2015-07-04
tags: [blog]
categories: blog
image:
  feature: abstract-10.png
---
Github에 블로그를 만들어 보자. 이를 위해 Windows에 jekyll 환경을 구축해 jekyll 테마를 적용하고, Github에 올려 블로그를 운영하는 방법을 정리해 본다. 이 글은 두번째 순서로 Github에 저장소를 만들고, 자신 PC에서 작업한 내용을 반영하는 방법을 설명한다. 

[첫번째 글: Windows에서 Jekyll 이용해 Github 블로그 만들기(1)](http://dakoo.github.io/blog/how-to-use-jekyll-on-github-1/)

## Github 블로그 저장소(Repository) 만들기

### 계정 만들기 
[Github](www.github.com)에 접속해서 새로운 계정을 만든다. 이미 계정이 있다면 이 단계는 넘어가자.

### Github에 블로그 저장소 만들기 
[Github](www.github.com)에서 New Repository를 선택한다. 

<figure>
	<img src="/images/github_new_repository.PNG" alt="">
</figure>

Repository 생성 페이지에서 repository 이름을 아래와 같이 정하고 **Create repository**를 선택한다. 
 
>자기아이디.github.io 

<figure>
	<img src="/images/github_new_repository2.jpg" alt="">
</figure>

<u>자기아이디.github.io</u>로 repository를 만들면 http://자기아이디.github.io라는 경로의 블로그가 생기게 된다. 블로그의 버전을 관리하는 Github 프로젝트 프로토콜은 이디이와 패스워드 만으로도 사용할 수 있는 **https** 프로토콜로 선택한다. 이 경우 경로는 다음과 같다. 

>https://github.com/자기아이디/자기아이디.github.io.git
    
<figure>
	<img src="/images/github_new_repository3.PNG" alt="">
</figure>


## Git 클라이언트 설치와 설정

### git-scm 설치

git-scm은 Git 클라이언트들을 위한 기본 라이브러리를 제공한다. 다른 git 클라이언트 설치 이전에 기본으로 설치되어야 한다. [git-scm](https://git-scm.com/downloads) 설치 파일을 다운로드해서 설치한다. 설치 중 PATH 설정 창에서 *Use Git from the Windows Command Prompt*를 선택하고 그 외는 default로 설치한다. 

<figure>
	<img src="/images/git-scm.PNG" alt="">
</figure>


### SourceTree 설치

SourceTree는 최고의 공짜 GUI Git 클라이언트이다. 이것으로 작업해 보자. 
[SourceTree](https://www.sourcetreeapp.com/download/) 설치 파일을 다운로드받아 설치한다. 참고로, .Net Framework이 설치되어 있지 않은 경우, 자동으로 .Net Framework을 설치한 후 설치가 진행된다. 

### SourceTree 기본 설정

SourceTree의 아이콘 메뉴에서 터미널을 선택하여 실행한다. 터미널 창에서 `git config --global user.email "사용자 이메일"`과 `git config --global user.name "사용자 이름"` 명령을 실행하여  계정에 대한 기본 정보를 입력한다. 

<figure>
	<img src="/images/sourcetree-account.PNG" alt="">
</figure>

## git local repository 설정

PC의 블로그 폴더를 git local repository로 설정한다. SourceTree를 기준으로 설명한다. 

1. 메뉴에서 파일> 복제/생성 선택  
2. 저장소 설정 창이 뜨면 '새 저장소 생성' 탭으로 이동
3. 목적지 경로에서 블로그의 루트 폴더 선택
4. 나머지는 모두 그대로 두고 생성하기를 선택
5. **스테이지에 올라가지 않은 파일**을 선택해서 모두 **스테이지에 올라간 파일**로 이동시킴 (만약 반영하고 싶지 않은 파일이 있다면 블로그 폴더의 .gitignore 파일에 추가해 놓는다)
6. 메시지 커밋 창(화면 아래편)에 수정 내용을 기록한 후 **커밋**을 실행
 
## remote repository 설정

SourceTree에서 Github repository를 git remote repository로 설정한다.

1. 상단 우측의 **설정**을 선택 
2. 원격 탭에서 **추가**를 선택

<figure>
	<img src="/images/sourcetree-remoterepository.PNG" alt="">
</figure>

3. Github의 repository를 중심으로 버전관리하기 위해 **디폴트 원격**을 설정
4. **URL/경로**에 github의 경로인 아래 경로를 입력

>https://github.com/자기아이디/자기아이디.github.io.git

5. 호스트 종류는 github로 선택하고, 사용자명과 패스워드를 입력하고 확인

<figure>
	<img src="/images/sourcetree-remoterepository2.PNG" alt="">
</figure>

## github에 푸시
local repository(PC의 블로그 폴더)의 내용을 remote repository(github)에 반영하기 위해 푸시를 한다. SourceTree의 메뉴에서 **푸시**를 선택한다. **이때 반드시 github를 마스터로 잡아야 한다. **

성공적으로 완료되면 SourceTree에 local과 remote repository가 함께 표시된다. 

<figure>
	<img src="/images/sourcetree-remoterepository3.PNG" alt="">
</figure>

## 확인

### github repository의 업데이트 확인
github의 repository 내용을 확인해서 업데이트가 잘 되었는지 확인한다. 최근 푸시된 내용이 보여야 한다. 

>https://github.com/자기아이디/자기아이디.github.io.git

### 블로그 업데이트 확인
블로그의 내용이 업데이트 되는 것은 약간 (최대 수분) 걸린다. 최근 푸시된 내용이 반영되었는지 확인한다. 

>http://자기아이디.github.io

## 추가
항상 git 클라이언트를 통해서만 블로그 내용을 업데이트 하는 것이 불편할 수 있다. 이때는 [prose](http://prose.io)에 접속해서 웹 기반으로 블로그 내용을 업데이트 해보자.


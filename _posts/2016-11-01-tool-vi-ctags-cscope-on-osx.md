---
layout: post
title: Tool - OSX에서 vi ctags cscope 사용한 개발 환경 구축
description: Tool - OSX에서 vi ctags cscope 사용한 개발 환경 구축
modified: 2016-11-01
tags: [osx, tool]
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

별도의 IDE를 구축하지 않고도 빠르게 코드를 확인하고 수정할 수 있는 vi 기반의 코딩 환경을 돕는 툴로 ctags와 cscope가 있다. 
ctags는 심볼을 찾아서 그 구현으로 이동하는 것을 위한 툴이고, cscope는 심볼, 패턴, 파일명을 검색하는 툴이다. 
각각 사용할 수 있지만, 함께 사용할 때 아주 강력한 힘을 발휘한다. 맥 OSX 환경에서 어떻게 설정하고 사용할 수 있는지 알아보자. 

### 1 설치

#### 1.1 brew 설치

가장 먼저 homebrew를 다음 명령으로 설치하자. 

```bash
$ ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)" < /dev/null 2> /dev/null
```

#### 1.2 ctags 설치

osx의 터미널에서 그냥 ctags를 실행시키면 명령어가 있기에 그냥 사용할 수 있을 것 같지만, 다음과 같은 에러를 만난다. 

```bash
$ ctags -R .
ctags: illegal option -- R
usage: ctags [-BFadtuwvx] [-f tagsfile] file ...
```

다음과 같이 제대로 동작하는 ctags를 설치해 보자. 

```bash
$ brew install ctags
```

다음 자신 계정의 루트 폴더에 .bashrc 파일이나 .bash_profile을 만들고, 거기에 다음과 같이 alias를 추가한다. 

```bash
alias ctags="`brew --prefix`/bin/ctags"
```

이제 저장 후 나와서 다음 명령으로 설정을 활성화 시킨다.(.bash_profile의 경우) 

```bash
$ source .bash_profile
```

#### 1.3 cscope 설치 

다음과 같이 제대로 동작하는 cscope를 설치한다.  

```bash
$ brew install cscope
```

### 2 설정

다음 자신 계정의 루트 폴더에 .vimrc 파일을 만들고 아래와 같은 내용을 추가하자. 

```bash
set autoindent
set number
set sm
set tabstop=4
set shiftwidth=4
set title
set hlsearch
colo desert
syntax on
set tags=./tags,tags,../tags,../../tags,../../../tags,../../../../tags,../../../../../tags,../../../../../../tags,../../../../../../../tags,../../../../../../../../tags
set csprg=/usr/bin/cscope
set csto=0
set cst
```

이제 저장 후 나와서 다음 명령으로 설정을 활성화 시킨다.

```bash
$ source .vimrc
```

### 3 프로젝트 만들기 

- 자바 프로젝트를 기준으로 설명한다. 프로젝트 폴더의 루트로 이동한다.

#### 3.1 하나씩 실행

- 루트 폴더에서 다음 명령을 수행한다. 

```bash
$ ctags -R .
```

- cscope data파일을 같은 폴더에서 만든다. 

```bash
$ find . *.java -print > cscope.files
```

- cscope를 실행한다. 

```bash
$ cscope -i cscope.files
```

#### 3.2 스크립트로 실행

위의 한 단계씩 진행할 수 도 있지만, script파일을 만들어서 바로 진행할 수 있다. 

- run.sh 라는 파일을 만들고 아래 내용을 붙여 넣는다. 

```
#!/bin/sh 
rm -rf cscope.files cscope.out
ctags -R .
find . *.java -print > cscope.files
cscope -i cscope.files
``` 

- 실행가능하게 만든다. 

```bash
$ chmod +x run.sh
```

이제 run.sh를 이용해 실행한다. 

```bash
$ ./run.sh
```

### 4 사용 방법 

##### 4.1 기본 

- Cscope가 실행되면 아래에 여러 줄의 Find... 메뉴가 보인다. 원하는 메뉴로 이동하기 위해서는 탭을 누르거나 화살표를 위아래로 움직이면 된다. 
- 원하는 메뉴에서 원하는 패턴을 입력하면 바로 실행된다. Java의 경우에 Find this C symbol 메뉴에서 Java 심볼을 입력하면 된다. 
- Cscope를 끝내는 것은 control + d이다. 
- 열린 코드 파일에서 control + ]를 누르면 심볼의 선언이나 구현으로 점프하고 control + t를 누르면 뒤로 돌아간다. 이것은 ctags명령이다. 

##### 4.2 vi 명령 모드에서 입력

코드 상에서 원하는 것을 찾고 싶을 텐데 그것은 다음 명령을 참고하자. 

```
:cs find {질의종류} {symbol_name}
ex) cs find s main
```

- 0 or s : symbol_name 중 검색 (Cntl-'\' + s)
- 1 or g : symbol_name의 정의를 검색 (Cntl-'\' + g)
- 2 or d : symbol_name에 해당하는 함수에서 호출된 함수를 검색 (Cntl-'\' + d)
- 3 or c : symbol_name에 해당하는 함수를 호출하는 함수를 검색 (Cntl-'\' + c)
- 4 or t : symbol_name에 해당하는 text문자열을 검색 (Cntl-'\' + t)
- 6 or e : 확장 정규식을 사용하여 symbol_name을 검색 (Cntl-'\' + e)
- 7 or f : 파일 이름중에서 symbol_name을 검색 (Cntl-'\' + f)
- 8 or i : symbol_name을 include하는 파일을 검색 (Cntl-'\' + i) 

### 5 Reference

- [ctags setup on mac](https://gist.github.com/nazgob/1570678)
- [cscope setup on mac](http://macappstore.org/cscope/)
- [리눅스에서 vim + ctags + cscope](http://blog.naver.com/leekdh1965/220549087542)
- [vim color scheme](http://alvinalexander.com/linux/vi-vim-editor-color-scheme-colorscheme)

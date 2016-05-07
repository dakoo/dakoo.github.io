---
layout: post
title: cpp - coding convention
description: cpp - coding convention
modified: 2016-05-07
tags: [blog]
comments: true
image:
  feature: abstract-11.png
---
cpp code convention에 대한 간단한 정리 

### Python code convention

[google cpp code convention](https://google.github.io/styleguide/cppguide.html)을 참조하자.

### Terminal에서의 Code convention check

[google cpplint](https://github.com/google/styleguide/tree/gh-pages/cpplint)를 사용하자. 

#### 설치

[github의 google coding sytle page](https://github.com/google/styleguide/tree/gh-pages/cpplint)에서 cpplint.py 파일을 다운로드 한다 .
그리고 실행 방식에 따라 cpp파일이 있는 폴더 내, 혹은 그 상위 폴더에 위치시킨다. 

#### 실행

다음과 같이 터미널에서 실행한다. 

> python cpplint.py 파일1 파일2 파일3 ..

또는 

> python cpplint.py 폴더/*.cpp

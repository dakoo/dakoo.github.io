---
layout: post
title: Java - vim 모드와 함께 쓸 만한 Intellij 단축키
description: Java - vim 모드와 함께 쓸 만한 Intellij 단축키
modified: 2016-08-17
tags: [java, vi, intellij]
comments: true
image:
  feature: abstract-11.png
---
Intellij는 아주 강력한 단축키를 제공하는데 나의 경우 vim 모드를 사용하기 때문에 많은 단축키의 경우 불필요하다. 
vim이 커버하지 못하는 부분을 채워주는 단축키를 알아보자. 

#### 코딩 

- alt + insert : 코드 삽입
- ctrl + p: 함수 인수 확인
- ctrl + q: 문서창
- ctrl + /: // 주석 토글
- ctrl + shft + /: /**/주석 토글

#### Navigation

- alt + left/right: 이전/이후 편집 포인트 또는 파일의 위치로 이동 
- Esc: 편집창으로 
- F12: 이전 tool window로 (탐색창, 디버깅창, 콘솔창 등)
- ctrl + shift + backspace : 마지막 편집 포인트로 이동
- ctrl + b: 선언 또는 사용된 위치로 상황에 따라 
- alt + up/down: class의 위/아래 메소드 이동
- ctrl + [/]: block의 시작/끝으로 이동

#### Search 및 선택

- ctrl + e: 최근 사용한 파일들
- ctrl + n : class 검색해서 열기
- ctrl + shift + alt + n: symbol 검색해서 열기 

#### Refactoring

- shift + f6: rename
- ctrl + alt + m: 메소드 추출

#### Compile 및 Run

- shift + f10 : run
- shift + f9 : debug
- ctrl + f8 : toggle breakpoint
- ctrl + sift + f8 : view breakpoints (별도 창에서 breakpoint 제어)
- f9: resume
- f8: step over (한 줄씩 진행)
- f7: step into (함수 안으로 진입하기)
- shift + f8: step out (함수 내에서 더 진행안하고 밖으로 나가기)


---
layout: post
title: Java - OSX에서 vim 모드와 함께 쓸 만한 Intellij 단축키
description: Java - OSX에서 vim 모드와 함께 쓸 만한 Intellij 단축키
modified: 2016-08-17
tags: [java, vi, intellij, osx]
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

Intellij는 아주 강력한 단축키를 제공하는데 나의 경우 vim 모드를 사용하고 있고, 또한 osx에서 사용하기에 일반적인 단축키 맵과는 조금 다르다. 
OSX에서 사용할 수 있는 vim이 커버하지 못하는 부분을 채워주는 단축키를 알아보자. 

### 창간 이동하기

- **코드 편집창으로 포커스 옮기기**: esc (Caplock에 esc를 맵핑하고 있다면 가볍게 Caplock을 누르자)
- 열려 있는 파일이나 다른 툴 윈도우로 이동: control + tab
- 코드 편집창으로 세로로 분할해 놓은 경우, 포커스 이동하기: option + tab

### 찾아서 열거나 참조하기, 코드 내 이동

- **모든 것 찾기**: Shift, Shift 
- **빠른 Javadoc 문서 참조**: control + j (Caplock에 esc를 맵핑하고 있다면 가볍게 Caplock을 누르자)
- **현재 호출하고 있는 메소드의 파라미터 확인하기**: command + p
- **현재 호출하고 있는 메소드의 반환 타입 확인하기**: shift + control + p 
- **문자열 검색하기**: shift + command + F
- 클래스 찾기(단어사이 대문자로 조회): command + o 
- **최근 사용 파일 찾아 열기**: command + e (control+tab과 동일해 보이지만 살짝 다르다)
- 현재 클래스의 필드, 메서드 등을 보여주는 창이 떠서 선택하여 이동: command + F12 
- **이전 위치**: option + command + <-
- **다음 위치** : option + command + ->
- **선언으로 이동**: command + b
- 구현으로 이동: option + command + b

### 코드 작성


#### 자동 완성 

- **기본 자동 완성**: control + space
- 스마트 자동 완성: control + shift + space
- **오류 해결 솔루션 제시**: option + enter

#### 편리한 코드 삽입

- **코드 생성**: command + n
- **선택된 코드를 위, 아래로 이동**. 선택을 안 했으면 한 줄이 default: command + shift + up/down: 
- **자동 import 관리**: control + option + o: 
- 주석 토글: command + /
- 블록 주석 토글: shift + command + /

#### 리팩토링 

- **Rename**: Shift + f6
- 메소드 추출: option + command + m
- 변수를 필드(객체변수)로 분리: option + command + f

#### 테스트 

- **클래스의 테스트 케이스 생성**: shift + command + t
- 테스트 메소드 생성: 테스트 케이스에서 command + n

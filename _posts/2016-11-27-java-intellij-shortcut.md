---
layout: post
title: Java - OSX에서 vim 모드와 함께 쓸 만한 Intellij 단축키
description: Java - OSX에서 vim 모드와 함께 쓸 만한 Intellij 단축키
modified: 2016-11-27
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

# 중요 알림

> 아래 글을 지극히 개인적인 선호에 따라 설정한 IntelliJ 키맵입니다. 개인적으로 정리하기 위한 글이오니 **되도록 참고하지 말아주세요**.

# 시작

IntelliJ IDEA를 본격적으로 사용하기 위해 vi와 eclipse 단축키에 익숙한 나 자신만을 위해 IntelliJ 키맵 설정을 해본다. 되도록 나의 eclipse keymap과 유사하고 vi mode에 최적화된 keymap을 설정하고자 한다. 

## 기본 설정

IntelliJ IDEA > Preferences > Keymap으로 가서 Keymaps를 Mac OS X 10.5+로 한 후 copy를 하여 새로운 이름의 keymap으로 정한다. 
참고로 Preferences 단축키는 **command + ,**이다. 

### 키보드 설정 추가

이제 키보드 설정을 추가한다. 중간에 다른 키보드 설정을 제거한다는 경고가 뜨면 이전 설정을 지우는 것으로 하고 그냥 진행한다. 

- Split Vertically: option + v
- Expression Type: command + shift + p
- Class in Navigate: command + shift + t
- File structure : control + m
- Back : control + h
- Forward: control + l
- Declaration: control + b
- Implementation: control + p
- Show Intention Actions: control + ,
- Optimize Imports: command + shift + o
- Rename: command + shift + r
- Method in Extract: command + shift + m
- Find Usages: control + i
- Call Hierarchy: command + shift + i
- Reformat Code: command + shift + f
- Inspect Code: control + u

Preferences에서 Other Settigs > Vim Emulation으로 이동한다. 모두 IDE로 바꾼다. 

## 자주 사용하는 단축키

위의 변경내용이 반영된 자주 사용하는 단축키는 다음과 같다. 

### 이동 및 선택 관련 

- 코드 편집창으로 포커스 옮기기: esc (Caplock에 esc를 맵핑하고 있다면 Caplock)
- 이전 파일과 두 파일간 switch: control + tab 
- 최신 파일 중 선택: command + e (이동은 command + e를 연속으로 누르거나, command 누르지 않고 방향키 누르기. 이후 엔터로 선택)
- 코드 창을 세로로 분할: option + v
- 코드 편집창으로 세로로 분할해 놓은 경우, 포커스 이동하기: option + tab
- 현재 클래스의 필드, 메서드 등을 보여주는 창이 떠서 선택하여 이동: control + m
- 이전 위치: control + h
- 다음 위치: control + l
- 선언으로 이동: control + b 
- 구현으로 이동: control + p
- 현재 메소드의 수퍼 메소드로 이동: command + u
- 다음 method : control + down
- 이전 metod : control + up

### 검색 및 참조

- 모든 것 찾기: Shift, Shift
- 빠른 Javadoc 문서 참조: control + .
- 현재 호출하고 있는 메소드의 파라미터 확인하기: command + p
- 현재 호출하고 있는 메소드의 반환 타입 확인하기: command + shift + p
- 문자열 검색하기: shift + command + F
- 클래스 찾기(단어사이 대문자로 조회): command + shift + T
- Find Usage: control + i
- Call Hierarchy: control + shift + i

#### 코드 수정

- 기본 자동 완성: control + space
- 스마트 자동 완성: control + shift + space
- 오류 해결 솔루션 제시: control + ,
- 코드 생성: command + n 
- 선택된 코드를 위, 아래로 이동. 선택을 안 했으면 한 줄이 default: command + shift + up/down:
- 자동 import 관리: command + shift + o
- 주석 토글: command + /   
- 블록 주석 토글: shift + command + /
- Rename: command + shift + r
- 메소드 추출: command + shift + m   
- auto-formatting: command + shift + f
- Inspect code: control + u

## 관련 수정

### 폰트 

Preferences > Editor > Colors & Fonts > Font

- 폰트는 menlo를 유지하되 사이즈를 11로 한다. 

### 색깔

기본 설정의 경우 주석 색깔이 너무 튄다. 주석 색깔을 녹색이지만 더 흐리게 한다. 

- Preferences > Editor > Colors & Fonts > Java에서 Comments > Javadoc으로 가서 Block comment, Line commment, Markup, Tag, Tagvalue, Text의 색깔을 모두 Forground를 41470B로 바꾼다. 이때 바꾸기위해서는 Use Inherited attributes를 해제한다. 

---
layout: post
title: Web - OSX에서 WebStorm과 Dash 연동
description: OSX에서 WebStorm과 Dash 연동
modified: 2015-08-18
tags: [web, webstorm, osx]
comments: true
image:
  feature: abstract-4.png
---

OSX에서 Web 개발을 하기위해서는 최고의 Web 개발용 IDE인 WebStorm과 API 검색의 최강툴인 Dash를 적절히 사용하는 것이 필요하다. 
다음과 같은 순서로 설정을 하면 WebStorm에서 코딩하면서 빠르게 API문서를 참조할 수 있다. 

### 연동을 위한 설치와 설정 순서

1. WebStorm 라이선스를 구매한 후 [키를 획득](https://account.jetbrains.com/)하고 [프로그램을 다운로드](https://www.jetbrains.com/webstorm/download/)
하여 설치한다.
2. Dash 2를 앱스토어에서 다운로드 받아 설치한다. 무료이다. 설치 후 필요한 언어의 API document set을 다운받는다. 
3. Dash는 환경설정에서 시스템 시작시 실행되도록 설정한다. 
4. WebStorm 실행하여 Preference > Plug-ins에서 ‘Dash' Plugin이 설치되어 있는지 확인한다. 만약 설치되어 있지 않다면 그 창에서 검색하여 설치한다. 
5. WebStorm의 Preference > Keymap > Plug-ins > Dash > Command+Shfit+d 인지 확인한다. 

이제 코딩창에서 키워드를 선택하고 단축키 **Command + Shift + d**를 누르면 dash가 실행되어 원하는 키워드에 대한 검색 결과를 보여준다.    

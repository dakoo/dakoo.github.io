---
layout: post
title: 개발환경 - Ubuntu 14.04 Caps Lock키 변경
description: 개발환경
modified: 2016-02-28
tags: [tool]
comments: true
image:
  feature: algorithm.jpeg
---
Caps Lock 키는 소프트웨어 개발자에게 별로 유효한 키가 아니다. 그보다는Escape키가 더 중요하다. 특히 vi 모드를 주로 이용하는 개발자는 더욱 그러하다. 손가락 위치를 많이 움직이지 않고 새끼 손가락 만으로 누를 수 있으므로 익숙해지면 입력 속도가 비약적으로 상승한다. 

### Ubuntu 14.04 키보드 및 단축키 설정

vi모드를 주로 사용하는 개발자들은 생산성을 높이기 위해 키보드의 Caps Lock 키가 다음과 같이 동작하도록 하자. 

- Escape로 동작

#### Caps Lock을 Escape 키로 변경

다음과 같이 gnome-tweak-tool을 설치해서 Caps Lock의 설정을 변경한다. 

- sudo apt-get install gnome-tweak-tool
- DASH에서 `tweak-tool`을 찾아서 실행한다. 
- Typing(입력) 메뉴에서 Caps Lock 키 동작을 `Make Caps Lock an additionnal Escape key'으로 설정한다. 


#### 테스트

이제 터미널에서 `vi`를 실행하여 vi 모드로 입력을 해보자. Escape 대신 Caps Lock키를 이용하면 얼마나 편한지 느껴보도록 하자. 

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
Caps Lock 키는 소프트웨어 개발자에게 별로 유효한 키가 아니다. 그보다는 Control키나 Escape키가 더 중요하다. 특히 vi 모드를 주로 이용하는 개발자는 더욱 그러하다. 그런데, Control 키는 다른 키와 함께 눌러서 사용하고, Escape키는 그보다는 그 키 하나만 눌렀다가 떼는 형태로 사용한다. 사용하는 패턴이 다르므로 잘 사용하지 않는 Caps Lock키에 두 키를 모두 맵핑해서 사용해 보도록 하자. 손가락 위치를 많이 움직이지 않고 새끼 손가락 만으로 누를 수 있으므로 익숙해지면 입력 속도가 비약적으로 상승한다. 

### Ubuntu 14.04 키보드 및 단축키 설정

vi모드를 주로 사용하는 개발자들은 생산성을 높이기 위해 키보드의 Caps Lock 키가 다음과 같이 동작하도록 하자. 

- 눌렀을 때 Control키로 동작
- 눌렀다가 뗄때는 Escape로 동작

#### Caps Lock을 Control 키로 변경

다음과 같이 gnome-tweak-tool을 설치해서 Caps Lock의 설정을 변경한다. 

- sudo apt-get install gnome-tweak-tool
- DASH에서 `tweak-tool`을 찾아서 실행한다. 
- Typing(입력) 메뉴에서 Caps Lock 키 동작을 `Make Caps Lock an additionnal Ctrl key'으로 설정한다. 

#### Control 키를 눌렀다 뗄때 Escape 키로 동작하도록 

다음과 같이 xcape를 설치하고 설정한다. 

- xcape를 build하기위해 필요한 package를 `sudo apt-get install git gcc make pkg-config libx11-dev libxtst-dev libxi-dev`명령을 사용하여 설치한다. 
- `https://github.com/alols/xcape.git`에서 xcape를 다운받아 압축을 푼다. 
- 압축을 푼 폴더로 이동하여 `make` 명령을 수행하여 xcape를 build한다. 
- `sudo make install`을 이용해 설치를 한다. 
- 자신 계정의 루트 폴더의 `.bashrc`파일에 (없다면 만들자) 아래 내용을 추가하고 `. .bashrc` 명령으로 설정을 반영한다. 

```
xcape -e 'Control_L=Escape'
```

#### 테스트

이제 터미널에서 `vi`를 실행하여 vi 모드로 입력을 해보자. Escape와 Control키를 누르는 대신 Caps Lock키를 이용하면 얼마나 편한지 느껴보도록 하자. 

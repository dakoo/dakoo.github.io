---
layout: post
title: Ubuntu - Caps Lock키를 Control키/Esc키로 변경
description: 개발환경
modified: 2016-02-28
tags: [ubuntu]
comments: true
image:
  feature: algorithm.jpeg
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->


Caps Lock 키는 소프트웨어 개발자에게 별로 유효한 키가 아니다. 그보다는 Control키와 Escape키가 더 중요하다. 특히 vi 모드를 주로 이용하는 개발자는 더욱 그러하다. 손가락 위치를 많이 움직이지 않고 새끼 손가락 만으로 누를 수 있으므로 익숙해지면 입력 속도가 비약적으로 상승한다. 

xcape를 이용한 설정은 가끔 풀릴때가 있다. 그때마다 다시 실행하는 건 매우 매우 번거로운 일이다!!
그러므로 **gnome-tweak-tool툴을 사용하여 esc로만 전환하고, control key는 기존 것을 쓰는 것을 추천**한다. 

### Ubuntu 키보드 및 단축키 설정

#### 1. (비추) Caps Lock을 Control과 Esc키를 동시에 지원하도록 변경 

vi모드를 주로 사용하는 개발자들은 생산성을 높이기 위해 키보드의 Caps Lock 키가 다음과 같이 동작하도록 하자. 

1. 누르면 Control 키로 동작 
2. 떼면 Escape키로 동작

다음과 같이 설정하자. 

- xcape를 설치하자. 
 
```bash
$ sudo apt-get install git gcc make pkg-config libx11-dev libxtst-dev libxi-dev
$ git clone https://github.com/alols/xcape.git
$ cd xcape
$ make
$ sudo make install
```

- 자신의 계정의 root 폴더로 이동해서 .profile 파일 또는 .bashrc를 열고 다음을 추가하자. 

```bash
setxkbmap -option 'caps:ctrl_modifier'
xcape -e 'Caps_Lock=Escape;Control_L=Escape'
```

##### Update

위와같이 설정한 keymap이 가끔 풀릴때가 있다. 그때마다 Script를 다시 실행하는 건 매우 매우 번거로운 일이다!!
그러므로 gnome-tweak-tool툴을 사용하여 esc로만 전환하고, control key는 기존 것을 쓰는 것을 추천한다. 

#### 2. Caps Lock을 Escape 키로 변경

vi를 사용하기 위해서는 Escape 키로만 동작하도록 해도 충분하다. 이를 위해서는 두 가징 방법이 있다. 

1. xcape을 사용하여 변경하는 방법
2. gnome-tweak-tool을 사용해서 변경하는 방법 

##### (비추) 2.1 xcape을 사용하는 방법

- 위와 같이 xcape를 설치한다. 
- 자신의 계정의 root 폴더로 이동해서 .profile 파일을 열고 다음을 추가하자. 

```bash
setxkbmap -option 'caps:ctrl_modifier'
xcape -e 'Caps_Lock=Escape'
```

##### (추천) 2.2 gnome-tweak-tool을 사용해서 변경하는 방법 

- gnome-tweak-tool을 설치해서 Caps Lock의 설정을 변경한다. 

```bash
$ sudo apt-get install gnome-tweak-tool
```

- DASH에서 `tweak-tool`을 찾아서 실행한다. 
- Typing(입력) 메뉴에서 Caps Lock 키 동작을 `Make Caps Lock an additionnal Escape key'으로 설정한다. 


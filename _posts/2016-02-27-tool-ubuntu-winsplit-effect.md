---
layout: post
title: Ubuntu - Ubuntu 14.04에서 Winsplit 효과 내기
description: 개발환경
modified: 2016-02-27
tags: [ubuntu]
comments: true
image:
  feature: algorithm.jpeg
---
Winsplit은 화살표 키와 Control, Alt, Windows, Shift 키의 조합으로 활성화된 어플리케이션 창을 다른 모니터로 이동시키거나 같은 모니터내에서 미리 설정된 포지션으로 이동시키는데 탁월한 Windows용 툴이다. 현재는 지원이 더이상 없는 대신 무료 소프트웨어로 바뀌었기 때문에 무료로 사용이 가능한다. 이와 유사한 기능을 Ubuntu에서 사용해 보자. 

### CompizConfig

CompizConfig는 Graphic 카드와 결합하여 다양한 효과를 제공하는 Linux 유틸리티이다. 너무 많은 기능이 있기에 모두 소개할 수 없고, 그 중 Winsplit와 유사한 효과를 내기 위한 부분만 알아보자

#### 설치 

다음과 같이CompizConfig을 설치한다. 

- `sudo apt-get install compizconfig-settings-manager`

##### Trouble Shooting

If you see the following messages... use Ubuntu Software Center instead of terminal.

```
Reading package lists... Done
Building dependency tree       
Reading state information... Done
Package compizconfig-settings-manager is not available, but is referred to by another package.
This may mean that the package is missing, has been obsoleted, or
is only available from another source
However the following packages replace it:
  compiz-core

E: Package 'compizconfig-settings-manager' has no installation candidate
```

#### 설정

DASH에서 `compizconfig`을 찾아서 실행한 후 winsplit효과와 같이 설정하자. 여기서의 키 설정은 나에게 익숙한 설정을 따르는 것이므로 개인별로 다를 수 있다. ubuntu는 workspace를 전환할 수 있고 default로 control+alt+화살표키가 workspace전환에 할당되어 있는데 통상 Winsplit에서는 이를 모니터간 전환에 할당한다. 그러므로 이를 어떻게 할지 고민이 필요한데 여기서는 이를 다루지 않는다.  
 
##### 창 최대화 및 최소화

- General > General Option> Keybindgs로 이동한다. 
- maximize를 control + shift + up로 설정한다. 
- unmaximize or minimize를 control + shift + down로 설정한다. 

#####  창을 왼쪽/오른쪽 화면에 고정

- WIndow Management > Grid > Binding로 이동한다. 
- put left를 ctrol + shift + left로 설정한다. 
- put right를 ctrol + shift + right로 설정한다. 

##### 불필요한 설정 제거

- WIndow Management > Grid > Corners/Edges로 이동하여 모든 것을 none으로 설정한다. 


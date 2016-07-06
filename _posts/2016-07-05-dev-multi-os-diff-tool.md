---
layout: post
title: 개발 환경 - diffmerge와 diffuse (멀티 OS diff 툴)
description: 개발 환경 - diffmerge와 diffuse (멀티 OS diff 툴)
modified: 2016-07-05
tags: [dev]
comments: true
image:
  feature: abstract-11.png
---
Windows는 다양한 diff 툴이 있다. 무료인 Win Merge, 유료인 Araxis Merge 등은 직관적이고 사용이 편리하다. 
Ubuntu와 OSX에서는 상대적으로 빈곤한 diff 툴을 제공하는데, 이번에 소개하는 Diffmerge와 Diffuse는 무료 툴이면서도 쓰기 편리하다. OSX는 Diffmerge를 사용하자. 

## Diffmerge

[diffmerge](http://www.sourcegear.com/diffmerge/downloads.php)는 직관적이고 Windows, OSX, Ubuntu, Fedora 모두 설치 파일을 제공하는 멀티 OS diff 툴이다. 

## Diffuse

[diffuse](http://diffuse.sourceforge.net/download.html)는 쓰기 편리하고 2개의 화면만이 아니라 3개의 화면을 이용해서 동시 Diff/Merge를 지원한다. Windows와 Ubuntu는 Diffuse 사이트에서 설치 파일을 다운받아서 설치하면 되므로 따로 설명할 필요가 없다.  

### OSX - 설치하지 말것 

Diffuse는 OSX는 별도로 설치파일을 제공하지 않다. [Diffuse 사이트](http://diffuse.sourceforge.net/download.html)에서는 OSX 설치방법을 가이드 하고 있으나, 절대로 하면 안된다. 
의존성에 따라 너무나 많은 package들을 설치하기 때문이다. macport이던 brew이던 동일하게 너무 많은 것을 설치한다. 

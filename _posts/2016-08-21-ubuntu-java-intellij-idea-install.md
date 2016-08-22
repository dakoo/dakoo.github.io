---
layout: post
title: Ubuntu Java - Intellij IDEA 설치하기 
description: Ubuntu Java - Intellij IDEA 설치하기 
modified: 2016-8-21
tags: [ubuntu, java]
comments: true
image:
  feature: abstract-11.png
---
Intellj IDEA를 Ubuntu에 설치하는 법을 알아보자. 

먼저 Java가 설치되어 있어야 한다. 이를 위해 [이전 글](http://hochulshin.com/oracle-java8-installation-ubuntu-1604/)을 참조하자.

### 설치 

1. [jetbrain 다운로드 사이트](https://www.jetbrains.com/idea/)에서 Ubuntu용을 선택해 다운로드 
2. 다운로드 받은 폴더에서 압축을 해제한다. 필요하면 적절한 폴더로 이동
3. 압축을 푼 폴더의 **bin**폴더의 **idea.sh**를 실행
4. Set UI theme page에서 적절한 theme을 설정후 **Next: Destkop Entry**
5. Create a desktop... 의 checkbox를 선택하고, For all users의 checkbox도 선택 
6. **Next:Launcher Script**
7. Create a script for opening files..의 checkbox를 선택 후 **Next**를 계속 선택
8. Download featured plugins page에서 IdeaVim의 Install and Enable 버튼 선택하여 설치
9. **Start using IntelliJ IDEA**

이제 terminal이 아닌 DASH를 통해서 intellj를 실행시킬 수 있다. 

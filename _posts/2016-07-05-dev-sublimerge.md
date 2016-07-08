---
layout: post
title: 개발 환경 - sublimerge
description: 개발 환경 - sublimerge
modified: 2016-07-05
tags: [dev]
comments: true
image:
  feature: abstract-11.png
---
Windows는 다양한 diff 툴이 있다. 무료인 Win Merge, 유료인 Araxis Merge 등은 직관적이고 사용이 편리하다. 
Ubuntu와 OSX에서는 상대적으로 빈곤한 diff 툴을 제공하는데, 이번에 소개하는 sublimerge는 유료이지만, 가끔 질문을 하는 sublime스러운 라이선스 정책을 가지고 있는 sublime기반 merge툴이다. 

## sublimerge

### 설치 

1. control(OSX는 command) + shift + p을 눌러서 package control을 실행시킨다. 
2. Install package를 선택한다. 
3. sublimerge Pro를 검색하여 설치한다. 

### 파일 또는 directory 비교 

- sidebar에서 두개의 파일 또는 폴더를 마우스로 선택한다. 선택을 위해서는 **control + left click**을 한다. 
- 오른쪽 마우스 키를 눌러서 뜬 팝업 메뉴에서 Sublimerge를 실행시킨다. 

#### 단축키

- 'enter': directory 비굥의 경우 folder 선택해서 안으로 이동
- 'down' : 다음 비교할 곳으로 이동
- 'up' : 이전 비교할 곳으로 이동 (directory 비교의 경우엔 맨 위 파일에서 한 level위의 folder로 이동으로 동작) 
- 'right' : 오른쪽 코드를 왼쪽에 덮어쓰기
- shift + 'right': 왼쪽 코드를 오른쪽에 모두 덮어쓰기
- 'left' : 왼쪽 코드를 오른쪽에 덮어쓰기
- shift + 'left' : 오른쪽 코드를 왼쪽에 모두 덮어쓰기
- control + shift + w: 윈도우 닫기 (이것은 sublime text의 단축키)

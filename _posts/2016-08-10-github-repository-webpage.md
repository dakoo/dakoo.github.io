---
layout: post
title: Github - Repository를 이용해서 web page 만들기 
description: Github - Repository를 이용해서 web page 만들기
modified: 2016-08-10
tags: [github, git, web]
comments: true
image:
  feature: abstract-19.png
---
Github를 이용해서 hochulshin.com을 호스팅하고 있는데, 다른 용도의 page를 계속 추가하고 싶은 경우 어떻게 할지를 설명하고자 한다. 

## hochulshin.com을 github에서 관리하기 

hochulshin.com 주소로 이동하려고 하면 dns에서 자동으로 github의 공식 호스팅 주소인 dakoo.github.io로 이동하도록 설정이 되어 있다.  

```
hochulshin.com 검색 --------------> 자동으로 dakoo.github.io의 주소 획득 -----------> dakoo.github.io로 접근 
```

이를 위해서는 dakoo.github.io repository root에 CNAME이라는 파일을 만들어서 `hochulshin.com`이라고 저장해 두고, hochulshin.com을 판매한 Domain업체에 dakoo.github.io를 등록하면 된다. 

## hochulshin.com/{새로운 프로젝트 페이지} 추가하기

github에 repository를 생성하고 web으로 publish하면 webpage가 생성된다. 그러면 hochulshin.com/{새로운 프로젝트 페이지} 주소로 접근할 수 있다. 
이제 내가 만든 englishvoca라는 프로젝트를 대상으로 설명해 보자. 

1. github상단에서 **+**를 선택 후 New repository를 선택
2. Ceate a new repository에서 이름은 englishvoca로 한 후 모든 것을 default로 하고 **Create repository** 
3. README를 선택해서 Edit 창에 my daily study라고 입력하고 **Commit new file** 선택
4. **Settings**에서 **GitHub Pages**으로 이동
5. **Launch automatic page generator** 선택
6. 글짜가 많이 써 있는 첫번째 창을 모두 지우고 **Continue to layout** 선택
7. Layout 중 하나를 적당히 골라 선택 후 **Publish page**
8. 'Branch:master'라고 표시된 버튼을 클릭해 드롭다운 메뉴 중 Braches를 'gh-pages'로 선택
9. 이제 여기서 간단히 web page를 구성할 수 있다. 

이제 hochulshin.com/englishvoca 라는 주소로 접근 가능하다. 

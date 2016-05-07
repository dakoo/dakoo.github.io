---
layout: post
title: java - coding convention
description: java - coding convention
modified: 2016-05-07
tags: [blog]
comments: true
image:
  feature: abstract-11.png
---
Java code convention에 대한 간단한 정리 

### Java code convention

[Java 코딩 규칙](http://kwangshin.pe.kr/blog/java-code-conventions-%EC%9E%90%EB%B0%94-%EC%BD%94%EB%94%A9-%EA%B7%9C%EC%B9%99/)을 참조하자.

### eclipse에서의 code convention check

eclipse에서 Java code convention을 check하기 위해서는 [pmd](http://pmd.sourceforge.net/pmd-5.0.4/integrations.html#eclipse)을 사용하자. 

#### 설치

다음 순서를 따라 설치하자. 

1. eclipse에서 "Help"->"Install New Software"를 선택한다.  
2. 이름 필드에 "PMD", URL 필드에 "http://sourceforge.net/projects/pmd/files/pmd-eclipse/update-site/"를 입력하고 진행한다. 
3. eclipse version에 따라 적절한 PMD를 선택한다. (최신은 4)

#### 실행

package explorer에서 project에서 오른쪽 마우스 버튼을 눌러 "PMD > Check Code"를 실행한다. 

---
layout: post
title: Java - intellij의 live template 기능 사용하기 
description:  Java - intellij의 live template 기능 사용하기 
modified: 2016-08-17
tags: [java, intellij]
comments: true
image:
  feature: abstract-14.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

intellij는 코딩 생산성 향상을 위해 다양한 live template을 제공하고 있다. 이 중 유용한 것들을 알아보고 자신의 live template을 등록하는 방법도 살펴보자. 

### 1. 미리 정의된 live templates과 단축키

live template은 `control + j`로 창을 열어 선택할 수 있고, 또한 shortcut을 이용할 수 도 있다. 

#### 1.1 iteration

- fori : for loop
- itar: Array
- itve: Vector
- itli: List
- itco: Collection
- iten: Enumerator
- itit: Iterator
- ittok: String의 token

#### 1.2 String

- sfmt: string format
- sout: println

#### 1.3 기타 

- psvm: public static void main
- ifn: if null
- inn: if not null

### 2. live template 등록하기 

변수는 $$안에 추가해야 한다는 것을 명심하자. 

1. Settings 또는 Preferences 선택
2. Editor > Live Templates
3. (Optional) +표시 > Template Group 선택 > myTemplate입력
4. Group을 선택하고 +표시 > Live template
5. Abbreviation 입력
6. Description 입력
7. Template text 작성: 변수는 $$안에 넣자!
8. "No applicable contexts yet". Define이 있으면 Define을 선택 후 적당한 context 선택
9. 'Edit Variable'
10. Expression은 변수를 가져올 수 있는 것을 선택하는데 잘 모르겠으면 **completeSmart()**를 선택
11. Default value 입력
12. OK 

예를 들어 Template text:  

```
Abbreviation: sc
Description: System.in scanner
Template text: Scanner $SCANNERNAME$ = new Scanner(System.in);
```

Edit Variable:

```
Name: SCANNERNAME
Expressoin: completeSmart()
Default value: scan
Skip if defined: uncheck
```


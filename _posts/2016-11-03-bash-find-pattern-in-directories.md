---
layout: post
title: Bash - 폴더에서 파일 내용 검색하기
description: Bash - 폴더에서 파일 내용 검색하기
modified: 2016-11-03
tags: [ubuntu, osx, tool, bash]
comments: true
image:
  feature: abstract-11.png
---

<section id="table-of-contents" class="toc">
<div id="drawer" markdown="1">
*  Auto generated table of contents
{:toc}
</div>
</section><!-- /#table-of-contents -->

하위 폴더를 포함해서 파일 내용 검색해서 표시하는 것은 가끔 필요하기 하지만, 아주 절실할 때가 많다. 그 방법을 알아보자. 

## 1 테스트 환경

### 1.1 폴더 구조

간단히 다음과 같은 폴더 구조를 생각해보자. 

```
project - txt - AAA.txt
        - java - BBB.java 
```

project폴더 아래 txt, java 폴더가 각각 있고 각 폴더아래 aaa.txt 파일과 bbb.java파일이 있는 것이다. 

### 1.2 텍스트 파일 내용

AAA.txt파일의 내용은 다음과 같다. 

```txt
This file is a text file.
1. The first line is for class. 
2. The second line is for interface.
3. The third line is for dummies.
```


### 1.3 자바 파일 내용 


```java
public class BBB {
	void BBB() {
		//dummies
	}
}
```

## 2 명령어 살펴보기 

검색 시간을 줄이기 위해서는 파일 확장자를 제한하는 것이 필요하다. 쓸데없는 파일까지 뒤져서 출력하면 결과를 확인하기도 어려우니 말이다. 그것을 감안하고 다음 사용 방법을 살펴보자.

### 2.1 키워드에 일치 내용을 포함하는 파일 보여주기

```bash
find . -type f -name *.파일확장자 -print0 | xargs -0 grep -l 검색키워드
```

project 폴더에서 다음과 같은 사용 예를 보자. 

```bash
$ find . -type f -name *.java -print0 | xargs -0 grep -l dummies
./java/BBB.java
```

BBB라는 패턴과 가진 파일 중 java 확장자를 가진 BBB.java 파일 명을 보여준다. 


### 2.2 키워드에 일치 내용을 포함하는 파일 보여주기 - 확장자를 여럿으로 확장하기

확장자를 늘리기 위해서는 'f \( -name *.확장자1 -o -name *.확장자2 -o -name *.확장자3 \)' 이런 식의 약간 복잡한 패턴을 사용해야 한다. 

```bash
find . -type f \( -name *.확장자1 -o -name *.확장자2 -o -name *.확장자3 \) -print0 | xargs -0 grep -l 검색키워드
```

project 폴더에서 다음과 같은 사용 예를 보자. 

```bash
$ find . -type  f \( -name *.java -o -name *.txt \) - -print0 | xargs -0 grep -l dummies
./java/BBB.java
./txt/AAA.txt
```

이제 원하는 패턴을 가진 모든 파일을 보여준다. 


### 2.3 키워드 확장하기 

키워드가 공백을 포함한 문자열인 경우는 어떨까? 간단히 검색 키워드의 시작과 끝은 ''로 감싸면 된다. 예를 들어 dummies를 찾는데 //dummies를 빼고 싶다면 앞에 여백이 하나 있는 것을 찾으면 된다. 

```bash
find . -type f \( -name *.확장자1 -o -name *.확장자2 -o -name *.확장자3 \) -print0 | xargs -0 grep -l '검색 키워드'
```

project 폴더에서 다음과 같은 사용 예를 보자. 

```bash
$ find . -type  f \( -name *.java -o -name *.txt \) - -print0 | xargs -0 grep -l ' dummies'
./txt/AAA.txt
```

//dummies는 검색에서 제외한다. 

### 2.4 키워드에 일치하는 파일 내용 보여주기

일치하는 내용을 가진 줄을 결과로 보고 싶다면 어떻게 할까? 'grep -l' 명령대신 'grep -H'를 넣으면 되고, 색깔을 이용해 패턴을 강조하고 싶다면 '--color' 플래그를 추가한다. 

```bash
find . -type f \( -name *.확장자1 -o -name *.확장자2 -o -name *.확장자3 \) -print0 | xargs -0 grep -H --color '검색 키워드'
```

project 폴더에서 다음과 같은 사용 예를 보자. 

```bash
$ find . -type f \( -name *.java -o -name *.txt \) -print0 | xargs -0 grep -H --color 'BBB'
./java/BBB.java:public class BBB {
./java/BBB.java:	void BBB() {
```

파일 내용 만이 아니라 그 내용까지 알아 볼 수 있다!


## 3 정리 

최종적으로 다음과 같은 명령을 사용하면 된다는 것을 알 수 있을 것이다. 

```bash
find . -type f \( -name *.확장자1 -o -name *.확장자2 -o -name *.확장자3 \) -print0 | xargs -0 grep -H --color '검색 키워드'
```

이것을 .bashrc 등에 등록해 놓고 간단하게 사용하는 방법도 있을 것이다. 


